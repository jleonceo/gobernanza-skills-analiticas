# Plantilla: registro de decisiones con evidencia

Cuatro campos por entrada. El formato importa menos que la disciplina de rellenarlos
todos, sobre todo el último.

```markdown
## FECHA — Título del cambio

**Qué cambió:** ficheros, versiones, reglas. Concreto, sin resumir de más.

**Por qué:** el problema que lo motivó. Si nace de un fallo, el fallo con su caso.

**Evidencia / puerta:** qué prueba sostiene el cambio (simulación, query, test) y el
resultado de la puerta de no-regresión. Sin este campo, la entrada no vale.

**Descartado:** qué alternativas se consideraron y por qué se rechazaron.
```

## Por qué el campo "Descartado" es el que más vale

Los tres primeros campos cuentan lo que hiciste. El cuarto cuenta lo que decidiste no
hacer, y esa información no está en ningún otro sitio: ni en el código, ni en el diff,
ni en la memoria de nadie pasados tres meses. Cuando el mismo debate reaparece (y
reaparece), una línea de "descartado" lo cierra en un minuto.

## Ejemplo real rellenado

Sacado del registro del proyecto, tal cual se escribió (solo se acortan rutas):

```markdown
## 09/06/2026 — Nóminas con embargo judicial: cuenta nueva

**Qué cambió:** el generador contabiliza el embargo judicial de una nómina en una
subcuenta nueva de 465 ("Embargos a favor de terceros"), creada en el plan de cuentas.
Skills extractor/generador/validador actualizadas y versionadas.

**Por qué:** dos bugs reproducidos en simulación. El extractor verificaba el neto con
una fórmula que ignoraba anticipos y embargos, y daba por descuadrada una nómina
correcta. Y la regla anterior mandaba el embargo a la cuenta 410, que es de acreedores
comerciales: un embargo judicial no es actividad comercial.

**Evidencia / puerta:** mini-simulación de nóminas con el enjambre real. El caso que
fallaba pasó a contabilizarse con 7 líneas y cuadre exacto; los casos previos siguieron
pasando (0 regresiones). Una iteración intermedia destapó un segundo fallo de colocación
del IRPF, que se cerró en la misma sesión.

**Descartado:**
- Cuenta 410 (acreedores comerciales): el embargo no es actividad comercial. Era
  nuestra propia norma anterior; se corrigió contra el PGC oficial, no contra la costumbre.
- Cuenta 475 (Hacienda): el acreedor de un embargo judicial a un particular no es
  Hacienda.
- Grupo 52 (deudas financieras): forzaría a tratar el embargo como deuda con entidades
  de crédito, que no lo es.
```

Fíjate en lo que permite este registro: medio año después, cualquiera (humano o agente)
puede saber no solo qué cuenta se usa para un embargo, sino por qué no se usan las otras
tres candidatas. Esa es la diferencia entre una decisión y una costumbre.
