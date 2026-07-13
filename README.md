# Gobernar skills de IA que trabajan con datos reales

[Español](#español) · [English](#english)

---

## Español

Cómo gobernar skills de IA que trabajan con datos reales: el método que uso a diario,
contado con los casos que lo fueron formando. Sin marcos teóricos. Cada regla de este
repo existe porque algo se rompió primero.

### Antes de nada: qué es una skill

Una skill es un fichero de texto que le da criterio a un modelo de IA. No es código que
se ejecuta, es conocimiento que el modelo lee antes de trabajar: qué reglas aplicar, qué
cuentas usar, cuándo frenar y pedir revisión. Si quieres la explicación completa desde
cero, está en [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable).

El problema aparece cuando esa skill trabaja con datos de verdad. Los modelos cambian,
los datos cambian, y una skill que ayer funcionaba hoy puede estar fallando sin que nadie
lo note. Gobernarla significa tener respuesta para tres preguntas: ¿cómo sé que funciona?,
¿cómo la mejoro sin romper lo que ya hacía bien?, y ¿cuánta libertad le doy para actuar sola?

### El día que nació el método

El 23 de mayo de 2026 lancé una actualización masiva sobre una base de datos contable:
modifiqué líneas de ventas y compras sin ajustar sus contrapartidas. Resultado: **2.449
asientos descuadrados**. La partida doble, rota de golpe.

De ese error salió la primera regla: ninguna operación compleja toca los datos sin una
simulación previa. Y de los errores siguientes salieron las demás. Este repo es ese
recorrido, ordenado.

Las cifras de estos incidentes salen del registro interno del proyecto (una contabilidad
ficticia sobre MySQL). Las partes medibles en abierto están en los repos que enlazo al
final: el salto del 66% al 100% en [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable)
y las 19 simulaciones en [accounting-agent-swarm](https://github.com/jleonceo/accounting-agent-swarm).

### Las cinco reglas, con el caso que las parió

#### 1. Medir antes de confiar

Una medida de gastos en Power BI daba 322.957 €. El dato real eran 2.571.241 €: casi
ocho veces más. La medida calculaba solo el debe del grupo 6 e ignoraba el haber
(devoluciones, descuentos). Lo inquietante es que todo "funcionaba": el dashboard
cargaba, la cifra parecía razonable, nadie se quejaba.

La lección: **"funciona" no es lo mismo que "está bien"**. Un sistema puede ser
matemáticamente coherente y conceptualmente erróneo. Desde entonces, cada medida nueva
se cruza con SQL al céntimo, y cada skill se examina contra un golden set: una batería
de casos con la respuesta correcta conocida de antemano, puntuada por un corrector
automático. La opinión no puntúa; el corrector sí.

#### 2. La puerta de no-regresión

Mejorar una skill es fácil. Mejorarla sin estropear lo que ya hacía bien es lo difícil.
Me pasó con una corrección quirúrgica que parecía inocente: arreglé un caso concreto y
el grupo de altas de terceros se hundió, con 6 de 9 casos marcándose a revisión sin motivo.

La regla que lo evita: **ningún cambio se acepta si los casos que antes pasaban dejan de
pasar**. Después de cada modificación se relanzan los casos previos. Si algo cae, el
cambio se revierte, aunque arreglara lo que prometía arreglar.

#### 3. El examen no se amaña

La regla más incómoda del método: **la simulación se adapta al sistema, nunca el sistema
a la simulación**. La cuento con dos errores míos, porque caí dos veces.

La primera: con un caso fallando, escribí una versión de la skill calibrada mirando la
respuesta esperada del examen. Ingeniería inversa pura. El propietario del proyecto lo
detectó de inmediato y se revirtió todo.

La segunda fue más sutil. Cambié la respuesta esperada de un caso razonando "esta es la
convención de cuentas". Sonaba bien. La base de datos demostró que la cuenta que yo había
puesto no existía: había adaptado el examen al error del sistema, en vez de arreglar el
sistema. También se revirtió, y el informe de aquel día lo documenta tal cual.

La puerta de no-regresión no te protege de amañar: para eso solo sirve preguntarse, ante
cada cambio en un examen, *¿esto es criterio general o es la respuesta de este caso?*

#### 4. El verificador independiente

Los agentes de IA se equivocan con seguridad. En una evaluación reciente, el agente
validador frenó dos asientos correctos alegando que sus cuentas "no existían". Un primer
análisis le creyó. Al verificar contra la base de datos, las cuentas existían: el que
fallaba era el propio validador. En otra ocasión, de 10 hallazgos reportados por un
agente, 1 resultó ser falso al comprobarlo.

Por eso ninguna afirmación importante se acepta del primer agente. Siempre hay un segundo
que consulta la fuente directamente, sin acceso a las conclusiones del primero, y cuyo
trabajo es intentar refutarlas. Cuesta más tiempo. Cuesta menos que un asiento falso en
los libros.

#### 5. Autonomía proporcional al riesgo

¿Cuánta libertad le das a un sistema de IA sobre datos reales? Ni toda ni ninguna: la
respuesta depende de lo reversible que sea cada operación.

| Nivel | Operaciones | Quién decide |
|---|---|---|
| Autónomo | Consultas, análisis, alertas, borradores en tablas de paso | El sistema, sin preguntar |
| Autónomo con aviso | Propuestas de mejora, candidatos a casos de examen | El sistema avisa; el humano puede ignorar |
| Confirmación obligatoria | Escrituras en los datos reales, cambios en skills activas | El humano confirma antes |
| Solo humano | Borrados. Sin excepción | Nunca el sistema |

Lo importante no son las cuatro filas, es el criterio que las genera: **a más difícil de
deshacer, más control humano**. Un análisis equivocado se repite; un borrado equivocado no.

### El registro de decisiones

Todo lo anterior se sostiene sobre una pieza aburrida y decisiva: cada cambio queda
registrado con cuatro campos, qué cambió, por qué, qué evidencia lo motivó y qué se
descartó. El cuarto campo es el que casi nadie escribe y el que más vale: dentro de tres
meses, saber por qué NO hiciste algo evita repetir el debate entero.

En [docs/plantilla_registro_decisiones.md](docs/plantilla_registro_decisiones.md) está la
plantilla con un ejemplo real rellenado.

### Este repo se reescribió a sí mismo

La primera versión de este repositorio (mayo de 2026) contaba este marco como teoría:
estructura prometida, cifras sin fuente, tono de folleto. Era el plan de un método, no
un método. Dos meses después, el método existe, está medido y tiene cicatrices. Esta
versión lo reescribe entero desde la evidencia, que es exactamente lo que el método manda
hacer con cualquier cosa que no la tenga.

### Dónde verlo funcionando

- [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable): el examen de una
  skill individual, de 66% a 100% en seis iteraciones medidas.
- [accounting-agent-swarm](https://github.com/jleonceo/accounting-agent-swarm): el método
  aplicado a un enjambre de agentes, con sus 19 simulaciones y las caídas explicadas.
- [agent-memory-governance](https://github.com/jleonceo/agent-memory-governance): la misma
  filosofía aplicada a la memoria persistente del agente.
- [orquestacion-enjambres-ia](https://github.com/jleonceo/orquestacion-enjambres-ia): el enrutado multi-agente, cómo se decide a qué agente va cada petición sin romper al crecer.
- [verificacion-determinista-ia](https://github.com/jleonceo/verificacion-determinista-ia): la coherencia del estado comprobada por pura aritmética, sin IA.
- [tu-primer-asistente-ia-web](https://github.com/jleonceo/tu-primer-asistente-ia-web): qué es un asistente de IA, para quien empieza de cero.
- [tesoreria-forecast-ia](https://github.com/jleonceo/tesoreria-forecast-ia): previsión de caja por descomposición con backtesting, más ratios y aging.
- [control-interno-fraude-ia](https://github.com/jleonceo/control-interno-fraude-ia): detección de fraude contable con aritmética, dentro de un marco de control interno.

---

## English

How to govern AI skills that work with real data: the method I use daily, told through the
cases that shaped it. No theoretical frameworks. Every rule in this repo exists because
something broke first.

### First things first: what a skill is

A skill is a text file that gives judgement to an AI model. It is not code that runs, it is
knowledge the model reads before working: which rules to apply, which accounts to use, when
to stop and ask for review. If you want the full explanation from scratch, it is in
[llm-eval-contable](https://github.com/jleonceo/llm-eval-contable).

The problem appears when that skill works with real data. Models change, data changes, and a
skill that worked yesterday may be failing today without anyone noticing. Governing it means
having an answer to three questions: how do I know it works? how do I improve it without
breaking what it already did well? and how much freedom do I give it to act on its own?

### The day the method was born

On 23 May 2026 I ran a mass update on an accounting database: I modified sales and purchase
lines without adjusting their counterparts. Result: **2,449 unbalanced entries**. Double
entry, broken at a stroke.

Out of that error came the first rule: no complex operation touches the data without a prior
simulation. And the following errors produced the rest. This repo is that path, in order.

The figures for these incidents come from the project's internal log (a fictitious set of
books on MySQL). The parts that can be measured in the open are in the repos I link at the
end: the jump from 66% to 100% in [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable)
and the 19 simulations in [accounting-agent-swarm](https://github.com/jleonceo/accounting-agent-swarm).

### The five rules, with the case that gave birth to each

#### 1. Measure before you trust

An expenses measure in Power BI returned 322,957 EUR. The real figure was 2,571,241 EUR:
almost eight times more. The measure computed only the debit side of group 6 and ignored the
credit (returns, discounts). The unsettling part is that everything "worked": the dashboard
loaded, the figure looked reasonable, nobody complained.

The lesson: **"it works" is not the same as "it is right"**. A system can be mathematically
coherent and conceptually wrong. Since then, every new measure is cross-checked against SQL
to the cent, and every skill is examined against a golden set: a battery of cases with the
correct answer known in advance, scored by an automatic grader. Opinion does not score; the
grader does.

#### 2. The no-regression gate

Improving a skill is easy. Improving it without spoiling what it already did well is the hard
part. It happened to me with a surgical fix that looked harmless: I fixed a specific case and
the third-party creation group collapsed, with 6 of 9 cases flagged for review for no reason.

The rule that prevents it: **no change is accepted if cases that used to pass stop passing**.
After each modification the previous cases are re-run. If something drops, the change is
reverted, even if it fixed what it promised to fix.

#### 3. The exam is not rigged

The most uncomfortable rule of the method: **the simulation adapts to the system, never the
system to the simulation**. I tell it through two of my own mistakes, because I fell twice.

The first: with a case failing, I wrote a version of the skill calibrated by looking at the
exam's expected answer. Pure reverse engineering. The project owner caught it immediately and
everything was reverted.

The second was subtler. I changed the expected answer of a case, reasoning "this is the
account convention". It sounded right. The database proved that the account I had used did not
exist: I had adapted the exam to the system's error, instead of fixing the system. It was
reverted too, and that day's log documents it as it happened.

The no-regression gate does not protect you from rigging: for that, the only thing that works
is asking, before every change to an exam, *is this general judgement, or is it the answer to
this one case?*

#### 4. The independent verifier

AI agents get things wrong with confidence. In a recent evaluation, the validator agent
stopped two correct entries claiming their accounts "did not exist". A first analysis believed
it. On checking against the database, the accounts existed: the one failing was the validator
itself. On another occasion, of 10 findings reported by an agent, 1 turned out to be false on
checking.

That is why no important claim is accepted from the first agent. There is always a second one
that consults the source directly, without access to the first one's conclusions, and whose
job is to try to refute them. It costs more time. It costs less than a false entry in the books.

#### 5. Autonomy proportional to risk

How much freedom do you give an AI system over real data? Neither all nor none: the answer
depends on how reversible each operation is.

| Level | Operations | Who decides |
|---|---|---|
| Autonomous | Queries, analysis, alerts, drafts in staging tables | The system, without asking |
| Autonomous with notice | Improvement proposals, candidate exam cases | The system notifies; the human may ignore |
| Confirmation required | Writes to real data, changes to active skills | The human confirms first |
| Human only | Deletions. No exception | Never the system |

What matters is not the four rows, it is the criterion that generates them: **the harder to
undo, the more human control**. A wrong analysis is repeated; a wrong deletion is not.

### The decision log

Everything above rests on a boring, decisive piece: every change is logged with four fields,
what changed, why, what evidence motivated it, and what was discarded. The fourth field is the
one almost nobody writes and the one worth most: three months later, knowing why you did NOT
do something saves repeating the whole debate.

The template, with a real example filled in, is in
[docs/plantilla_registro_decisiones.md](docs/plantilla_registro_decisiones.md).

### This repo rewrote itself

The first version of this repository (May 2026) told this framework as theory: promised
structure, figures without a source, brochure tone. It was the plan of a method, not a method.
Two months later, the method exists, it is measured and it has scars. This version rewrites it
entirely from the evidence, which is exactly what the method dictates for anything that lacks it.

### Where to see it working

- [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable): the exam of a single
  skill, from 66% to 100% over six measured iterations.
- [accounting-agent-swarm](https://github.com/jleonceo/accounting-agent-swarm): the method
  applied to an agent swarm, with its 19 simulations and its drops explained.
- [agent-memory-governance](https://github.com/jleonceo/agent-memory-governance): the same
  philosophy applied to the agent's persistent memory.
- [orquestacion-enjambres-ia](https://github.com/jleonceo/orquestacion-enjambres-ia): multi-agent routing, how each request is sent to the right agent without breaking as it grows.
- [verificacion-determinista-ia](https://github.com/jleonceo/verificacion-determinista-ia): state coherence checked by pure arithmetic, without AI.
- [tu-primer-asistente-ia-web](https://github.com/jleonceo/tu-primer-asistente-ia-web): what an AI assistant is, for absolute beginners.
- [tesoreria-forecast-ia](https://github.com/jleonceo/tesoreria-forecast-ia): cash-flow forecasting by decomposition with backtesting, plus ratios and aging.
- [control-interno-fraude-ia](https://github.com/jleonceo/control-interno-fraude-ia): accounting fraud detection with arithmetic, inside an internal-control framework.

---

Construido por / Built by [Juan Luis León Rodríguez](https://juanluisleon.vercel.app) · mayo-julio 2026 · Licencia [CC BY-NC-SA 4.0](LICENSE)
