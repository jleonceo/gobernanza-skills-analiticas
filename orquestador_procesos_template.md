# **Plantilla: Orquestador Dinámico de Procesos Analíticos**

Este documento contiene la estructura estéril para registrar y auditar el enrutamiento de datos entre operarios humanos, bases de datos y asistentes de IA.

## **1\. Ficha Técnica del Proceso**

* **ID del Proceso:** \[Ej. PR-CONT-001\]  
* **Área Funcional:** \[Ej. Control de Gestión / Finanzas\]  
* **Objetivo:** \[Descripción breve del flujo\]  
* **Frecuencia:** \[Mensual / Semanal / Tiempo Real\]

## **2\. Matriz de Interacción Humano-IA (HCI)**

Identificación exacta de qué actor ejecuta cada fase para evitar duplicidades o "amnesia de proceso".

| Fase | Tarea Específica | Actor (Humano / SQL / IA) | Entrada (Inputs) | Salida (Outputs) |
| :---- | :---- | :---- | :---- | :---- |
| **1** | Extracción de datos origen | \[Especificar actor\] | \[Tablas / Orígenes\] | \[CSV / DataFrame\] |
| **2** | Transformación y limpieza | \[Especificar actor\] | \[Datos en bruto\] | \[Modelo de Datos\] |
| **3** | Generación de Insights | \[Especificar actor\] | \[Métricas / DAX\] | \[Reporte Preliminar\] |
| **4** | Validación y Cierre | \[Especificar actor\] | \[Reporte Preliminar\] | \[Entregable Final\] |

## **3\. Logs de Ejecución e Histórico**

*Registro obligatorio para mantener la trazabilidad de cambios en la lógica del negocio.*

* **\[DD/MM/AAAA\]**: \[Descripción del cambio o ejecución \- Iniciales del Responsable\]