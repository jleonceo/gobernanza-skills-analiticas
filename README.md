# gobernanza-skills-analiticas

Cómo gobernar skills de IA que trabajan con datos reales: el método que uso a diario,
contado con los casos que lo fueron formando. Sin marcos teóricos. Cada regla de este
repo existe porque algo se rompió primero.

---

## Antes de nada: qué es una skill

Una skill es un fichero de texto que le da criterio a un modelo de IA. No es código que
se ejecuta, es conocimiento que el modelo lee antes de trabajar: qué reglas aplicar, qué
cuentas usar, cuándo frenar y pedir revisión. Si quieres la explicación completa desde
cero, está en [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable).

El problema aparece cuando esa skill trabaja con datos de verdad. Los modelos cambian,
los datos cambian, y una skill que ayer funcionaba hoy puede estar fallando sin que nadie
lo note. Gobernarla significa tener respuesta para tres preguntas: ¿cómo sé que funciona?,
¿cómo la mejoro sin romper lo que ya hacía bien?, y ¿cuánta libertad le doy para actuar sola?

## El día que nació el método

El 23 de mayo de 2026 lancé una actualización masiva sobre una base de datos contable:
modifiqué líneas de ventas y compras sin ajustar sus contrapartidas. Resultado: **2.449
asientos descuadrados**. La partida doble, rota de golpe.

De ese error salió la primera regla: ninguna operación compleja toca los datos sin una
simulación previa. Y de los errores siguientes salieron las demás. Este repo es ese
recorrido, ordenado.

## Las cinco reglas, con el caso que las parió

### 1. Medir antes de confiar

Una medida de gastos en Power BI daba 322.957 €. El dato real eran 2.571.241 €: casi
ocho veces más. La medida calculaba solo el debe del grupo 6 e ignoraba el haber
(devoluciones, descuentos). Lo inquietante es que todo "funcionaba": el dashboard
cargaba, la cifra parecía razonable, nadie se quejaba.

La lección: **"funciona" no es lo mismo que "está bien"**. Un sistema puede ser
matemáticamente coherente y conceptualmente erróneo. Desde entonces, cada medida nueva
se cruza con SQL al céntimo, y cada skill se examina contra un golden set: una batería
de casos con la respuesta correcta conocida de antemano, puntuada por un corrector
automático. La opinión no puntúa; el corrector sí.

### 2. La puerta de no-regresión

Mejorar una skill es fácil. Mejorarla sin estropear lo que ya hacía bien es lo difícil.
Me pasó con una corrección quirúrgica que parecía inocente: arreglé un caso concreto y
el grupo de altas de terceros se hundió, con 6 de 9 casos marcándose a revisión sin motivo.

La regla que lo evita: **ningún cambio se acepta si los casos que antes pasaban dejan de
pasar**. Después de cada modificación se relanzan los casos previos. Si algo cae, el
cambio se revierte, aunque arreglara lo que prometía arreglar.

### 3. El examen no se amaña

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

### 4. El verificador independiente

Los agentes de IA se equivocan con seguridad. En una evaluación reciente, el agente
validador frenó dos asientos correctos alegando que sus cuentas "no existían". Un primer
análisis le creyó. Al verificar contra la base de datos, las cuentas existían: el que
fallaba era el propio validador. En otra ocasión, de 10 hallazgos reportados por un
agente, 1 resultó ser falso al comprobarlo.

Por eso ninguna afirmación importante se acepta del primer agente. Siempre hay un segundo
que consulta la fuente directamente, sin acceso a las conclusiones del primero, y cuyo
trabajo es intentar refutarlas. Cuesta más tiempo. Cuesta menos que un asiento falso en
los libros.

### 5. Autonomía proporcional al riesgo

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

## El registro de decisiones

Todo lo anterior se sostiene sobre una pieza aburrida y decisiva: cada cambio queda
registrado con cuatro campos — qué cambió, por qué, qué evidencia lo motivó y qué se
descartó. El cuarto campo es el que casi nadie escribe y el que más vale: dentro de tres
meses, saber por qué NO hiciste algo evita repetir el debate entero.

En [docs/plantilla_registro_decisiones.md](docs/plantilla_registro_decisiones.md) está la
plantilla con un ejemplo real rellenado.

## Este repo se reescribió a sí mismo

La primera versión de este repositorio (mayo de 2026) contaba este marco como teoría:
estructura prometida, cifras sin fuente, tono de folleto. Era el plan de un método, no
un método. Dos meses después, el método existe, está medido y tiene cicatrices. Esta
versión lo reescribe entero desde la evidencia, que es exactamente lo que el método manda
hacer con cualquier cosa que no la tenga.

## Dónde verlo funcionando

- [llm-eval-contable](https://github.com/jleonceo/llm-eval-contable): el examen de una
  skill individual, de 66% a 100% en seis iteraciones medidas.
- [accounting-agent-swarm](https://github.com/jleonceo/accounting-agent-swarm): el método
  aplicado a un enjambre de agentes, con sus 19 simulaciones y las caídas explicadas.
- [agent-memory-governance](https://github.com/jleonceo/agent-memory-governance): la misma
  filosofía aplicada a la memoria persistente del agente.

---

Construido por [Juan Luis León Rodríguez](https://juanluisleon.vercel.app) · mayo-junio 2026
