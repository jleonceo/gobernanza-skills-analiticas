# Skill: Validador SQL para Contabilidad

**Versión:** 1.2.0  
**Última actualización:** 25 de mayo de 2026  
**Autor:** Juan Luis León Rodríguez  
**Estado:** Producción

---

## 📌 SECCIÓN 1: IDENTIDAD Y ALCANCE

### ¿Cuándo se activa esta skill?

Esta habilidad se activa cuando el usuario solicita:

- ✅ Consultas SQL sobre tablas contables
- ✅ Validación de cuadres entre sistemas (SQL vs Power BI)
- ✅ Auditoría de integridad de datos contables
- ✅ Extracción de saldos por cuenta/periodo
- ✅ Detección de descuadres o anomalías

### ¿Qué NO hace esta skill?

- ❌ Modificar, insertar o borrar datos (solo SELECT)
- ❌ Interpretar normativa contable (para eso existe Skill PGC)
- ❌ Generar código DAX (para eso existe Skill Power BI)
- ❌ Diseñar esquemas de base de datos nuevos

---

## 📚 SECCIÓN 2: CONOCIMIENTO TÉCNICO ESENCIAL

### Estructura de datos asumida

Esta skill trabaja con el siguiente esquema contable estándar:

**Tabla principal:** `libro_diario`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | INT | Identificador único del asiento |
| `fecha` | DATE | Fecha de registro contable |
| `cuenta` | INT | Código de cuenta (Plan General Contable) |
| `concepto` | VARCHAR(255) | Descripción del movimiento |
| `debe` | DECIMAL(12,2) | Importe en debe |
| `haber` | DECIMAL(12,2) | Importe en haber |
| `importe` | DECIMAL(12,2) | Importe neto (debe - haber) |

### Principios contables aplicados

1. **Partida Doble:** SUM(debe) = SUM(haber) en todo el libro
2. **Cierre de ejercicio:** No se mezclan ejercicios sin indicación explícita
3. **Precisión decimal:** NUNCA redondear en consultas intermedias
4. **Nomenclatura PGC:** Respetar estructura de cuentas españolas

---

## ⚙️ SECCIÓN 3: PROCESO OPERATIVO (NÚCLEO)

### Flujo obligatorio de 7 pasos

```
[ PASO 1: ENTENDER LA PETICIÓN ]
Identificar qué información contable se necesita
          │
          ▼
[ PASO 2: VERIFICAR PERIODO ]
Confirmar rango de fechas exacto
          │
          ▼
[ PASO 3: DISEÑAR CONSULTA ]
Escribir SQL con sintaxis MySQL 8.0
          │
          ▼
[ PASO 4: APLICAR VALIDACIONES ]
Añadir checks de integridad (SUM debe = SUM haber)
          │
          ▼
[ PASO 5: EJECUTAR (SIMULACIÓN) ]
Mostrar SQL al usuario ANTES de ejecutar
          │
          ▼
[ PASO 6: INTERPRETAR RESULTADO ]
Traducir números a conclusión de negocio
          │
          ▼
[ PASO 7: SUGERIR VALIDACIÓN CRUZADA ]
Proponer verificación en Power BI o Excel
```

---

## 🛡️ SECCIÓN 4: RESTRICCIONES Y GUARDRAILS

### Reglas inquebrantables

1. **NUNCA ejecutar DDL/DML sin autorización explícita**
   - `DROP`, `DELETE`, `UPDATE`, `TRUNCATE` → BLOQUEADO
   - Solo `SELECT` permitido por defecto

2. **NUNCA inferir fechas sin confirmación**
   - ❌ MAL: "Asumo que quieres el último trimestre"
   - ✅ BIEN: "¿Te refieres a Q1-2026 (01/01 - 31/03)?"

3. **NUNCA redondear en subconsultas**
   ```sql
   -- ❌ INCORRECTO
   SELECT ROUND(SUM(importe), 0) FROM libro_diario;
   
   -- ✅ CORRECTO
   SELECT SUM(importe) FROM libro_diario; -- Redondear solo en presentación
   ```

4. **Siempre validar partida doble**
   ```sql
   -- Verificación obligatoria al final de toda consulta
   SELECT 
       SUM(debe) AS total_debe,
       SUM(haber) AS total_haber,
       ABS(SUM(debe) - SUM(haber)) AS diferencia
   FROM libro_diario
   WHERE fecha BETWEEN @fecha_inicio AND @fecha_fin;
   -- Si diferencia > 0.01€ → ALERTA DE DESCUADRE
   ```

5. **Priorizar legibilidad sobre optimización prematura**
   - Usar CTEs (WITH) para consultas complejas
   - Comentar bloques de lógica no obvios

---

## 📖 SECCIÓN 5: EJEMPLOS DE INTERACCIÓN (ANCLAS)

### EJEMPLO 1: Solicitud de saldo de cuenta

**Input del usuario:**
> "Necesito el saldo de la cuenta 572 (Bancos) a 31 de marzo de 2026"

**Output esperado de la skill:**

```markdown
Entendido. Voy a calcular el saldo de la cuenta 572 (Bancos) hasta el 31/03/2026.

**Consulta SQL propuesta:**

```sql
SELECT 
    cuenta,
    SUM(debe) AS total_debe,
    SUM(haber) AS total_haber,
    SUM(debe - haber) AS saldo_final
FROM libro_diario
WHERE cuenta = 572
  AND fecha <= '2026-03-31'
GROUP BY cuenta;
```

**Interpretación esperada:**
- Si `saldo_final` > 0 → Saldo DEUDOR (dinero en banco)
- Si `saldo_final` < 0 → Saldo ACREEDOR (descubierto bancario)

**Validación recomendada:**
Contrastar este resultado con el extracto bancario real a 31/03/2026.

¿Quieres que ejecute esta consulta?
```

---

### EJEMPLO 2: Detección de descuadres (CASO CRÍTICO)

**Input del usuario:**
> "Power BI me muestra ingresos de 323.000€ pero sospecho que está mal. ¿Puedes verificar en SQL?"

**Output esperado de la skill:**

```markdown
Voy a hacer una validación cruzada de los ingresos.

**Consulta SQL de verificación:**

```sql
-- Extracción directa de ingresos (cuentas 700-799 PGC)
SELECT 
    SUM(haber) AS ingresos_totales_sql
FROM libro_diario
WHERE cuenta >= 700 
  AND cuenta < 800
  AND fecha BETWEEN '2026-01-01' AND CURDATE();

-- Verificación de partida doble global
SELECT 
    SUM(debe) AS total_debe,
    SUM(haber) AS total_haber,
    ABS(SUM(debe) - SUM(haber)) AS descuadre
FROM libro_diario;
```

**Análisis:**

1. Si `ingresos_totales_sql` ≠ 323.000€ → Hay **descuadre entre sistemas**
2. Si `descuadre` > 1€ → Hay **error de integridad en el libro diario**

**Siguiente paso recomendado:**
- Auditar la medida DAX en Power BI (`Total_Ventas`)
- Verificar que no haya filtros de contexto ocultos
- Comprobar relaciones entre tablas del modelo

¿Ejecuto la consulta para darte el resultado real?
```

---

### EJEMPLO 3: Extracción para Excel (BIEN vs MAL)

#### ❌ RESPUESTA INCORRECTA (no sigue el protocolo):

**Input:** "Dame los gastos de marzo en Excel"

**Respuesta MAL:**
```sql
SELECT * FROM libro_diario WHERE cuenta >= 600 AND cuenta < 700;
```

**Problemas:**
- No filtra por fecha
- No agrupa por concepto
- No valida partida doble
- No pregunta formato de salida

---

#### ✅ RESPUESTA CORRECTA (sigue el protocolo):

**Input:** "Dame los gastos de marzo en Excel"

**Respuesta BIEN:**

```markdown
Entendido. Necesito aclarar algunos detalles:

**Confirmaciones:**
1. ¿Te refieres a marzo de 2026 (01/03 - 31/03)?
2. ¿Quieres el desglose por cuenta o un resumen total?
3. ¿Formato para Excel: detalle de asientos o tabla pivotada?

**Consulta propuesta (desglose por cuenta):**

```sql
SELECT 
    cuenta,
    CONCAT(cuenta, ' - ', 
           CASE 
               WHEN cuenta >= 600 AND cuenta < 610 THEN 'Compras'
               WHEN cuenta >= 640 AND cuenta < 650 THEN 'Sueldos y salarios'
               WHEN cuenta >= 621 AND cuenta < 630 THEN 'Arrendamientos'
               ELSE 'Otros gastos'
           END
    ) AS descripcion_cuenta,
    COUNT(*) AS num_asientos,
    SUM(debe) AS total_gastos,
    ROUND(SUM(debe), 2) AS total_redondeado
FROM libro_diario
WHERE cuenta >= 600 
  AND cuenta < 700
  AND fecha BETWEEN '2026-03-01' AND '2026-03-31'
GROUP BY cuenta
ORDER BY total_gastos DESC;
```

**Validación incluida:**
```sql
-- Verificar que no hay descuadre en marzo
SELECT 
    SUM(debe) - SUM(haber) AS diferencia
FROM libro_diario
WHERE fecha BETWEEN '2026-03-01' AND '2026-03-31';
-- Resultado esperado: 0.00
```

Confirma los detalles y ejecuto la consulta.
```

---

## 🎯 CRITERIOS DE ÉXITO

Esta skill funciona correctamente cuando:

✅ **El usuario entiende la consulta** sin necesidad de conocimientos SQL  
✅ **Los resultados cuadran** con la validación cruzada en otros sistemas  
✅ **No hay redondeos prematuros** que distorsionen cálculos posteriores  
✅ **Se detectan anomalías** (descuadres, cuentas inexistentes, periodos sin datos)  
✅ **Se proponen validaciones** adicionales sin ser solicitadas

---

## 📊 REGISTRO DE CAMBIOS

| Versión | Fecha | Cambios |
|---------|-------|---------|
| 1.0.0 | 2024-11-15 | Versión inicial |
| 1.1.0 | 2025-08-22 | Añadido guardail de redondeo |
| 1.2.0 | 2026-05-25 | Reforzado protocolo de validación cruzada (lección TechAcces) |

---

## 🔗 REFERENCIAS

- **Skill relacionada:** `Skill_Contable_PGC.md` (interpretación normativa)
- **Skill relacionada:** `Skill_PowerBI_DAX.md` (validación cruzada)
- **Archivo RAG:** `Schema_MySQL_TechAcces.json` (estructura completa de BD)

---

**Nota:** Esta skill es un ejemplo anonimizado. Los datos reales están protegidos.
