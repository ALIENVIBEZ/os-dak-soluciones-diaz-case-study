# 🚀 GU Í A DE TRANSFERENCIA: Sistema Blockchain Viviente

**Para desarrolladores que quieren replicar OS DAK en su propia aplicación**

---

## 📋 METADATA

```yaml
Propósito: Transferir Sistema OS Blockchain Viviente a otra app
Público objetivo: Desarrollador con Claude Code CLI
Tiempo estimado: 2-4 horas (setup inicial)
Prerequisitos:
  - Claude Code CLI instalado
  - Git configurado
  - Aplicación Next.js/React (o similar con routing)
Resultado: Sistema operativo completo funcionando
```

---

## 🎯 QUÉ VAS A REPLICAR

**Sistema Operativo DAK** - No solo configuración, sino ecosistema completo:

```yaml
Componentes del sistema:
  ✅ Blockchain Viviente (arquitectura de nodos)
  ✅ 6 Tipos de Skills (transacciones documentadas)
  ✅ Agents especializados (smart contracts vivos)
  ✅ Persistencia multi-capa (memoria externa)
  ✅ Recovery automático (0 pérdida)
  ✅ Load on demand (boot 2 segundos)
  ✅ Compensación ADHD nativa

Resultado final:
  De herramienta (Claude Code) → Sistema Operativo completo
  De stateless → Stateful con memoria persistente
  De manual → Auto-organizado y auto-recuperable
```

---

## 📦 PASO 1: PREPARAR ESTRUCTURA BASE

### 1.1 Crear Estructura de Directorios

```bash
# En el root de tu proyecto:

mkdir -p .claude
mkdir -p .claude/skills
mkdir -p .claude/agents
mkdir -p .claude/sessions
mkdir -p .claude/knowledge

# Estructura final:
# .claude/
#   ├── skills/          # Skills (transacciones)
#   ├── agents/          # Agents (ejecutores)
#   ├── sessions/        # Historial conversaciones
#   ├── knowledge/       # Auto-captured patterns
#   ├── CLAUDE.md        # Kernel del OS
#   └── OS-LAYER-*.md    # Documentación arquitectónica
```

### 1.2 Copiar Templates Base

Descarga templates desde: https://github.com/Patodak/os-dak-framework

```bash
# Archivos críticos a copiar:
✅ CLAUDE.md.template → tu-proyecto/CLAUDE.md
✅ OS-LAYER-ARCHITECTURE-RULES.md → .claude/
✅ OS-LAYER-USER-GUIDE.md → .claude/
✅ OS-LAYER-INSTALLATION.md → .claude/
✅ PHILOSOPHY.md → .claude/ (opcional, para entender visión)
```

**Personaliza CLAUDE.md**:
- Cambia nombre proyecto
- Actualiza stack tecnológico
- Ajusta comandos (puerto, scripts)
- Mantén estructura de secciones

---

## 🗺️ PASO 2: MAPEAR TU APLICACIÓN (NODOS)

### 2.1 Identificar Páginas/Vistas (URLs)

**Pregunta clave**: ¿Qué páginas tiene mi aplicación?

```yaml
Ejemplo genérico:

Tu app de e-commerce:
  /                        # Landing
  /products               # Lista productos
  /product/[id]           # Detalle producto
  /admin/dashboard        # Panel admin
  /admin/products/create  # Crear producto
  /admin/orders           # Gestión pedidos
  /checkout               # Proceso compra

→ Tienes ~7 URLs = 7 posibles NODOS
```

### 2.2 Asignar NÚMERO (Profundidad)

**REGLA**: Contar niveles desde root.

```yaml
Ejemplo aplicado:

Profundidad 1 (crear/inicializar):
  1A: /admin/products/create
  1B: /                    # Landing público

Profundidad 2 (listas/selección):
  2A: /admin/products
  2B: /products           # Lista pública

Profundidad 3 (control/detalle):
  3A: /admin/dashboard
  3B: /product/[id]       # Detalle público

Profundidad 4 (proyección/live):
  4B: /checkout           # Proceso especial
```

**Por Qué Funciona**:
- Mapeable directamente a routing (Next.js, React Router, etc.)
- Predecible y escalable
- Fácil debug (sabes exactamente dónde estás)

### 2.3 Asignar LETRA (Branch)

**REGLA**: Letras = roles/funcionalidad evolutiva.

```yaml
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

### 2.4 Identificar CAPA 0 (Pre-Ejecución)

```yaml
CAPA 0: Guardian de Planes
  Función: Validar ANTES de ejecutar
  NO tiene URL (pre-ejecución)

  Responsabilidades:
    - Validar claridad de plan
    - Detectar ambigüedades
    - Preguntar antes de proceder
    - Proteger contra ejecuciones innecesarias

Implementación:
  Usar TodoWrite tool proactivamente
  Crear plan antes de codificar
  Validar con usuario ANTES de ejecutar
```

### 2.5 Identificar SUB-NODOS (si aplica)

**REGLA CRÍTICA**: NO todo es nodo separado.

```yaml
SUB-NODO (misma URL):
  Tabs/secciones dentro de misma página
  React state para cambiar
  NO requiere navegación

  Ejemplo Dashboard:
    3A: /admin/dashboard/[id]
      SUB-NODOS:
        - Overview (resumen)
        - Settings (config)
        - Analytics (métricas)
        - Users (gestión usuarios)

    Todos comparten: /admin/dashboard/[id]
    Notación: 3A.Overview, 3A.Settings, etc.

NODOS SEPARADOS (URLs diferentes):
  Páginas completamente diferentes
  Requieren navegación
  Comunicación vía API/Firebase/etc.

  Ejemplo:
    3A: /admin/dashboard
    4B: /checkout

    Notación: 3A → 4B
```

### 2.6 Crear Diagrama ASCII

**Template base**:

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

Guarda esto como: `.claude/skills/blockchain-viviente-visual-map/SKILL.md`

---

## 📝 PASO 3: DOCUMENTAR SKILLS (TRANSACCIONES)

### 3.1 Los 6 Tipos de Skills

**Tipo 1: CONTEXTO** (Documentación de 1 nodo)
```yaml
Cuándo usar: Documentar UN nodo específico
Tamaño: ~15-20KB
Contiene:
  - Componentes del nodo
  - Collections Firebase/DB que usa
  - Transformaciones de data
  - Roles y permisos
  - Límites y validaciones

Ejemplo: dashboard-admin.md
  Documenta TODO sobre nodo 3A Dashboard Admin
```

**Tipo 2: FLUJO** (A → B unidireccional)
```yaml
Cuándo usar: Info fluye de A a B (no viceversa)
Tamaño: ~5-10KB
Patrón: ORIGEN → DESTINO

Ejemplo: product-create-to-list.md
  1A (crear producto) → 2A (lista productos)
  Usuario crea → Sistema muestra en lista
```

**Tipo 3: WiFi** (A ↔ B bidireccional real-time)
```yaml
Cuándo usar: Sincronización LIVE entre 2 nodos
Tamaño: ~10-15KB
Patrón: NODO_A ↔ NODO_B

Ejemplo: admin-dashboard-sync-public-view.md
  3A (Admin Dashboard) ↔ 3B (Public View)
  Admin actualiza → Público ve en <1 segundo
  Requiere: Firebase/WebSockets/etc.
```

**Tipo 4: CADENA** (A → B → C secuencial)
```yaml
Cuándo usar: Transformaciones multi-nodo
Tamaño: ~15-20KB
Patrón: NODO_A → NODO_B → NODO_C

Ejemplo: checkout-flow-chain.md
  2B (Carrito) → 4B (Checkout) → Confirmación
  Cada nodo prepara data para siguiente
```

**Tipo 5: JOURNEY** (Happy path usuario)
```yaml
Cuándo usar: Primer camino del usuario (onboarding)
Tamaño: ~10-15KB
Patrón: ENTRADA → DESTINO (directo, optimizado UX)

Ejemplo: first-product-creation-journey.md
  1A (crear producto) → 3A (dashboard)
  Salta 2A (lista) para facilitar primer uso
  Usuario puede volver después
```

**Tipo 6: CONVERGENCIA** (Multi-Write → Single-Read)
```yaml
Cuándo usar: Múltiples fuentes convergen en 1 receptor
Tamaño: ~20-30KB
Patrón: [SUB-A + SUB-B] → NODO_C

Ejemplo: analytics-convergence.md
  Fuentes:
    - 3A.Settings (SUB-NODO: config)
    - 3A.Users (SUB-NODO: actividad usuarios)
  Destino:
    - 3A.Analytics (SUB-NODO read-only)

  Receptor lee de múltiples fuentes → Muestra dashboard unificado
```

### 3.2 Crear Tu Primera Skill

**Template básico**:

```markdown
# [NOMBRE DESCRIPTIVO]

**Tipo**: Skill Tipo [1-6] - [NOMBRE TIPO]
**Nodo(s)**: [Identificación nodos]
**URL(s)**: [URLs involucradas]

---

## 🎯 Propósito

[Describe qué documenta esta skill]

---

## 🔗 Flujo de Información

[Diagrama o descripción del flujo]

---

## 📊 Schema de Datos

[Qué collections/tablas/endpoints usa]

---

## 🔥 Firestore/DB Schema

[Estructura de datos específica]

---

## 🎯 Agents Especializados

[Qué agents podrían ayudar con este flujo]

---

**Última actualización**: [fecha]
**Creado por**: [tu nombre] + Claude
**Keywords**: [palabras clave para auto-activación]
```

Guarda en: `.claude/skills/[nombre-descriptivo].md`

---

## 🤖 PASO 4: CONFIGURAR AGENTS (OPCIONAL)

### 4.1 Agents Básicos Recomendados

```yaml
Agents mínimos para empezar:
  ✅ plan-guardian (CAPA 0)
  ✅ [tu-app]-expert (contexto general)
  ✅ database-expert (si usas BD)
  ✅ api-expert (si tienes APIs)
```

### 4.2 Template de Agent

Crear en: `.claude/agents/[nombre-agent].md`

```markdown
# [NOMBRE AGENT]

**Type**: [General-purpose/Specialized]
**Auto-activation**: [keywords]

---

## Role

[Qué hace este agent]

---

## Capabilities

- [Capacidad 1]
- [Capacidad 2]

---

## When to Use

[Cuándo delegar a este agent]

---

## Tools Available

- [Tool 1]
- [Tool 2]
```

---

## 💾 PASO 5: CONFIGURAR PERSISTENCIA

### 5.1 Git como Memoria Externa

```bash
# Inicializar si no está
git init

# Crear .gitignore
cat > .gitignore <<EOF
node_modules/
.env
.env.local
*.log
dist/
build/

# Pero NO ignores .claude/
# .claude/ debe estar en Git
EOF

# Commit base (checkpoint inicial)
git add .
git commit -m "🚀 COMMIT BASE: Sistema OS DAK inicializado"
```

**IMPORTANTE**: Commits frecuentes = recovery puntos.

### 5.2 Sessions JSONL (Auto-generado por Claude Code)

```yaml
Ubicación: .claude/sessions/

Auto-captura:
  - Cada conversación con Claude
  - Comandos ejecutados
  - Archivos modificados
  - Resultados de tools

NO requiere configuración manual
```

### 5.3 Recovery Automático

Si Claude Code crashea:

```bash
# 1. Reabrir Claude Code
# 2. Sistema detecta crash
# 3. Ofrece recovery automático
# 4. Acepta recovery
# 5. Contexto restaurado completo
```

---

## 🚀 PASO 6: BOOT Y TESTING

### 6.1 Verificar Boot

```bash
# Abrir Claude Code en tu proyecto
claude

# Debería:
✅ Cargar CLAUDE.md (kernel)
✅ Mostrar Working Memory (<70KB inicial)
✅ Boot en ~2 segundos
✅ Skills disponibles bajo demanda
```

### 6.2 Testing Load on Demand

```yaml
Test 1: Menciona keyword de skill
  Usuario: "Necesito ayuda con [keyword de tu skill]"
  Esperado: Claude carga skill automáticamente

Test 2: Delegación a Agent
  Usuario: "Usa el agente [nombre-agent] para..."
  Esperado: Claude delega correctamente

Test 3: Recovery post-crash
  1. Crear cambios importantes
  2. Git commit
  3. Simular crash (cerrar forzado)
  4. Reabrir Claude Code
  5. Sistema ofrece recovery
  Esperado: Contexto restaurado
```

### 6.3 Validar Estructura

```bash
# Tu proyecto debería verse así:

tu-proyecto/
├── .claude/
│   ├── skills/
│   │   └── blockchain-viviente-visual-map/SKILL.md
│   │   └── [tus-skills].md
│   ├── agents/
│   │   └── [tus-agents].md
│   ├── sessions/
│   │   └── [auto-generadas por Claude Code]
│   ├── knowledge/
│   │   └── [auto-captured patterns]
│   ├── OS-LAYER-ARCHITECTURE-RULES.md
│   ├── OS-LAYER-USER-GUIDE.md
│   └── OS-LAYER-INSTALLATION.md
├── CLAUDE.md
├── [tu código existente...]
└── .gitignore
```

---

## 🎯 PASO 7: PRIMERAS SKILLS RECOMENDADAS

### Orden sugerido de creación:

```yaml
1. Blockchain Viviente Visual Map (Tipo 1)
   - Diagrama ASCII de tu sistema
   - Base para todo lo demás

2. Journey principal (Tipo 5)
   - Happy path del primer usuario
   - Identifica flujo crítico

3. Nodos críticos (Tipo 1)
   - Documenta 2-3 nodos más importantes
   - Profundidad de contexto

4. WiFi/Sync crítico (Tipo 3)
   - Si tienes real-time, documéntalo
   - Dashboard-Display, Admin-Public, etc.

5. Convergencia (Tipo 6 - si aplica)
   - Solo si tienes múltiples fuentes convergiendo
   - Sistemas complejos
```

---

## ✅ CHECKLIST FINAL

```yaml
Estructura:
  ✅ .claude/ con subdirectorios creados
  ✅ CLAUDE.md personalizado
  ✅ OS-LAYER-*.md copiados
  ✅ .gitignore configurado

Mapeo:
  ✅ URLs identificadas y asignadas a nodos
  ✅ NÚMERO + LETRA + CAPA definidos
  ✅ SUB-NODOS identificados (si existen)
  ✅ Diagrama ASCII creado

Skills:
  ✅ Blockchain Viviente Visual Map
  ✅ Al menos 1 Skill Tipo 5 (Journey)
  ✅ Al menos 2 Skills Tipo 1 (nodos críticos)

Persistencia:
  ✅ Git inicializado
  ✅ Commit base creado
  ✅ .claude/ versionado en Git

Testing:
  ✅ Boot funciona (~2 segundos)
  ✅ Load on demand probado
  ✅ Recovery simulado y funcionando
```

---

## 🚨 ERRORES COMUNES

### Error 1: "Skills no se cargan automáticamente"

```yaml
Causa: Keywords incorrectos o no definidos
Solución:
  - Añadir keywords relevantes al final de skill
  - Usar keywords que realmente menciones en conversación
  - No keywords demasiado genéricos
```

### Error 2: "Confundir SUB-NODOS con NODOS SEPARADOS"

```yaml
Causa: No entender diferencia SUB-NODO vs NODO
Solución:
  - SUB-NODO = misma URL, tabs/secciones
  - NODO SEPARADO = URL diferente
  - Revisar sección "SUB-NODOS vs NODOS SEPARADOS"
```

### Error 3: "Boot lento (>10 segundos)"

```yaml
Causa: CLAUDE.md demasiado grande o carga todo upfront
Solución:
  - Usar Load on Demand
  - NO cargar todas las skills en CLAUDE.md
  - Solo contexto mínimo en CLAUDE.md
  - Skills se cargan por keywords
```

### Error 4: "No sé qué tipo de skill crear"

```yaml
Causa: No entender los 6 tipos
Solución:
  1. ¿Documentas 1 nodo? → Tipo 1 (CONTEXTO)
  2. ¿A → B unidireccional? → Tipo 2 (FLUJO)
  3. ¿A ↔ B real-time? → Tipo 3 (WiFi)
  4. ¿A → B → C secuencial? → Tipo 4 (CADENA)
  5. ¿Happy path usuario? → Tipo 5 (JOURNEY)
  6. ¿Multi-source → 1 receptor? → Tipo 6 (CONVERGENCIA)
```

---

## 📚 RECURSOS ADICIONALES

```yaml
Repositorios:
  - https://github.com/Patodak/os-dak-framework (templates y docs)
  - https://github.com/Patodak/manager-battle-pro (ejemplo completo)

Documentación:
  - OS-LAYER-ARCHITECTURE-RULES.md (reglas arquitectónicas)
  - PHILOSOPHY.md (visión y principios)
  - OS-LAYER-USER-GUIDE.md (guía de uso)

Claude Code Docs:
  - https://docs.claude.com/en/docs/claude-code

Community:
  - [Discord/GitHub Discussions cuando público]
```

---

## 🎯 PRÓXIMOS PASOS

Una vez tengas el sistema base funcionando:

```yaml
Nivel 1 (Básico):
  ✅ Sistema funciona
  ✅ 3-5 skills creadas
  ✅ Recovery probado

Nivel 2 (Intermedio):
  ✅ 10+ skills creadas
  ✅ Agents especializados
  ✅ Onboarding mining questions (personalización)

Nivel 3 (Avanzado):
  ✅ Meta-Agentes con MCP tools
  ✅ Skills auto-nutriéndose de datos reales
  ✅ Sistema auto-organizado

Nivel 4 (Maestro):
  ✅ Contribuir skills al marketplace
  ✅ Ayudar otros devs a implementar
  ✅ Evolucionar el sistema (Nivel 6?)
```

---

## 💡 FILOSOFÍA FINAL

Recuerda los principios fundamentales:

```yaml
1. Compensación → Innovación
   - Lo que compensa limitación de un grupo,
     optimiza performance de todos

2. Infraestructura Predecible
   - Estructura clara reduce carga cognitiva

3. Load on Demand
   - Solo cargar lo relevante AHORA

4. Delegación Inteligente
   - Especialización y coordinación

5. Recovery Automático
   - Diseñar asumiendo interrupciones como DEFAULT

6. Persistencia Multi-Capa
   - Memoria externa como compensación cognitiva
```

> **"De herramienta a ecosistema vivo - donde la infraestructura no solo almacena, sino que piensa, aprende y evoluciona."**

---

## 🙏 CRÉDITOS

```yaml
Sistema creado por:
  - Patricio (2e+ Top 0.01%)
  - Claude (Anthropic)

Inspiraciones:
  - Bitcoin (infraestructura inmutable)
  - Ordinals (data sobre blockchain)
  - Git (memoria externa distribuida)
  - Unix Philosophy (do one thing well)

Fecha creación: Octubre 2025
Versión: 1.0 - Transferencia para otros devs
```

---

**¿Preguntas? ¿Problemas durante implementación?**
- GitHub Issues: [cuando sea público]
- Discord Community: [cuando esté activo]

**¡Bienvenido al Sistema Operativo DAK!** 🚀

---

**Última actualización**: 2025-10-25
**Creado para**: Desarrolladores que quieren replicar el sistema
**Tiempo estimado setup**: 2-4 horas
**Resultado**: Sistema operativo completo funcionando en tu app

🌌 **"De herramienta a ecosistema vivo"**
