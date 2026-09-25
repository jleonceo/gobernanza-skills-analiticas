# Cinco comprobaciones para que una skill de IA siga acertando con datos reales

[Español](#español) · [English](#english)

## Español

El 23 de mayo de 2026 se lanzó una actualización masiva sobre la base contable de mi proyecto. Subía las ventas un 30 % sin tocar sus contrapartidas. De golpe, 2.449 de los 7.159 asientos quedaron descuadrados. Con los agentes de IA pasa lo mismo a otra escala: una skill que ayer acertaba puede equivocarse hoy sin dar ningún error. Estas cinco comprobaciones sirven para enterarte a tiempo. Las he escrito para quien construye skills o agentes que trabajan sobre datos reales, sea analista, desarrollador o responsable técnico. Salen de una contabilidad ficticia que llevo como controller financiero: TechAcces S.L., con datos sintéticos en MySQL, Power BI encima y agentes de Claude que proponen asientos. Las cifras de los casos salen del historial privado de ese proyecto, así que desde este repositorio no se pueden rehacer. Lo que sí se mide en abierto está en los repositorios enlazados al final.

### Qué es una skill

Una skill es un fichero de texto en Markdown que un agente de IA lee antes de trabajar. Le da el criterio del dominio, las reglas que debe respetar y los casos en que tiene que parar y preguntar. El agente la interpreta como cualquier otra instrucción. Por eso una skill que funcionaba puede fallar en silencio cuando cambia el modelo que la lee o cambian los datos. Si quieres la explicación desde cero, con ejemplos, la tienes en [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable).

### Las cinco comprobaciones

Van en el orden en que se las aplico a una skill, desde que la escribo hasta que la dejo trabajar sola.

#### 1. Medir antes de confiar

Una medida DAX de mi panel de Power BI daba +2.571.241,27 €. El resultado correcto, ventas menos gastos, era +322.957,19 €. La medida sumaba solo el debe del grupo 6 e ignoraba el haber, que es donde van las devoluciones y los descuentos. El panel cargaba. La cifra parecía razonable, así que a simple vista nada indicaba que estuviera mal.

Desde entonces cada medida nueva se cruza con una consulta SQL. Tiene que coincidir al céntimo antes de publicarla. Con las skills hago lo mismo por otra vía: antes de confiar en una, la examino contra una batería de casos cuya respuesta correcta fijo antes de lanzarla, lo que en evaluación de modelos se conoce como golden set. Es la misma batería que vuelvo a lanzar en cada una de las comprobaciones siguientes. La nota la pone un corrector automático con reglas escritas, de modo que dos personas que lo ejecuten obtienen la misma puntuación.

Para montarlo en tu proyecto necesitas tres piezas. La primera son unos casos parecidos al trabajo real. La segunda, la respuesta esperada de cada uno, escrita antes de ver lo que contesta la skill. La tercera es un script que compare las dos y devuelva la nota, que te permitirá repetir el examen cada vez que toques algo.

#### 2. La puerta de no-regresión

Cuando una skill falla de una forma nueva, actúo en ese primer fallo. Lo que controlo es cuándo acepto el arreglo. Para eso le pongo una puerta con dos condiciones:

1. El cambio arregla más casos de los que rompe.
2. Ningún caso que antes pasaba deja de pasar.

En el mismo movimiento en que tocas la skill relanzas todos los casos anteriores. Esperar a ver si algo se estropea más adelante solo retrasa el momento de descubrirlo. Lo aprendí con un arreglo pensado para un único caso que hundió todo el grupo de proveedores o clientes nuevos, porque 6 de sus 9 casos pasaron a pedir revisión sin ningún motivo en cuanto lo apliqué. Lo revertí. El problema también está medido fuera de mi proyecto: en el estudio SkillsBench, 16 de 84 skills empeoraron al cambiarlas.

Si ya tienes la batería de la comprobación anterior, la puerta es un script que la ejecuta antes y después del cambio y compara caso a caso. Guarda los dos resultados, porque son la evidencia que irá al registro de decisiones.

#### 3. El examen que no se amaña

El 30 de mayo de 2026 fijé un principio: la simulación se adapta al sistema. En la práctica significa que, cuando un examen falla, solo puedes tocar sus propias variables. Son la respuesta esperada si estaba mal fijada y el guion de la prueba. La skill, los datos y las reglas del negocio se quedan como estaban aunque el examen siga en rojo. Si los cambias para que pase, la nota deja de medir el trabajo real.

Tres días después llegó el primer amaño. Con un caso fallando, el agente que me ayudaba a mejorar una skill de análisis de datos escribió dos reglas nuevas mirando la cifra de referencia del examen, con la única intención de clavarla en la siguiente pasada. Una reclasificaba años desplazados y la otra era un `drop_duplicates`. El segundo cambio borró además 4.198 filas en lugar de 1.500. Se había llevado por delante duplicados legítimos. La skill resultante sacó 0 de 3. Lo vi enseguida y se revirtió todo. Aquel día entendí que la puerta de no-regresión no protege de esto. Un amaño puede arreglar el caso que falla sin romper ninguno de los demás. La única defensa es una pregunta ante cada cambio: ¿es criterio general o es la respuesta de este caso concreto? De la misma sesión salió algo aprovechable. El tratamiento del formato numérico europeo frente al americano depende de quién exportó el fichero. Se reescribió como un criterio general de cinco pasos que vale para cualquier fichero de entrada.

El segundo amaño fue en sentido contrario y cuesta más verlo. Se cambió la respuesta esperada de un caso con el argumento de que era la convención de cuentas. Al consultar la base de datos, esa cuenta no existía. Se había adaptado el examen al error del sistema. Ese cambio también se revirtió, igual que el primero.

En tu proyecto, cada cambio en una respuesta esperada tiene que llevar su justificación contra la fuente de verdad. Esa fuente puede ser la base de datos o la norma. Cada regla nueva en una skill tiene que poder explicarse sin nombrar el caso que la motivó. Si para justificarla necesitas citar la cifra del examen, lo que tienes es la respuesta de ese caso con forma de criterio. Quítala antes de volver a lanzar la batería.

#### 4. El verificador independiente

Quien produce un resultado no lo da por bueno. Cualquier hallazgo o propuesta de un agente pasa por otro agente que lo revisa desde cero antes de que yo actúe. Estos son los casos que me convencieron:

- El 29 de mayo de 2026 tres agentes auditores encontraron 10 hallazgos. El verificador confirmó 8, dio 1 por parcialmente correcto y marcó 1 como falso. Sin él habría corregido algo que estaba bien.
- En una evaluación del sistema contable, el agente validador frenó dos asientos correctos. Alegaba que sus cuentas no existían. Al comprobarlo en la base de datos existían, así que también el veredicto de quien revisa se contrasta con el dato antes de hacerle caso.
- El 5 de junio de 2026 un verificador escéptico tumbó una tesis que iba a usar como diferencial de un producto. Decía que ninguna otra plataforma tenía algo como CLAUDE.md. ChatGPT y Gemini lo tienen, Cursor también, y además existe el estándar AGENTS.md.

Lo que más cambia el resultado es lo que le das al verificador. Lo medí el 31 de julio de 2026 sobre un mismo fichero. Un verificador que recibió la lista de hallazgos dio 0 falsos, mientras que otro que solo recibió el artefacto y la tarea cazó 1 falso y 8 defectos que la lista no nombraba en ninguna parte. En la práctica, encarga la revisión a un agente distinto del que produjo el resultado. Entrégale el artefacto y lo que tiene que comprobar. **No le pases nunca la lista de hallazgos**: con ella delante encontró menos problemas.

#### 5. Autonomía según lo reversible

Esta última comprobación decide qué puede hacer el agente sin preguntarte. Cuanto más difícil de deshacer es una acción, más control humano lleva.

| Nivel | Qué entra | Qué hace el agente |
|---|---|---|
| Autónomo | Consultas, análisis, alertas y borradores en una tabla aparte de los datos reales | Lo hace sin preguntar |
| Autónomo con aviso | Propuestas de mejora y candidatos a casos de examen | Lo hace y te avisa |
| Confirmación obligatoria | Escribir en los datos reales y cambiar una skill en uso | Espera tu confirmación |
| Solo humano | Borrar, sin excepción | Lo haces tú |

El borrado lo garantizo en el propio recurso. El usuario de base de datos con el que conecta el agente no tiene permiso de DELETE. MySQL se lo niega con el error 1142, llegue la orden por donde llegue. Te recomiendo hacerlo igual en tu proyecto, con los permisos del usuario de base de datos. Así el candado sigue cerrado aunque el agente lea mal sus instrucciones.

### El registro de decisiones

Cada cambio que supera las comprobaciones queda anotado con cuatro campos:

- Qué cambió.
- Por qué cambió.
- Qué evidencia lo sostiene, con el resultado de la puerta de no-regresión.
- Qué se descartó.

El cuarto es el que casi nadie escribe. Es también el que más vale cuando, meses después, alguien vuelve a proponer una opción ya probada y rechazada. La plantilla está en [docs/plantilla_registro_decisiones.md](docs/plantilla_registro_decisiones.md). Trae un ejemplo real rellenado, el de una nómina con embargo judicial, con sus tres descartes: las cuentas 410, 475 y 52x. Cópiala a tu proyecto. Añade una entrada cada vez que un cambio pase la puerta, con los dos resultados del examen en el campo de evidencia.

### Dónde se ve el método funcionando

#### Evaluar skills y agentes

- [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable) · desarrollo guiado por evaluaciones de una skill contable con 50 casos, del 66 % al 100 % en seis iteraciones, reproducible con el corrector incluido
- [accounting-agent-swarm](https://github.com/jleonceo/accounting-agent-swarm) · cuatro agentes para contabilidad con batería de casos, corrector, puertas de no-regresión y verificador independiente; nota de 93,3 sobre 100 con cero falsos positivos
- [orquestacion-enjambres-ia](https://github.com/jleonceo/orquestacion-enjambres-ia) · enrutado entre varios agentes evaluado a ciegas, 15 de 15

#### Verificar con código

- [verificacion-determinista-ia](https://github.com/jleonceo/verificacion-determinista-ia) · coherencia de los datos comprobada con aritmética, sin IA
- [guardianes-verificados-ia](https://github.com/jleonceo/guardianes-verificados-ia) · comprueba, mutándolos, que los guardarraíles de una IA bloquean de verdad
- [gobernanza-agentes-verificada](https://github.com/jleonceo/gobernanza-agentes-verificada) · comprueba con código que un sistema de agentes sigue funcionando como se diseñó
- [adherencia-reglas](https://github.com/jleonceo/adherencia-reglas) · mide desde el historial local si el agente cumple las reglas que le escribiste
- [detector-control-negativo](https://github.com/jleonceo/detector-control-negativo) · cuenta los bancos de pruebas que darían verde con el código roto

#### Proteger lo que sale y lo que recuerda el agente

- [pii-output-gate](https://github.com/jleonceo/pii-output-gate) · bloquea el texto con datos personales antes de publicarlo
- [agent-memory-governance](https://github.com/jleonceo/agent-memory-governance) · gobernanza de la memoria persistente de un agente
- [claude-code-context-management](https://github.com/jleonceo/claude-code-context-management) · evita que CLAUDE.md y MEMORY.md saturen el contexto

## English

On 23 May 2026 a bulk update ran against my project's accounting database. It raised sales by 30 % without touching the offsetting entries. In one go, 2,449 of the 7,159 journal entries fell out of balance. AI agents break the same way at a different scale: a skill that worked yesterday can get things wrong today without throwing a single error. The five checks below are how I catch that in time. I wrote them for anyone building skills or agents on real data, whether you're an analyst, a developer or a technical lead. They come from a fictional set of books I keep as a financial controller: TechAcces S.L., with synthetic data in MySQL, Power BI on top and Claude agents proposing journal entries. The figures in these cases come from that project's private history, so they can't be recomputed from this repository. What can be measured in the open is in the repositories linked at the end.

### What a skill is

A skill is a Markdown file that an AI agent reads before it starts work. It gives the agent domain judgement, the rules it has to follow and the points where it should stop and ask. The agent reads it the way it reads any other instruction. That's why a skill that used to work can quietly start failing when the model changes or the data shifts. There's a from-scratch explanation with examples in [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable).

### The five checks

They're listed in the order I apply them to a skill, from the day I write it to the day I let it run on its own.

#### 1. Measure before you trust

A DAX measure in my Power BI dashboard returned +€2,571,241.27. The right figure, sales minus expenses, was +€322,957.19. The measure added up only the debit side of account group 6 and ignored the credits, which is where returns and discounts sit. The dashboard loaded. The number looked plausible, so nothing on screen hinted at a problem.

Ever since, every new measure gets checked against a SQL query. It has to match to the cent before it ships. I do the same with skills by another route: before I rely on one, I run it against a set of test cases whose correct answers I pin down in advance, known in model evaluation as a golden set. It's the same set I rerun in every check that follows. An automated grader with written rules produces the score, so two people running it get the same result.

To set this up you need three pieces. First, a handful of cases that look like the real work. Second, the expected answer for each one, written before you see what the skill says. Third, a script that compares the two and returns a score, which lets you rerun the test whenever you touch something.

#### 2. The no-regression gate

When a skill fails in a new way, I act on that first failure. What I control is when a fix gets accepted. The gate has two conditions:

1. The change fixes more cases than it breaks.
2. No case that used to pass starts failing.

You rerun every earlier case in the same step as the edit. Waiting to see whether something breaks later only delays the moment you find out. I learned this from a fix aimed at a single case that sank the whole group covering new suppliers and customers, since 6 of its 9 cases started asking for review for no reason as soon as it went in. I rolled it back. The problem shows up outside my project too: in the SkillsBench study, 16 out of 84 skills got worse after being changed.

If you already have the test set from the previous check, the gate is a script that runs it before and after the change and compares the results case by case. Keep both runs, because they're the evidence that goes into the decision log.

#### 3. A test you can't game

On 30 May 2026 I settled on a principle: the simulation adapts to the system. In practice, when a test fails, the only things you may touch are the test's own variables. That means an expected answer that was set wrong, or the test script. The skill, the data and the business rules stay as they are, even if the test stays red. Bend them until it passes and the score stops measuring the real work.

Three days later came the first attempt at gaming it. With one case failing, the agent helping me improve a data analysis skill wrote two new rules while looking straight at the test's reference figure, with no aim other than hitting it on the next run. One reclassified shifted years and the other was a `drop_duplicates`. The second change also deleted 4,198 rows instead of 1,500. It had wiped out legitimate duplicates. The resulting skill scored 0 out of 3. I spotted it straight away and everything was reverted. That day I understood that the no-regression gate can't catch this. A gamed fix can repair the failing case without breaking any of the others. The only defence is one question for every change: is this general judgement, or the answer to this one case? Something useful did come out of that session. How to handle European versus American number formats depends on who exported the file. It was rewritten as a general five-step rule that works for any input file.

The second attempt ran the other way and is harder to spot. The expected answer for a case was changed on the grounds that it was the account normally used for that kind of entry. A look at the database showed that the account didn't exist. The test had been bent to fit the system's mistake. That change was rolled back as well, the same way as the first.

In your own project, every change to an expected answer needs a justification against the source of truth. That source might be the database or the regulation. Every new rule in a skill should make sense without naming the case that prompted it. If you have to quote the test figure to justify a rule, what you have is that case's answer dressed up as judgement. Take it out before you run the test set again.

#### 4. The independent verifier

Whoever produces a result doesn't get to sign it off. Every finding or proposal from an agent goes through a second agent that reviews it from scratch before I act. These are the cases that convinced me:

- On 29 May 2026 three auditing agents came back with 10 findings. The verifier confirmed 8, rated 1 as partly right and flagged 1 as false. Without it I'd have fixed something that was fine.
- During an evaluation of the accounting system, the validating agent blocked two correct journal entries. It claimed their accounts didn't exist. A check against the database showed they did, so the reviewer's verdict also gets tested against the data before anyone acts on it.
- On 5 June 2026 a sceptical verifier knocked down a claim I was about to use as a product's selling point. The claim was that no other platform had anything like CLAUDE.md. ChatGPT and Gemini both have it, so does Cursor, and there's also the AGENTS.md standard.

What changes the outcome most is what you hand the verifier. I measured it on 31 July 2026 on the same file. One verifier that was given the list of findings reported 0 false ones, while another that only got the artefact and the task caught 1 false finding and 8 defects the list never mentioned anywhere. In practice, give the review to a different agent from the one that produced the result. Hand it the artefact and what it needs to check. **Never pass it the list of findings**: with that list in front of it, the verifier found fewer problems.

#### 5. Autonomy based on reversibility

This last check decides what the agent may do without asking you. The harder an action is to undo, the more human control it gets.

| Level | What falls here | What the agent does |
|---|---|---|
| Autonomous | Queries, analysis, alerts and drafts in a table kept apart from the live data | Goes ahead without asking |
| Autonomous with notice | Improvement proposals and candidate test cases | Goes ahead and lets you know |
| Confirmation required | Writing to the live data and changing a skill in use | Waits for your go-ahead |
| Human only | Deleting, no exceptions | You do it yourself |

I enforce the deletion rule on the resource itself. The database user the agent connects with has no DELETE privilege. MySQL refuses it with error 1142, whichever path the command takes. I'd suggest doing the same in your project, through the database user's own permissions. That way the lock holds even if the agent misreads its instructions.

### The decision log

Every change that clears the checks is logged with four fields:

- What changed.
- Why it changed.
- What evidence backs it, including the result of the no-regression gate.
- What was ruled out.

The fourth is the one almost nobody fills in. It's also the most valuable when, months later, someone proposes an option that was already tried and rejected. The template lives in [docs/plantilla_registro_decisiones.md](docs/plantilla_registro_decisiones.md). It comes with a real filled-in example, a wage garnishment, with its three rejected options: accounts 410, 475 and 52x. Copy it into your project. Add an entry every time a change gets through the gate, with both test runs in the evidence field.

### Where to see the method at work

#### Evaluating skills and agents

- [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable) · eval-driven development of an accounting skill with 50 cases, from 66 % to 100 % in six iterations, reproducible with the bundled grader
- [accounting-agent-swarm](https://github.com/jleonceo/accounting-agent-swarm) · four agents for bookkeeping with a test set, grader, no-regression gates and an independent verifier; scores 93.3 out of 100 with zero false positives
- [orquestacion-enjambres-ia](https://github.com/jleonceo/orquestacion-enjambres-ia) · routing across several agents, blind-evaluated, 15 out of 15

#### Verifying with code

- [verificacion-determinista-ia](https://github.com/jleonceo/verificacion-determinista-ia) · data consistency checked with arithmetic, no AI involved
- [guardianes-verificados-ia](https://github.com/jleonceo/guardianes-verificados-ia) · checks, by mutating them, that an AI's guardrails actually block
- [gobernanza-agentes-verificada](https://github.com/jleonceo/gobernanza-agentes-verificada) · checks with code that an agent system still works the way it was designed
- [adherencia-reglas](https://github.com/jleonceo/adherencia-reglas) · measures from the local history whether the agent follows the rules you wrote for it
- [detector-control-negativo](https://github.com/jleonceo/detector-control-negativo) · counts the test suites that would pass with the code broken

#### Protecting what goes out and what the agent remembers

- [pii-output-gate](https://github.com/jleonceo/pii-output-gate) · blocks text containing personal data before it's published
- [agent-memory-governance](https://github.com/jleonceo/agent-memory-governance) · governance for an agent's persistent memory
- [claude-code-context-management](https://github.com/jleonceo/claude-code-context-management) · keeps CLAUDE.md and MEMORY.md from flooding the context

---

Construido por / Built by [Juan Luis León Rodríguez](https://juanluisleon.vercel.app) · mayo-septiembre 2026 · Licencia [CC BY-NC-SA 4.0](LICENSE)
