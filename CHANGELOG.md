# Changelog

Todos los cambios notables en este proyecto serán documentados en este archivo.

El formato está basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/),
y este proyecto sigue [Versionado Semántico](https://semver.org/lang/es/).

---

## [1.0.0] - 2026-05-25

### 🎉 Lanzamiento Inicial

Primera versión pública del Framework de Gobernanza de Skills Analíticas.

### ✨ Añadido

#### Documentación Core
- **README.md**: Portada principal con caso TechAcces (error ×7,96), diagrama de arquitectura 3 niveles, ROI cuantificado y guías de uso por perfil
- **LICENSE**: Licencia Creative Commons BY-NC-SA 4.0
- **CONTRIBUTING.md**: Guía completa de colaboración
- **CHANGELOG.md**: Este archivo de registro de cambios

#### Arquitectura Central (`core-architecture/`)
- **orquestador_procesos_template.md**: Plantilla del Nivel 1 (Orquestador) con matriz de interacción Humano-IA
- **estructura_skills_template.md**: Molde estándar de 5 secciones obligatorias para diseño de skills
- **ejemplo_skill_sql.md**: Ejemplo funcional completo de skill de validación SQL para contabilidad

#### Protocolos de Governanza (`governance-protocols/`)
- **protocolo_decisiones_criticas.md**: Protocolo "Freno de Mano" de 5 pasos antes de acciones críticas sobre datos
- **matriz_validacion_cruzada.md**: Protocolo de verificación interdisciplinar SQL↔DAX↔Excel con 3 niveles de criticidad

#### Casos de Estudio (`case-studies/`)
- **techacces_error_arrastre_mayo2026.md**: Caso real completo documentando error de ×7,96 en Power BI (2,57M€), metodología de detección en 3h 40min, causa raíz (redondeo prematuro), solución implementada y lecciones aprendidas

### 🎯 Características Principales

- Arquitectura de 3 niveles (Orquestación → Skills → Referencias)
- ROI cuantificado: 44h/año recuperadas por analista
- Reducción 40-50% en consumo de recursos
- Protocolo de validación cruzada obligatoria
- Caso real que valida el framework empíricamente

### 📊 Métricas del Proyecto

- **Archivos de documentación**: 9
- **Ejemplos funcionales**: 1 skill completa (SQL)
- **Casos de uso documentados**: 1 (TechAcces)
- **Protocolos de seguridad**: 2

### 👥 Colaboradores

- **Autor y Mantenedor**: Juan Luis León Rodríguez ([@jleonceo](https://github.com/jleonceo))

---

## [Próximas Versiones]

### [1.1.0] - Planificado

#### En Desarrollo
- [ ] Ejemplo de skill DAX para Power BI
- [ ] Ejemplo de skill de interpretación contable (PGC)
- [ ] Traducción del README al inglés
- [ ] Diagrama visual profesional de la arquitectura (Mermaid/Excalidraw)

#### Propuestas de la Comunidad
- [ ] Integración con herramientas de CI/CD
- [ ] Templates de validación automatizada
- [ ] Biblioteca de casos de uso adicionales

*Si quieres contribuir a alguna de estas funcionalidades, consulta [CONTRIBUTING.md](CONTRIBUTING.md)*

---

## Formato de Cambios

Este changelog utiliza las siguientes categorías:

- **✨ Añadido**: Nuevas funcionalidades
- **🔧 Cambiado**: Cambios en funcionalidad existente
- **🐛 Corregido**: Corrección de bugs
- **🗑️ Eliminado**: Funcionalidades removidas
- **🔒 Seguridad**: Cambios relacionados con vulnerabilidades

---

## Contacto

Para reportar problemas o sugerir mejoras:

📩 **Email**: jleonceo@gmail.com  
💼 **LinkedIn**: [linkedin.com/in/jlleonrodriguez](https://www.linkedin.com/in/jlleonrodriguez/)  
🐙 **Issues**: [github.com/jleonceo/gobernanza-skills-analiticas/issues](https://github.com/jleonceo/gobernanza-skills-analiticas/issues)

---

**Convención de Versionado:**

- **MAJOR** (X.0.0): Cambios incompatibles con versiones anteriores
- **MINOR** (0.X.0): Nuevas funcionalidades compatibles con versiones anteriores
- **PATCH** (0.0.X): Correcciones de bugs compatibles con versiones anteriores
