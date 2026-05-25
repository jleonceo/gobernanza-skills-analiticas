# **Protocolo de Decisión ante Acciones Críticas (Mitigación de Riesgos)**

Este protocolo establece el flujo obligatorio de "Freno de Mano Operativo" antes de proceder a la eliminación, modificación masiva o archivado de datos históricos de auditoría o bases de datos productivas.

## **1\. Evaluación del Impacto y Riesgo**

Antes de ejecutar cualquier acción de borrado o reestructuración recomendada por un asistente automatizado o solicitada por un proceso, evalúe:

* **¿Afecta a datos históricos de auditoría?** (Sí / No)  
* **¿Existe una copia de seguridad física e independiente fuera del entorno de producción?** (Sí / No)  
* **¿Qué reportes (Power BI, Excel, ETLs) dependen directamente de este origen?**

## **2\. El Freno de Mano Operativo (Flujo de 5 Pasos)**

\[ PASO 1: INVENTARIO \]   
Identificar y listar los archivos y tablas afectadas detallando rangos de fechas.  
          │  
          ▼  
\[ PASO 2: MATRIZ DE EVIDENCIA \]  
Escribir una justificación analítica explícita de por qué el dato ya no es necesario.  
          │  
          ▼  
\[ PASO 3: VERIFICACIÓN CRUZADA \]  
Buscar duplicidades del mismo histórico en repositorios locales o backups antiguos.  
          │  
          ▼  
\[ PASO 4: ANÁLISIS DE DEPENDENCIAS \]  
Comprobar que ningún informe activo en producción consuma este recurso.  
          │  
          ▼  
\[ PASO 5: VALIDACIÓN HUMANA OBLIGATORIA \]  
Firma y aprobación por escrito del Controller o Responsable del área.

## **3\. Registro de Firmas y Autorizaciones**

* **Solicitante de la Acción:** \[Nombre y Cargo\]  
* **Aprobador del Protocolo:** \[Nombre y Cargo del Responsable\]  
* **Fecha de Aplicación:** \[DD/MM/AAAA\]  
* **Estado:** \[Pendiente / Aprobado / Rechazado\]