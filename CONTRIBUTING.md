# Guía de Contribución

¡Gracias por tu interés en contribuir al Framework de Gobernanza de Skills Analíticas! 🎉

Este documento explica cómo puedes colaborar para mejorar este proyecto.

---

## 🎯 Formas de Contribuir

Puedes contribuir de varias maneras:

1. **Reportar errores o problemas** en la documentación
2. **Sugerir mejoras** al framework metodológico
3. **Compartir casos de uso reales** aplicando la metodología
4. **Añadir ejemplos de skills** adicionales (anonimizadas)
5. **Mejorar la documentación** (traducciones, claridad, ejemplos)
6. **Proponer nuevos protocolos** de governanza

---

## 🐛 Reportar Problemas

Si encuentras un error, inconsistencia o algo que no funciona como se describe:

1. Ve a la [pestaña Issues](https://github.com/jleonceo/gobernanza-skills-analiticas/issues)
2. Haz clic en **"New Issue"**
3. Describe el problema claramente:
   - ¿Qué esperabas que pasara?
   - ¿Qué pasó en realidad?
   - ¿En qué archivo/sección está el problema?

---

## 💡 Sugerir Mejoras

Para proponer nuevas funcionalidades o mejoras:

1. Abre un [Issue](https://github.com/jleonceo/gobernanza-skills-analiticas/issues) con la etiqueta `enhancement`
2. Explica:
   - ¿Qué problema resuelve tu propuesta?
   - ¿Cómo mejoraría el framework?
   - ¿Tienes algún ejemplo de implementación?

---

## 🔧 Contribuir con Código/Documentación

### Paso 1: Fork del repositorio

1. Haz clic en el botón **"Fork"** en la esquina superior derecha
2. Clona tu fork a tu máquina local:

```bash
git clone https://github.com/TU_USUARIO/gobernanza-skills-analiticas.git
cd gobernanza-skills-analiticas
```

### Paso 2: Crear una rama para tu contribución

```bash
git checkout -b feature/nombre-descriptivo
```

Ejemplos de nombres de rama:
- `feature/skill-python-ejemplo`
- `fix/typo-readme`
- `docs/traduccion-ingles`

### Paso 3: Hacer tus cambios

- **Documentación:** Sigue el formato Markdown existente
- **Nuevas skills:** Usa la plantilla de `core-architecture/estructura_skills_template.md`
- **Casos de uso:** Asegúrate de **anonimizar** todos los datos sensibles

### Paso 4: Commit de tus cambios

```bash
git add .
git commit -m "Añadido: [descripción breve de tu contribución]"
```

Ejemplos de mensajes de commit:
- `Añadido: ejemplo de skill para análisis Python`
- `Corregido: typo en README sección de ROI`
- `Mejorado: protocolo de validación cruzada con ejemplo Excel`

### Paso 5: Push a tu fork

```bash
git push origin feature/nombre-descriptivo
```

### Paso 6: Crear Pull Request

1. Ve a tu fork en GitHub
2. Haz clic en **"Pull Request"**
3. Describe claramente qué aporta tu contribución
4. Espera revisión del mantenedor

---

## 📋 Estándares de Calidad

Para mantener la coherencia del proyecto, por favor sigue estos estándares:

### Documentación (Markdown)

- ✅ Usa títulos jerárquicos (`#`, `##`, `###`)
- ✅ Incluye ejemplos prácticos siempre que sea posible
- ✅ Usa tablas para comparaciones
- ✅ Usa bloques de código con sintaxis resaltada (```sql, ```dax, ```python)
- ✅ Revisa ortografía y gramática

### Nuevas Skills

Toda skill debe incluir obligatoriamente las **5 secciones**:

1. **Identidad y Alcance** (cuándo se activa, qué NO hace)
2. **Conocimiento Técnico Esencial** (sintaxis básica)
3. **Proceso Operativo** (flujo obligatorio paso a paso)
4. **Restricciones y Guardrails** (reglas inquebrantables)
5. **Ejemplos de Interacción** (casos BIEN vs MAL)

### Casos de Estudio

Si compartes un caso real:

- ✅ **ANONIMIZA** todos los datos sensibles (nombres, empresas reales, CIFs, etc.)
- ✅ Usa nombres ficticios tipo "Empresa X", "Cliente A"
- ✅ Redondea números financieros para evitar trazabilidad
- ✅ Documenta el problema, la solución y las lecciones aprendidas

---

## 🚫 Qué NO Aceptamos

- ❌ Código ejecutable malicioso
- ❌ Datos personales o confidenciales sin anonimizar
- ❌ Contenido con licencias incompatibles con CC BY-NC-SA 4.0
- ❌ Spam o contenido no relacionado con el framework
- ❌ Contribuciones que violen derechos de terceros

---

## 📬 Contacto del Mantenedor

Para dudas sobre cómo contribuir o proponer mejoras al framework:

**Juan Luis León Rodríguez**

📩 **Email:** jleonceo@gmail.com  
💼 **LinkedIn:** [linkedin.com/in/jlleonrodriguez](https://www.linkedin.com/in/jlleonrodriguez/)  
🐙 **GitHub:** [@jleonceo](https://github.com/jleonceo)

También puedes abrir un [Issue](https://github.com/jleonceo/gobernanza-skills-analiticas/issues) para consultas generales.

---

## 🎓 Código de Conducta

Este proyecto sigue un código de conducta simple:

- ✅ Sé respetuoso y constructivo
- ✅ Acepta feedback de forma profesional
- ✅ Enfócate en el contenido, no en las personas
- ✅ Asume buenas intenciones en los demás colaboradores

---

## 📄 Licencia

Al contribuir a este proyecto, aceptas que tus contribuciones se licencien bajo la misma licencia que el proyecto: [CC BY-NC-SA 4.0](LICENSE).

---

## 🙏 Reconocimientos

Todos los colaboradores serán reconocidos en el archivo `CHANGELOG.md` con cada nueva versión del framework.

---

**¡Gracias por ayudar a mejorar la gobernanza de IA en entornos analíticos!** 🚀
