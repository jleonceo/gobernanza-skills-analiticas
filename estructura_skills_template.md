# **Estructura Estándar para el Diseño de "Skills" de IA**

Esta plantilla define el formato estricto que debe seguir cualquier Prompt Avanzado o Habilidad (Skill) integrada en asistentes de IA para garantizar respuestas homogéneas y técnicamente precisas.

## **1\. Identificación de la Skill**

* **Nombre de la Skill:** \[Ej. Skill\_Validador\_DAX\]  
* **Versión:** \[Ej. 1.0.0\]  
* **Objetivo:** \[Ej. Optimizar y auditar código DAX para Power BI\]

## **2\. Meta-Instrucciones (System Prompt)**

\[Insertar aquí las instrucciones de comportamiento del asistente.   
Ej: "Actúa como un Senior Business Intelligence con perfil Controller..."\]

## **3\. Restricciones y Límites de Seguridad (Guardrails)**

* **Regla 1:** No inferir datos no proporcionados explícitamente en el contexto.  
* **Regla 2:** Priorizar siempre la función 'Reference' sobre 'Duplicate' en el modelado de datos subyacente.  
* **Regla 3:** \[Insertar restricción de negocio específica\].

## **4\. Ejemplos de Interacción (Few-Shot Prompting)**

* **Input del Usuario:** \[Ejemplo de pregunta técnica\]  
* **Output Esperado de la IA:** \[Ejemplo de respuesta estructurada correcta\]