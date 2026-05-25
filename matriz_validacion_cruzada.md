# Matriz de Validación Cruzada

## 🎯 Objetivo

Este documento define el **protocolo obligatorio de verificación interdisciplinar** para asegurar que los resultados de un sistema (SQL, Power BI, Excel) sean consistentes con los otros antes de reportar conclusiones a la dirección.

---

## 🏗️ Principio Fundamental

> **"Ningún dato financiero crítico debe confiarse a un único sistema. La validación cruzada no es opcional, es obligatoria."**

---

## 📊 Matriz de Dependencias Interdisciplinares

### Tabla de Verificación por Tipo de Dato

| Dato a Verificar | Sistema Primario | Sistema de Contraste | Criterio de Cuadre | Acción si descuadra |
|------------------|------------------|----------------------|-------------------|---------------------|
| **Ingresos totales** | Power BI (DAX) | MySQL (SELECT SUM) | Diferencia < 1€ | Auditar medida DAX + revisar filtros |
| **Gastos totales** | Power BI (DAX) | MySQL (SELECT SUM) | Diferencia < 1€ | Revisar contextos de filtro en modelo |
| **Saldo bancario** | MySQL | Extracto bancario real | Diferencia = 0€ | Revisar asientos no contabilizados |
| **EBITDA** | Power BI (DAX) | Excel (fórmulas) | Diferencia < 10€ | Verificar definición de EBITDA usada |
| **Número de clientes activos** | CRM | Power BI | Diferencia = 0 | Revisar filtro de estado "activo" |
| **Partida doble** | MySQL (libro_diario) | Control interno | SUM(debe) = SUM(haber) | Localizar asiento descuadrado |

---

## 🔍 Protocolo de Validación por Nivel de Criticidad

### NIVEL 1: DATOS CRÍTICOS (Tolerancia 0€)

**Aplicable a:**
- Saldos bancarios
- Cuentas de balance (patrimonio neto, pasivos)
- Partida doble contable

**Protocolo:**
```
[ EXTRACCIÓN PRIMARIA ]
Obtener dato del sistema de origen
          │
          ▼
[ EXTRACCIÓN SECUNDARIA ]
Obtener mismo dato de sistema alternativo
          │
          ▼
[ COMPARACIÓN EXACTA ]
diferencia = ABS(valor1 - valor2)
          │
          ▼
[ DECISIÓN ]
Si diferencia = 0€ → ✅ APROBADO
Si diferencia > 0€ → ❌ BLOQUEADO → Investigar
```

---

### NIVEL 2: DATOS DE GESTIÓN (Tolerancia ≤1€)

**Aplicable a:**
- Ingresos del periodo
- Gastos del periodo
- Márgenes calculados

**Protocolo:**
```
[ EXTRACCIÓN DUAL ]
Sistema A (Power BI) + Sistema B (SQL)
          │
          ▼
[ EVALUACIÓN DE DISCREPANCIA ]
diferencia = ABS(valor_A - valor_B)
          │
          ▼
[ DECISIÓN ]
Si diferencia ≤ 1€ → ✅ APROBADO (error de redondeo aceptable)
Si diferencia > 1€ → ⚠️ REVISAR URGENTE
Si diferencia > 100€ → 🚨 BLOQUEO TOTAL
```

---

### NIVEL 3: DATOS ANALÍTICOS (Tolerancia ≤2%)

**Aplicable a:**
- KPIs de rendimiento
- Ratios financieros
- Métricas de negocio no monetarias

**Protocolo:**
```
[ CÁLCULO EN MÚLTIPLES SISTEMAS ]
Sistema A + Sistema B + (Opcional: Excel manual)
          │
          ▼
[ ANÁLISIS DE VARIANZA ]
varianza_porcentual = ABS((A - B) / B) * 100
          │
          ▼
[ DECISIÓN ]
Si varianza ≤ 2% → ✅ ACEPTABLE
Si varianza > 2% → ⚠️ INVESTIGAR METODOLOGÍA
```

---

## 🛠️ Casos de Uso Prácticos

### CASO 1: Validación de Ingresos Mensuales

**Contexto:** Cerrar el mes de abril 2026 y reportar ingresos a dirección.

**Paso 1 - Extracción Power BI:**
```dax
Total_Ingresos_Abril = 
CALCULATE(
    SUM(libro_diario[haber]),
    libro_diario[cuenta] >= 700,
    libro_diario[cuenta] < 800,
    libro_diario[fecha] >= DATE(2026,4,1),
    libro_diario[fecha] <= DATE(2026,4,30)
)
```
**Resultado:** 245.780€

**Paso 2 - Validación SQL:**
```sql
SELECT SUM(haber) AS ingresos_sql
FROM libro_diario
WHERE cuenta >= 700 
  AND cuenta < 800
  AND fecha BETWEEN '2026-04-01' AND '2026-04-30';
```
**Resultado:** 245.780€

**Paso 3 - Evaluación:**
```
diferencia = |245.780 - 245.780| = 0€
✅ VALIDACIÓN APROBADA
```

**Conclusión:** El dato es fiable. Puede reportarse a dirección.

---

### CASO 2: Descuadre Detectado en EBITDA

**Contexto:** Revisión trimestral Q1-2026.

**Paso 1 - Extracción Power BI:**
```dax
EBITDA_Q1 = [Ingresos] - [Gastos_Operativos] - [Amortizaciones]
```
**Resultado:** 128.500€

**Paso 2 - Validación Excel manual:**
```
= SUMA(ingresos_Q1) - SUMA(gastos_operativos_Q1) - SUMA(amortizaciones_Q1)
```
**Resultado:** 132.200€

**Paso 3 - Evaluación:**
```
diferencia = |128.500 - 132.200| = 3.700€
diferencia > 100€ → 🚨 BLOQUEO TOTAL
```

**Paso 4 - Investigación obligatoria:**
1. Revisar definición de "Gastos Operativos" en cada sistema
2. Verificar que ambos excluyen gastos financieros
3. Comprobar que las amortizaciones se calcen exactamente

**Paso 5 - Causa raíz encontrada:**
Power BI estaba incluyendo "Gastos de personal eventual" (cuenta 641) como gasto operativo, mientras que Excel los excluía.

**Paso 6 - Corrección:**
Estandarizar definición de EBITDA en ambos sistemas.

**Paso 7 - Re-validación:**
```
Power BI: 132.200€
Excel:    132.200€
diferencia = 0€ → ✅ APROBADO
```

---

## 📋 Checklist de Validación Pre-Reporte

Antes de enviar cualquier informe a dirección, verificar:

- [ ] **¿Los datos han sido validados en al menos 2 sistemas diferentes?**
- [ ] **¿La diferencia está dentro de la tolerancia permitida?**
- [ ] **¿Se ha verificado la partida doble en el periodo analizado?**
- [ ] **¿Los filtros de contexto (fechas, cuentas) son idénticos en ambos sistemas?**
- [ ] **¿Se han documentado las discrepancias encontradas (si las hay)?**
- [ ] **¿Se ha obtenido aprobación del Controller/Responsable si hay descuadres?**

---

## 🔴 Situaciones de Bloqueo Inmediato

**Se debe detener el reporte y escalar a superior jerárquico si:**

1. **Descuadre en partida doble** > 0,01€
2. **Diferencia en saldos bancarios** > 0€
3. **Variación en ingresos/gastos** > 1% sin justificación documentada
4. **Imposibilidad de validar** por falta de acceso a sistema secundario

---

## 🎯 Responsabilidades

| Rol | Responsabilidad |
|-----|-----------------|
| **Analista de Datos** | Ejecutar validación cruzada en todos los informes críticos |
| **Controller Financiero** | Aprobar informes con descuadres documentados (si son < tolerancia) |
| **Director Financiero** | Establecer niveles de tolerancia según criticidad del dato |
| **IT/Sistemas** | Garantizar acceso a sistemas de validación (SQL, Power BI, Excel) |

---

## 📊 Registro de Validaciones

**Formato obligatorio de documentación:**

```markdown
## Validación: [Nombre del reporte]
**Fecha:** DD/MM/AAAA
**Analista:** [Nombre]
**Periodo:** [Mes/Trimestre/Año]

### Datos validados:
| Concepto | Sistema A | Sistema B | Diferencia | Estado |
|----------|-----------|-----------|------------|--------|
| Ingresos | 245.780€ | 245.780€ | 0€ | ✅ |
| Gastos   | 156.200€ | 156.201€ | 1€ | ✅ |
| EBITDA   | 89.580€  | 89.579€  | 1€ | ✅ |

### Conclusión:
Validación aprobada. Todas las diferencias < 1€.

### Aprobado por:
[Firma del Controller]
```

---

## 🔗 Referencias

- **Skill relacionada:** `ejemplo_skill_sql.md`
- **Skill relacionada:** `estructura_skills_template.md`
- **Caso de estudio:** `caso_techacces_mayo2026.md` (error ×7,96 detectado por validación cruzada)

---

**Versión:** 1.0  
**Fecha:** 25 de mayo de 2026  
**Autor:** Juan Luis León Rodríguez
