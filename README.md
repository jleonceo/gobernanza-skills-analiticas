# Gobernanza de Skills Analíticas

[![Licencia: CC BY-NC-SA 4.0](https://img.shields.io/badge/Licencia-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
[![Framework](https://img.shields.io/badge/Framework-Skills%20Analíticas-blue)](https://github.com/jleonceo/gobernanza-skills-analiticas)
[![Versión](https://img.shields.io/badge/Versión-1.0-green)]()

---

## ⚠️ Cómo evité que mi empresa tomara decisiones con un error de 2,57 millones de euros

**Mayo de 2026.** Estaba auditando el modelo Power BI de TechAcces (empresa de servicios con 608 líneas contables y 78 medidas DAX interrelacionadas) cuando detecté algo inquietante:

El balance de pérdidas y ganancias mostraba **323.000€**.

Pero algo no cuadraba. Hice validación cruzada con MySQL. El resultado real debería ser **2.570.000€**.

**Error detectado: ×7,96 veces el valor real.**

### 🔍 Causa raíz identificada

Una medida base (`Total_Gastos_Brutos`) arrastraba un error de redondeo de **0,03€** que se propagaba a través de **12 medidas jerárquicas**, distorsionando el EBITDA en un **796%**.

**Tiempo de detección y corrección:** 3 horas y 40 minutos de trabajo interdisciplinar:
1. Auditoría de dependencias (DAX → SQL)
2. Validación cruzada con `libro_diario` (MySQL)
3. Aplicación del Principio de Imagen Fiel (Plan General Contable)
4. Reconstrucción de la cadena de medidas

**Sin criterio contable + validación estructural = ese error habría costado decisiones estratégicas catastróficas.**

---

## 🎯 El Problema Real del Mercado

Las empresas no sufren por falta de herramientas de IA. Sufren por **falta de control** sobre ellas.

Cuando un departamento administrativo o analítico usa IA para escribir fórmulas DAX, consultas SQL o automatizar decisiones, se enfrenta a **tres riesgos críticos**:

### 1️⃣ **Amnesia Operativa**
Las soluciones a problemas complejos no se estandarizan. Se resuelven "al momento" y se olvidan. Cada cierre mensual = reinventar la rueda.

### 2️⃣ **Saturación de Recursos**
Se procesan masas de información redundante una y otra vez, ralentizando los sistemas y multiplicando costes de tokens/API.

### 3️⃣ **Automatización Ciega (Riesgo de pérdida de datos)**
Las herramientas automatizadas toman decisiones lógicas basadas en metadatos superficiales (fechas de archivos) **sin entender el valor histórico/auditable del dato para el negocio**.

**Resultado:** Eliminación accidental de auditorías críticas, errores de cálculo propagados y decisiones empresariales basadas en datos incorrectos.

---

## 💡 La Solución: Arquitectura de Gobernanza de 3 Niveles

Para resolver este caos operativo, este framework propone estructurar la interacción humano-IA mediante un **modelo de 3 niveles** que separa:

```
┌─────────────────────────────────────────────────┐
│  NIVEL 1: ORQUESTADOR                           │
│  (Punto de entrada único)                       │
│  ┌─────────────────────────────────────────┐   │
│  │ "Crear medida DAX para análisis churn"  │   │
│  └──────────────┬──────────────────────────┘   │
└─────────────────┼───────────────────────────────┘
                  │
    ┌─────────────┼─────────────┐
    │             │             │
    ▼             ▼             ▼
┌─────────┐  ┌─────────┐  ┌─────────┐
│ NIVEL 2 │  │ NIVEL 2 │  │ NIVEL 2 │
│ SKILL   │  │ SKILL   │  │ SKILL   │
│ SQL     │  │ DAX     │  │ PGC     │
└────┬────┘  └────┬────┘  └────┬────┘
     │            │            │
     └────────────┼────────────┘
                  │
                  ▼
         ┌────────────────┐
         │  NIVEL 3: RAG  │
         │  (Diccionarios)│
         │  Consulta bajo │
         │    demanda     │
         └────────────────┘
```

### 📊 Niveles explicados:

**NIVEL 1 - Orquestador:** Protocolo centralizado que analiza la orden del usuario y la deriva al especialista adecuado. Carga permanente, consumo mínimo de recursos.

**NIVEL 2 - Skills Ejecutoras:** Plantillas de trabajo estandarizadas y estancas (SQL, Power BI, Contabilidad, n8n). Cada una con restricciones férreas, flujos paso a paso y **anclas de interpretación** (ejemplos concretos que eliminan ambigüedad).

**NIVEL 3 - Base de Conocimiento:** Diccionarios de datos, manuales y referencias masivas. **Solo se consultan bajo demanda**, evitando saturación de memoria operativa.

---

## 📈 Impacto Medible (ROI)

### Eficiencia Operativa
- ✅ **40-50% reducción** en carga de procesamiento de contexto
- ✅ **+11 minutos ganados** por sesión operativa
- ✅ **44 horas/año liberadas** por puesto analítico

### Seguridad de Datos
- ✅ **Protocolo "Freno de Mano"** de 5 pasos antes de eliminar datos históricos
- ✅ **Validación cruzada obligatoria** (DAX ↔ SQL) antes de reportar resultados
- ✅ **Trazabilidad completa** de decisiones críticas

### Reducción de Riesgos
- ✅ Mitigación del error tipo "TechAcces Mayo 2026" (×7,96)
- ✅ Prevención de pérdidas de datos de auditoría
- ✅ Estandarización de respuestas de IA según reglas de negocio

---

## 🛡️ Caso Real: Protocolo "Freno de Mano"

Durante el desarrollo de esta metodología, la IA recomendó eliminar archivos "antiguos" basándose únicamente en metadatos de fechas.

**Archivos marcados para borrado:** Snapshots históricos de bases de datos SQL con 470 líneas de consultas base testeadas. **Irrecuperables.**

**Protocolo aplicado (5 pasos):**

```
[ PASO 1: INVENTARIO ]
Identificar archivos afectados con detalle de rangos de fechas
          │
          ▼
[ PASO 2: MATRIZ DE EVIDENCIA ]
Justificación explícita de por qué el dato ya no es necesario
          │
          ▼
[ PASO 3: VERIFICACIÓN CRUZADA ]
Buscar duplicidades en repositorios locales o backups
          │
          ▼
[ PASO 4: ANÁLISIS DE DEPENDENCIAS ]
Comprobar que ningún informe activo consume este recurso
          │
          ▼
[ PASO 5: VALIDACIÓN HUMANA OBLIGATORIA ]
Firma y aprobación del Controller/Responsable
```

**Resultado:** Eliminación detenida. Datos críticos preservados.

---

## 📂 Contenido del Repositorio

Para proteger la propiedad intelectual y el secreto comercial, este repositorio expone **exclusivamente la arquitectura conceptual y las plantillas reutilizables**:

```
gobernanza-skills-analiticas/
│
├── README.md (este archivo)
├── LICENSE
│
├── core-architecture/
│   ├── orquestador_procesos.md
│   ├── estructura_skills_template.md
│   └── ejemplo_skill_sql.md (NUEVO)
│
├── governance-protocols/
│   ├── protocolo_decisiones_criticas.md
│   └── matriz_validacion_cruzada.md (NUEVO)
│
├── case-studies/
│   └── techacces_error_arrastre_mayo2026.md
│
└── docs/
    └── manual_gobernanza_analitica.md
```

---

## 🚀 ¿Cómo usar esta metodología?

### Para Analistas/Controllers:
1. Descarga las plantillas de `/core-architecture/`
2. Adapta `estructura_skills_template.md` a tus herramientas
3. Aplica el `protocolo_decisiones_criticas.md` antes de eliminar datos

### Para Equipos de IT/Data:
1. Lee `orquestador_procesos.md`
2. Implementa validación cruzada SQL ↔ Power BI
3. Configura alertas de descuadre automático

### Para Directores Financieros:
1. Revisa la sección "Gobierno de Datos"
2. Evalúa el ROI (44h/año por analista)
3. Contacta para consultoría de implementación

---

## 🎓 ¿Para qué capacita este framework?

Al dominar esta metodología, demuestras competencias que el mercado actual demanda con urgencia:

- **AI Operations (AIOps):** Diseño del ciclo de vida de instrucciones para LLMs, mitigando alucinaciones y optimizando costes de API
- **Arquitectura de Agentes:** Patrones de diseño modernos, migrando de interacción simple humano-IA hacia arquitecturas orquestadas
- **Consultoría de Transformación Digital:** Traducir eficiencia técnica en KPIs financieros legibles por la dirección (ROI)

---

## ✍️ Autor y Contacto

**Juan Luis León Rodríguez**  
Especialista en Control de Gestión, Reporting y Optimización de Procesos con IA

📩 **Email:** jleonceo@gmail.com  
💼 **LinkedIn:** [linkedin.com/in/jlleonrodriguez](https://www.linkedin.com/in/jlleonrodriguez/)  
🐙 **GitHub:** [@jleonceo](https://github.com/jleonceo)

Para **consultoría, colaboraciones o implementación** de este framework en tu empresa, contáctame por email o LinkedIn.

---

## 📜 Licencia y Uso

Este framework está licenciado bajo [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).

**✅ Uso libre para:**
- Investigación académica
- Proyectos personales
- Pequeñas empresas (<50 empleados)

**⚠️ Uso comercial:**
- Requiere contacto previo para adaptación y consultoría

**❌ Prohibido:**
- Reventa de plantillas sin modificación
- Uso en formaciones de pago sin atribución

---

## 🌟 Reconocimientos

Metodología desarrollada e investigada entre 2024-2026.

El caso TechAcces Mayo 2026 validó empíricamente la necesidad de este framework en entornos de producción real.

---

**⭐ Si este proyecto te resulta útil, dale una estrella en GitHub y compártelo con tu equipo.**
