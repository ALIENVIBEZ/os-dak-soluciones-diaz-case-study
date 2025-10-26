# 🤝 CONTRIBUTING - Contribuir al Case Study

**¿Adaptaste OS DAK a tu app? Compártelo con la comunidad.**

---

## 🎯 Formas de Contribuir

### 1. Compartir Tu Caso de Estudio

**¿Adaptaste OS DAK exitosamente?**

Comparte tu experiencia para que otros aprendan:

```yaml
Pasos:
  1. Fork este repositorio
  2. Crea directorio: case-studies/[tu-app-nombre]/
  3. Documenta tu adaptación:
     - blockchain-map.md (tu mapa adaptado)
     - decisions.md (tus decisiones específicas)
     - README.md (overview de tu app)
  4. Pull Request

Información a incluir:
  - Tipo de app (e-commerce, SaaS, CRM, etc.)
  - Stack tecnológico (Next.js, Vue, React, etc.)
  - Total URLs mapeadas
  - Decisiones clave tomadas
  - Tiempo real de adaptación
  - Lecciones aprendidas
```

**Ejemplo estructura:**
```
case-studies/
├── manager-battle-pro/          # Patricio's second app
│   ├── README.md
│   ├── blockchain-map.md
│   └── decisions.md
├── tu-app-ecommerce/            # Tu contribución
│   ├── README.md
│   ├── blockchain-map.md
│   └── decisions.md
└── [más casos...]
```

---

### 2. Mejorar Documentación Existente

**¿Encontraste algo confuso o incompleto?**

Mejóralo:

```yaml
Pasos:
  1. Abre Issue describiendo el problema
  2. Propón mejora específica
  3. Fork y edita el archivo
  4. Pull Request con cambios

Ejemplos de mejoras:
  - Clarificar sección confusa en ADAPTATION-GUIDE.md
  - Agregar ejemplo adicional en DECISIONS-LOG.md
  - Corregir typos o errores técnicos
  - Mejorar diagramas ASCII
```

---

### 3. Crear Templates Adicionales

**¿Usas stack diferente?**

Crea template específico:

```yaml
Templates útiles:
  - Vue Router (vs Next.js App Router)
  - React Router (vs Next.js)
  - Svelte Kit
  - Ruby on Rails
  - Django
  - Laravel

Ubicación: templates/[framework]/
Contenido: Adaptación específica de nodos-worksheet.md
```

**Ejemplo:**
```
templates/
├── next-app-router/       # Existente (Soluciones Díaz)
├── vue-router/            # Tu contribución
│   ├── nodos-worksheet.md
│   └── router-mapping.md
└── react-router/
    └── nodos-worksheet.md
```

---

### 4. Reportar Errores

**¿Encontraste un error?**

Repórtalo con detalles:

```yaml
Pasos:
  1. Abre Issue en GitHub
  2. Usa template de bug report
  3. Incluye:
     - Qué esperabas
     - Qué obtuviste
     - Pasos para reproducir
     - Screenshot si aplica

Etiquetas:
  - bug (error técnico)
  - documentation (error en docs)
  - enhancement (mejora sugerida)
```

---

### 5. Sugerir Mejoras

**¿Tienes idea para mejorar el sistema?**

Proponla:

```yaml
Pasos:
  1. Abre Issue en GitHub
  2. Usa template de feature request
  3. Explica:
     - Problema que resuelve
     - Solución propuesta
     - Alternativas consideradas
     - Impacto en usuarios

Ejemplos:
  - Generador automático de Blockchain Map
  - Validador de consistencia
  - CLI para scaffolding
  - Plugin VS Code
```

---

## 📝 Guidelines de Contribución

### Estilo de Documentación

```yaml
Formato:
  ✅ Markdown (.md files)
  ✅ Diagramas ASCII (no imágenes externas)
  ✅ Code blocks con syntax highlighting
  ✅ YAML para estructuras de datos
  ✅ Ejemplos concretos (código real > abstracto)

Tono:
  ✅ Directo y claro (como USER_PATTERNS.md describe)
  ✅ Acción sobre teoría
  ✅ "Haz X" > "Podrías considerar X"
  ✅ Sin jerga innecesaria

Estructura:
  ✅ Usar headers H2 ## y H3 ###
  ✅ Código en bloques con lenguaje especificado
  ✅ YAML para configs/decisiones
  ✅ Listas para pasos/checklist
```

### Commits

```yaml
Formato: [tipo]: [descripción]

Tipos:
  - feat: Nueva funcionalidad o documento
  - fix: Corrección de error
  - docs: Mejora documentación
  - refactor: Reorganización sin cambio funcional
  - chore: Mantenimiento (actualizar deps, etc)

Ejemplos:
  ✅ feat: Add Vue Router template to templates/
  ✅ docs: Clarify NÚMERO vs LETRA in ADAPTATION-GUIDE
  ✅ fix: Correct typo in DECISIONS-LOG decision 3
  ❌ updated stuff (muy vago)
  ❌ fixes (sin contexto)

Mensaje completo:
  - Línea 1: Título (max 72 caracteres)
  - Línea 2: Vacía
  - Línea 3+: Detalles (qué, por qué, impacto)
  - Footer: Co-Authored-By si aplica
```

### Pull Requests

```yaml
Antes de PR:
  ✅ Testear cambios localmente
  ✅ Verificar markdown render correctamente
  ✅ Revisar typos y gramática
  ✅ Asegurar consistencia con docs existentes

Título PR:
  [tipo]: [descripción clara]
  Ejemplo: feat: Add case study for Vue.js e-commerce app

Descripción PR:
  ## Qué cambios
  [Lista de archivos/secciones modificados]

  ## Por qué
  [Razón del cambio]

  ## Cómo testear
  [Pasos para validar cambios]

  ## Screenshots (si aplica)
  [Capturas de diagramas, etc]
```

---

## 🎓 Proceso de Review

```yaml
1. PR es creado
2. Maintainer revisa en 1-3 días
3. Feedback si necesario:
   - Clarificación
   - Mejoras sugeridas
   - Correcciones menores
4. Autor actualiza PR
5. Segunda review
6. Merge cuando aprobado

Criterios de aprobación:
  ✅ Documentación clara y útil
  ✅ Ejemplos concretos (no solo teoría)
  ✅ Consistente con estilo existente
  ✅ Sin errores técnicos
  ✅ Markdown render correctamente
```

---

## 🌟 Reconocimiento

**Contributors serán reconocidos en:**

- README.md (sección Contributors)
- CHANGELOG.md (cambios por versión)
- Agradecimiento especial en releases

**Tipos de contribución reconocidos:**
- 📚 Case studies completos
- 📝 Mejoras documentación
- 🛠️ Templates nuevos
- 🐛 Bug reports y fixes
- 💡 Feature requests implementadas

---

## 📚 Recursos para Contributors

### Leer Primero

```yaml
Obligatorio:
  - README.md (entender el proyecto)
  - ADAPTATION-GUIDE.md (proceso completo)
  - DECISIONS-LOG.md (decisiones tomadas)

Recomendado:
  - examples/blockchain-map-after.md (resultado esperado)
  - templates/skill-template.md (estructura completa)
```

### Herramientas Útiles

```yaml
Markdown:
  - Obsidian (editor con preview)
  - VS Code + Markdown Preview
  - StackEdit (online)

Diagramas ASCII:
  - asciiflow.com
  - Monodraw (Mac)
  - ASCII Tree Generator

Validación:
  - markdownlint (VS Code extension)
  - prettier (formatear markdown)
```

---

## ❓ Preguntas Frecuentes

### ¿Puedo contribuir si NO he adaptado OS DAK?

**SÍ.** Puedes:
- Mejorar documentación existente
- Reportar errores o confusiones
- Sugerir mejoras
- Crear templates para otros stacks

### ¿Cuánto tiempo toma un case study?

**1-2 horas** documentar caso después de adaptar.

Ya hiciste el trabajo duro (adaptar OS DAK).
Documentarlo es compartir lo que aprendiste.

### ¿Qué pasa si mi app es muy diferente?

**Perfecto.** Casos diversos son más valiosos.

- Soluciones Díaz: Next.js + Firebase + CRM
- Tu app: ¿Vue + Supabase + SaaS? → Excelente contraste
- Comunidad aprende de diferentes contextos

### ¿Puedo contribuir en español?

**SÍ.** Este proyecto acepta:
- Español (idioma original)
- Inglés (para alcance internacional)

Si contribuyes en inglés, agradecemos traducción posterior.

### ¿Y si no estoy seguro si mi mejora es útil?

**Abre Issue primero.**

Discute tu idea antes de invertir tiempo:
1. Abre Issue describiendo propuesta
2. Comunidad comenta utilidad
3. Si hay consenso, implementa
4. Pull Request

Evitas trabajo innecesario.

---

## 📞 Contacto

**¿Dudas sobre contribución?**

- GitHub Issues: Preguntas técnicas
- GitHub Discussions: Ideas y conversación
- Email: [patricio@solucionesdiaz.cl] (solo para consultas privadas)

---

## 📄 Licencia

Al contribuir, aceptas que tu trabajo será licenciado bajo MIT License (misma que el proyecto).

---

**Última actualización:** 2025-10-26
**Creado por:** Patricio Díaz + Claude
**Para:** Contributors del Case Study OS DAK

🌌 **"Comunidad aprende de casos reales, no solo teoría"**

🙏 **¡Gracias por contribuir!**
