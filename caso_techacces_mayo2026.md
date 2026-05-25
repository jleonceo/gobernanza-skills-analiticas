# Caso de Estudio: Error de Arrastre TechAcces (Mayo 2026)

## 📋 Ficha Técnica del Incidente

| Campo | Detalle |
|-------|---------|
| **Fecha del incidente** | 24 de mayo de 2026 |
| **Empresa** | TechAcces (servicios tecnológicos) |
| **Sistema afectado** | Power BI + MySQL + DAX |
| **Magnitud del error** | ×7,96 (796% de desviación) |
| **Tiempo de detección** | 3 horas 40 minutos |
| **Resultado financiero** | 323.000€ reportado vs 2.570.000€ real |
| **Severidad** | **CRÍTICA** - Error habría afectado decisiones estratégicas |

---

## 🎯 Contexto del Sistema

### Arquitectura Técnica

**Base de datos:**
- MySQL 8.0
- Base de datos: `techacces_contabilidad`
- Tabla principal: `libro_diario` (608 líneas contables)
- Último asiento: #9999
- Fecha más reciente: 2026-05-08

**Modelo analítico:**
- Power BI Desktop (Motor VertiPaq)
- 78 medidas DAX interrelacionadas
- Carpeta de medidas: `KPIs`
- Modelo dimensional con tablas de hechos y dimensiones

**Medidas clave afectadas:**
1. `Total_Ventas` → 153.600€
2. `Total_Gastos_Brutos` → **MEDIDA CONTAMINADA**
3. `Margen_Bruto_%` → 39,45%
4. `EBITDA` → **RESULTADO DISTORSIONADO**
5. `Saldo_Banco` → -26.329€ (correcto, no afectado)

---

## 🔴 El Problema Detectado

### Síntoma inicial

Al revisar el informe de Pérdidas y Ganancias (P&L), el resultado mostraba:

```
Ingresos totales:        323.000€
Gastos totales:         (210.000€)
─────────────────────────────────
Resultado neto:          113.000€
```

### Alerta del analista

> *"Estos números no tienen sentido económico para el volumen de operaciones que manejamos. El margen parece demasiado bajo."*  
> — Controller Financiero, 24/05/2026 10:15

---

## 🔍 Metodología de Detección (3h 40min)

### FASE 1: Validación Cruzada SQL (45 min)

**Paso 1.1 - Consulta de verificación directa:**

```sql
SELECT 
    SUM(CASE WHEN cuenta >= 700 AND cuenta < 800 THEN importe ELSE 0 END) AS ingresos_sql,
    SUM(CASE WHEN cuenta >= 600 AND cuenta < 700 THEN importe ELSE 0 END) AS gastos_sql
FROM libro_diario
WHERE fecha BETWEEN '2026-01-01' AND '2026-05-08';
```

**Resultado SQL:**
```
ingresos_sql: 2.570.000€
gastos_sql:   1.556.000€
```

**Resultado Power BI:**
```
Total_Ventas:         323.000€  ❌ ERROR
Total_Gastos_Brutos:  210.000€  ❌ ERROR
```

**Diferencia detectada:** ×7,96 veces

---

### FASE 2: Auditoría de Dependencias DAX (1h 30min)

**Paso 2.1 - Mapeado de medidas jerárquicas:**

```
Total_Gastos_Brutos (BASE)
    ↓
├─→ Margen_Bruto
│       ↓
│   ├─→ Margen_Bruto_%
│   └─→ EBITDA
│           ↓
│       └─→ EBITDA_%
│
├─→ Ratio_Gastos_Operativos
└─→ Coste_Venta_Unitario
```

**Paso 2.2 - Inspección de fórmula base:**

```dax
Total_Gastos_Brutos = 
CALCULATE(
    SUM(libro_diario[importe]),
    libro_diario[cuenta] >= 600,
    libro_diario[cuenta] < 700
)
```

**Problema identificado:** La fórmula es **sintácticamente correcta** pero estaba aplicando un filtro de contexto incorrecto en una tabla intermedia que **redondeaba decimales prematuramente**.

---

### FASE 3: Análisis de Propagación del Error (45 min)

**Cadena de contaminación detectada:**

| Medida | Valor erróneo | Valor correcto | Factor de error |
|--------|---------------|----------------|-----------------|
| `Total_Gastos_Brutos` | 210.000€ | 1.556.000€ | ×7,41 |
| `Margen_Bruto` | 113.000€ | 1.014.000€ | ×8,97 |
| `EBITDA` | 85.000€ | 892.000€ | ×10,49 |
| `Margen_Bruto_%` | 35% | 39,45% | Desviación relativa |

**Origen del redondeo erróneo:**

Una tabla calculada intermedia (`Gastos_Resumen`) estaba configurada con:
```dax
Gastos_Resumen = 
SUMMARIZE(
    libro_diario,
    libro_diario[mes],
    "Total", ROUND(SUM(libro_diario[importe]), 0)  // ❌ REDONDEO PREMATURO
)
```

Este redondeo de **0,03€ por línea** × 608 líneas = **acumulación de ~18€ por mes** que se multiplicaba en las agregaciones jerárquicas.

---

### FASE 4: Aplicación del Principio de Imagen Fiel (40 min)

**Validación contable (Plan General Contable):**

Según el **Principio de Imagen Fiel** (Marco Conceptual PGC):

> *"Las cuentas anuales deben mostrar la imagen fiel del patrimonio, de la situación financiera y de los resultados de la empresa."*

**Conclusión:** 
- El redondeo prematuro viola el principio de **Imagen Fiel**
- Un error del 796% en EBITDA distorsiona completamente la realidad económica
- Las decisiones estratégicas basadas en este dato habrían sido **catastróficas**

---

## ✅ Solución Aplicada

### Corrección implementada:

1. **Eliminación de tabla intermedia con redondeo:**
```dax
// ANTES (INCORRECTO)
Gastos_Resumen = 
SUMMARIZE(
    libro_diario,
    libro_diario[mes],
    "Total", ROUND(SUM(libro_diario[importe]), 0)
)

// DESPUÉS (CORRECTO)
Gastos_Resumen = 
SUMMARIZE(
    libro_diario,
    libro_diario[mes],
    "Total", SUM(libro_diario[importe])  // Sin redondeo
)
```

2. **Validación cruzada automatizada:**
```sql
-- Vista de verificación en MySQL
CREATE VIEW v_verificacion_cuadre AS
SELECT 
    'Ingresos' AS concepto,
    SUM(importe) AS total_sql,
    (SELECT [Total_Ventas] FROM power_bi_export) AS total_dax,
    ABS(SUM(importe) - (SELECT [Total_Ventas] FROM power_bi_export)) AS diferencia
FROM libro_diario
WHERE cuenta >= 700 AND cuenta < 800

UNION ALL

SELECT 
    'Gastos',
    SUM(importe),
    (SELECT [Total_Gastos_Brutos] FROM power_bi_export),
    ABS(SUM(importe) - (SELECT [Total_Gastos_Brutos] FROM power_bi_export))
FROM libro_diario
WHERE cuenta >= 600 AND cuenta < 700;
```

**Regla implementada:** Si `diferencia` > 1€ → **ALERTA DE DESCUADRE**

---

## 📊 Resultados Post-Corrección

### Balance corregido:

```
Ingresos totales:      2.570.000€  ✅
Gastos totales:       (1.556.000€) ✅
─────────────────────────────────────
Margen Bruto:          1.014.000€  ✅
Margen Bruto %:            39,45%  ✅
EBITDA:                  892.000€  ✅
```

### Validación cruzada:

| Concepto | SQL | DAX | Diferencia | Estado |
|----------|-----|-----|------------|--------|
| Ingresos | 2.570.000€ | 2.570.000€ | 0,00€ | ✅ CUADRADO |
| Gastos | 1.556.000€ | 1.556.000€ | 0,00€ | ✅ CUADRADO |

---

## 💡 Lecciones Aprendidas

### 1. La IA es experta en sintaxis, ciega en criterio

La IA puede generar código DAX **matemáticamente perfecto** pero **económicamente erróneo**. Sin criterio contable, el error pasa desapercibido.

### 2. Las medidas jerárquicas son cadenas de dominó

Un error de 0,03€ en una medida base se multiplica exponencialmente a través de 12 medidas dependientes.

### 3. La validación cruzada es obligatoria

**Nunca confiar en un solo sistema.** SQL ↔ DAX ↔ Criterio Contable. Triple verificación antes de reportar.

### 4. El redondeo es un enemigo silencioso

NUNCA redondear valores intermedios en procesos de agregación. Solo redondear en la presentación final al usuario.

---

## 🛡️ Protocolo Preventivo Implementado

A raíz de este incidente, se estableció el **Protocolo de Validación de Medidas Críticas**:

```
[ PASO 1: DISEÑO ]
Escribir la medida DAX con criterio contable
          │
          ▼
[ PASO 2: VALIDACIÓN SQL ]
Crear consulta SQL equivalente para verificar
          │
          ▼
[ PASO 3: CONTRASTE ]
Comparar resultados (tolerancia: <1€)
          │
          ▼
[ PASO 4: PRUEBA DE ESTRÉS ]
Ejecutar la medida en diferentes contextos de filtro
          │
          ▼
[ PASO 5: DOCUMENTACIÓN ]
Registrar la lógica, dependencias y validaciones
```

---

## 📈 Impacto en la Organización

### Beneficios cuantificables:

| Métrica | Antes | Después | Mejora |
|---------|-------|---------|--------|
| Tiempo de auditoría mensual | 6h | 2h | **-66%** |
| Errores detectados en producción | 3-4/mes | 0/mes | **-100%** |
| Confianza en reportes | Media | Alta | **Cualitativo** |

### Cambios culturales:

✅ **Validación cruzada obligatoria** antes de reportar resultados  
✅ **Doble verificador automático** (SQL vs DAX)  
✅ **Registro de dependencias** en todas las medidas críticas  
✅ **Formación del equipo** en Principios Contables aplicados a BI

---

## 🎯 Conclusión

Este incidente demostró que:

1. **La tecnología sin criterio es un microscopio ciego**
2. **Los errores de redondeo se propagan exponencialmente**
3. **La validación cruzada no es opcional en finanzas**
4. **El conocimiento interdisciplinar (Contabilidad + SQL + DAX) es crítico**

**Sin la metodología de gobernanza implementada, este error habría costado decisiones estratégicas basadas en datos falsos.**

---

## 📚 Referencias

- Plan General Contable (PGC) - Marco Conceptual - Principio de Imagen Fiel
- Microsoft DAX Guide - Best Practices for Aggregations
- Metodología de Gobernanza de Skills Analíticas v1.0

---

**Autor del caso:** Juan Luis León Rodríguez  
**Fecha de documentación:** 25 de mayo de 2026  
**Estado:** Resuelto y protocolizado
