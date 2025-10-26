# BLOCKCHAIN VIVIENTE - Template Genérico (ANTES de Adaptar)

**Estado:** Template genérico del documento TRANSFERENCIA-SISTEMA-BLOCKCHAIN-VIVIENTE.md
**Fecha:** Pre-adaptación
**Para:** Referencia de comparación

---

## 📋 Características del Template Genérico

```yaml
Propósito:
  - Guía transferible a cualquier aplicación
  - Conceptos abstractos y generales
  - NO específico a ningún stack tecnológico

Conceptos clave:
  - CAPA 0: Guardian de Planes (pre-ejecución)
  - NÚMERO: Profundidad (niveles de URL)
  - LETRA: Branch evolutivo (A/B/C)
  - 6 Tipos de Skills (CONTEXTO, FLUJO, WiFi, CADENA, JOURNEY, CONVERGENCIA)

Ventajas:
  ✅ Aplicable a cualquier app
  ✅ Conceptos universales
  ✅ Estructura clara

Limitaciones:
  ⚠️  Requiere adaptación al contexto específico
  ⚠️  Decisiones clave NO tomadas (usuario debe decidir)
  ⚠️  Ejemplos genéricos (no código real)
```

---

## 🗺️ Diagrama Template Genérico

```
OS [TU APP] (Sistema Operativo de Desarrollo con IA)
Analogía: Blockchain Viviente

┌─────────────────────────────────────────────────────────────────┐
│  NÚMERO = PROFUNDIDAD (cuánto nesting)                          │
│  LETRA  = CONEXIÓN horizontal (branch evolutivo)                │
│  CAPA   = URL física única                                      │
└─────────────────────────────────────────────────────────────────┘

CAPA 0 - Guardian de Planes
├── 🛡️ Agente: plan-guardian
├── URL: N/A (pre-ejecución)
└── Función: Validar planes antes de ejecutar

CAPA 1 - [TU FUNCIÓN 1]
├── 1A (Admin): [URL]
│   └── 🔧 Agente: [nombre-agente]
└── 1B (Public): [URL]
    └── 🌐 Agente: [nombre-agente]

CAPA 2 - [TU FUNCIÓN 2]
├── 2A (Admin): [URL]
│   └── 📋 Agente: [nombre-agente]
└── 2B (Public): [URL]
    └── 👁️ Agente: [nombre-agente]

[... continuar con todas tus capas ...]
```

---

## 📝 Definiciones Template

### CAPA 0

```yaml
Nombre: Guardian de Planes
Función: Validar ANTES de ejecutar
URL: N/A (pre-ejecución)

Responsabilidades:
  - Validar claridad de plan
  - Detectar ambigüedades
  - Preguntar antes de proceder
  - Proteger contra ejecuciones innecesarias

Implementación:
  - Usar TodoWrite tool proactivamente
  - Crear plan antes de codificar
  - Validar con usuario ANTES de ejecutar
```

### NÚMERO

```yaml
Definición: Profundidad (cuánto nesting desde root)

Regla: Contar niveles desde root

Ejemplos:
  1: / (root)
  2: /products
  3: /products/[id]
  4: /products/[id]/reviews

Traducción:
  NÚMERO = cantidad de slashes en URL
```

### LETRA

```yaml
Definición: Branch evolutivo

Opciones sugeridas:
  LETRA A: Admin/Organizador
    - Control completo
    - Funcionalidad CRUD
    - Gestión y configuración

  LETRA B: Public/Usuario Final
    - Experiencia pública
    - Read-only o limitado
    - UI optimizada

  LETRA C: History/Auditoría (opcional)
    - Registro histórico
    - Analytics
    - Compliance
```

### SUB-NODOS

```yaml
Definición: NO muy clara en template

Menciona:
  "NO todo es nodo separado"
  "Tabs/secciones dentro de misma página"

Pero:
  - NO da notación específica
  - NO diferencia claramente NODO vs SUB-NODO
  - Usuario debe inferir
```

---

## 🎯 Ejemplo Genérico (E-commerce)

**Del template original:**

```yaml
Tu app de e-commerce:
  /                        # Landing
  /products               # Lista productos
  /product/[id]           # Detalle producto
  /admin/dashboard        # Panel admin
  /admin/products/create  # Crear producto
  /admin/orders           # Gestión pedidos
  /checkout               # Proceso compra

→ Tienes ~7 URLs = 7 posibles NODOS

Mapeo sugerido:
  1A: /admin/products/create (profundidad 1, admin)
  1B: /                       (profundidad 1, público)
  2A: /admin/products         (profundidad 2, admin)
  2B: /products               (profundidad 2, público)
  3A: /admin/dashboard        (profundidad 3, admin)
  3B: /product/[id]           (profundidad 3, público)
  4B: /checkout               (profundidad 4, público)
```

**Problemas del mapeo genérico:**

1. **Profundidad física NO refleja complejidad:**
   ```yaml
   /admin/products/create = profundidad 1 (según template)
   Pero /admin/products/create tiene 3 niveles de URL

   Confusión: ¿Contar slashes o función?
   ```

2. **LETRA A/B ambigua:**
   ```yaml
   Template sugiere:
     A = Admin
     B = Public

   Pero:
     ¿Qué si admin también navega /products?
     ¿Es A porque es admin o B porque es público?
   ```

3. **SUB-NODOS no especificados:**
   ```yaml
   Si /admin/dashboard tiene tabs:
     - Overview
     - Analytics
     - Settings

   ¿Cómo notarlos? Template no dice.
   ```

---

## 🔄 6 Tipos de Skills (Template)

### Tipo 1: CONTEXTO
```yaml
Cuándo usar: Documentar UN nodo específico
Tamaño: ~15-20KB
```

### Tipo 2: FLUJO
```yaml
Cuándo usar: A → B unidireccional
Tamaño: ~5-10KB
```

### Tipo 3: WiFi
```yaml
Cuándo usar: A ↔ B bidireccional real-time
Tamaño: ~10-15KB
```

### Tipo 4: CADENA
```yaml
Cuándo usar: A → B → C secuencial
Tamaño: ~15-20KB
```

### Tipo 5: JOURNEY
```yaml
Cuándo usar: Happy path usuario (onboarding)
Tamaño: ~10-15KB
```

### Tipo 6: CONVERGENCIA
```yaml
Cuándo usar: Multi-Write → Single-Read
Tamaño: ~20-30KB
```

**Nota:** Tipos bien definidos, aplicables sin cambios.

---

## ✅ Qué Funciona del Template

```yaml
Conceptos universales:
  ✅ NÚMERO+LETRA+CAPA es poderoso
  ✅ 6 Tipos de Skills son completos
  ✅ Estructura Blockchain es visualizable
  ✅ Recovery automático (Claude Code nativo)
  ✅ Load on demand (keywords activan skills)

Filosofía sólida:
  ✅ Memoria externa persistente (Git)
  ✅ Compensación ADHD (infraestructura predecible)
  ✅ Sistema auto-organizado
  ✅ De herramienta → ecosistema vivo
```

---

## ⚠️ Qué Requiere Adaptación

```yaml
Decisiones NO tomadas:
  ⚠️  CAPA 0: ¿Guardian Planes o algo específico tu app?
  ⚠️  NÚMERO: ¿Niveles físicos o profundidad funcional?
  ⚠️  LETRA: ¿A=Admin,B=Public o contexto de uso?
  ⚠️  SUB-NODOS: ¿Cómo notarlos exactamente?

Contexto faltante:
  ⚠️  Roles: ¿Tu app tiene roles? ¿Cómo mapearlos?
  ⚠️  Dispositivos: ¿Mobile/Desktop diferentes?
  ⚠️  Real-time: ¿WebSockets, Firebase, polling?
  ⚠️  Complejidad: ¿5 URLs o 50 URLs?

Ejemplos genéricos:
  ⚠️  E-commerce hipotético (no código real)
  ⚠️  Sin stack específico
  ⚠️  Sin decisiones arquitectónicas concretas
```

---

## 🎓 Lecciones del Template

### 1. Es una GUÍA, no un blueprint exacto

```yaml
Template provee:
  - Marco conceptual
  - Estructura base
  - Vocabulario compartido

Usuario debe:
  - Adaptar a su contexto
  - Tomar decisiones específicas
  - Documentar decisiones (DECISIONS-LOG)
```

### 2. Flexibilidad intencional

```yaml
Template NO dicta:
  - Stack tecnológico específico
  - Nomenclatura exacta de LETRA
  - Profundidad física vs funcional

Razón:
  Aplicable a Next.js, Vue, React, Svelte, etc.
  Cada app tiene contexto único
```

### 3. Fundamentos sólidos

```yaml
Lo que SÍ es universal:
  - Blockchain como metáfora visual
  - NÚMERO+LETRA para organizar
  - Skills para documentar transacciones
  - Recovery automático
  - Persistencia multi-capa

Esto NO cambia entre apps
```

---

## 📊 Comparación: Template vs Realidad

| Aspecto | Template Genérico | Aplicación Real |
|---------|-------------------|-----------------|
| CAPA 0 | Guardian de Planes | Depende (puede ser Roles) |
| NÚMERO | Niveles de URL | Puede ser funcional |
| LETRA | A=Admin, B=Public | Puede ser contexto uso |
| SUB-NODOS | Mencionados vagamente | Requiere notación específica |
| Ejemplos | E-commerce hipotético | Tu código real |
| Stack | Agnóstico | Next.js, Vue, etc. |
| Decisiones | Usuario debe tomar | Documentadas en DECISIONS-LOG |

---

## 🚀 Próximo Paso: Adaptación

**Este template es el PUNTO DE PARTIDA.**

Para adaptarlo a TU app:

1. **Lee** `ADAPTATION-GUIDE.md` (paso a paso)
2. **Usa** `templates/nodos-worksheet.md` (mapear URLs)
3. **Revisa** `DECISIONS-LOG.md` (decisiones Soluciones Díaz)
4. **Compara** `blockchain-map-after.md` (resultado adaptado)

**Resultado esperado:**
- Template adaptado a TU contexto
- Decisiones documentadas
- Sistema funcionando en 2-4 horas

---

**Creado:** 2025-10-26
**Autor:** Patricio Díaz + Claude
**Propósito:** Mostrar template ANTES de adaptación
**Comparar con:** `blockchain-map-after.md`

🌌 **"Template genérico → Adaptación específica = Sistema funcional"**
