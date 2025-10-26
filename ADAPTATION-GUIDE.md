# 📘 ADAPTATION GUIDE: OS DAK para Tu Aplicación

**Guía paso a paso para adaptar el Sistema Operativo DAK a tu app**

---

## 🎯 Objetivo

Al terminar esta guía, tendrás:
- ✅ Sistema OS DAK funcionando en tu app
- ✅ Blockchain Visual Map completo
- ✅ Skills especializadas documentadas
- ✅ Recovery automático configurado
- ✅ Comunicación eficiente con Claude Code

**Tiempo estimado:** 2-4 horas (primera vez)

---

## 📋 Pre-requisitos

```yaml
Tecnología:
  ✅ Claude Code CLI instalado
  ✅ Git configurado
  ✅ Aplicación existente con routing

Stack compatible:
  - Next.js (App Router o Pages Router)
  - React Router
  - Vue Router
  - Svelte Kit
  - Cualquier framework con routing basado en URLs

Complejidad mínima recomendada:
  - 15+ URLs diferentes
  - 2+ roles de usuario (o lógica equivalente)
  - Arquitectura multi-capa
```

---

## PASO 1: Identificar URLs de Tu App

### 1.1 Listar TODAS las URLs

**Objetivo:** Inventario completo de rutas

**En Soluciones Díaz:**
```yaml
Método usado:
  - Análisis AST de carpeta /app (Next.js App Router)
  - Validación manual navegando app
  - Documentado en route-navigator skill

Resultado:
  - 18 URLs Guest (públicas)
  - 8 URLs adicionales Cliente (autenticadas)
  - 2 URLs Admin Mobile (iPhone)
  - 6 URLs Admin Desktop (oficina)
```

**Para TU app:**

**Método A: Análisis automático (Next.js)**
```bash
# Si usas Next.js App Router
find src/app -type f -name "page.tsx" -o -name "page.ts" | sed 's|src/app||' | sed 's|/page.tsx||' | sed 's|/page.ts||'

# Si usas Pages Router
find pages -type f -name "*.tsx" -o -name "*.ts" | grep -v "_" | sed 's|pages||' | sed 's|\.tsx||' | sed 's|\.ts||'
```

**Método B: Análisis manual (React Router, Vue)**
```typescript
// Busca archivo de routing (ej: router.ts, routes.tsx)
// Extrae todas las rutas definidas

// React Router ejemplo:
<Route path="/" element={<Home />} />           → /
<Route path="/products" element={<Products />} /> → /products
<Route path="/admin" element={<Admin />} />     → /admin
```

**Método C: Navegación manual**
1. Abre app en browser
2. Navega todas las secciones
3. Anota URL de cada página
4. Diferencia públicas vs autenticadas

**Output esperado: Lista completa**
```yaml
URLs_PUBLICAS:
  - /
  - /productos
  - /contacto
  - /auth/signin
  - /auth/signup

URLs_AUTENTICADAS:
  - /dashboard
  - /perfil
  - /configuracion

URLs_ADMIN:
  - /admin
  - /admin/usuarios
  - /admin/reportes
```

---

### 1.2 Agrupar por Rol/Contexto

**Pregunta clave:** ¿Quién puede acceder a cada URL?

**En Soluciones Díaz:**
```yaml
Agrupación por roles:
  Guest (no autenticado):
    - Páginas web públicas (18)
    - Auth (signin, signup)

  Cliente (autenticado):
    - Hereda Guest (18)
    - Cotizaciones (2)
    - Dashboard (4 tabs)

  Admin Mobile:
    - Hereda Cliente (26)
    - Cotizaciones admin (1)
    - Clientes admin (1)

  Admin Desktop:
    - Hereda Admin Mobile (28)
    - Dashboard admin (1)
    - CRUD contenido (2)
    - Herramientas (3)
```

**Para TU app:**

**¿Tienes roles?**

**SÍ → Agrupa por rol:**
```yaml
Ejemplo e-commerce:

VISITANTE (no autenticado):
  - / (homepage)
  - /productos
  - /producto/[id]
  - /auth/signin

CLIENTE (autenticado):
  - Hereda VISITANTE
  - /mi-cuenta
  - /mis-pedidos
  - /checkout

VENDEDOR:
  - Hereda CLIENTE
  - /admin/productos
  - /admin/inventario

ADMIN:
  - Hereda VENDEDOR
  - /admin/usuarios
  - /admin/reportes
```

**NO → Agrupa por acceso:**
```yaml
Ejemplo app SaaS sin roles tradicionales:

PÚBLICO:
  - / (landing)
  - /pricing
  - /auth

FREE TIER:
  - Hereda PÚBLICO
  - /dashboard
  - /proyectos (max 3)

PRO TIER:
  - Hereda FREE
  - /proyectos (ilimitado)
  - /analytics
  - /api-access
```

---

### 1.3 Identificar NODOS vs SUB-NODOS

**REGLA CRÍTICA:** NO toda sección es un nodo separado.

**NODO (URL diferente):**
```yaml
Características:
  - URL propia única
  - Requiere navegación (cambio de route)
  - Ejemplo: /dashboard → /perfil (cambio URL)

En Soluciones Díaz:
  - 1A: /cotizacion
  - 2A1: /cotizacion/rapida
  - 2A2: /cotizacion/detallada
  - 1B: /mi-dashboard
```

**SUB-NODO (misma URL, tabs/secciones):**
```yaml
Características:
  - Misma URL base
  - Cambio por tabs, accordion, modal
  - React/Vue state cambia vista
  - Ejemplo: /dashboard?tab=perfil (misma URL)

En Soluciones Díaz:
  - 1B: /mi-dashboard (nodo base)
    - 1B.Perfil (tab)
    - 1B.Cotizaciones (tab)
    - 1B.Mensajes (tab)
    - 1B.Notificaciones (tab)
```

**¿Cómo identificar en TU app?**

**Pregunta:** ¿Cambiar de X a Y requiere navegación?

**SÍ → NODOS SEPARADOS**
```typescript
// Usuario navega /productos → /checkout
// URL cambia completamente
// → NODOS SEPARADOS

2B: /productos
4A: /checkout
```

**NO → SUB-NODOS**
```typescript
// Usuario cambia tab en /dashboard (Perfil → Configuración)
// URL NO cambia (o solo query param ?tab=)
// → SUB-NODOS

3A: /dashboard
  3A.Perfil
  3A.Configuracion
  3A.Notificaciones
```

**Output esperado:**
```yaml
NODOS:
  - /
  - /productos
  - /checkout
  - /dashboard

SUB-NODOS (dentro de /dashboard):
  - Dashboard.Resumen
  - Dashboard.Perfil
  - Dashboard.Configuracion
```

---

## PASO 2: Adaptar CAPA 0 a Tu Sistema

**Template genérico dice:**
```yaml
CAPA 0: Guardian de Planes (pre-ejecución)
  - Valida planes ANTES de ejecutar
  - NO tiene URL
  - Función: Preguntar, validar, proteger
```

**¿Aplica a tu app?**

### Opción A: Tienes ROLES → CAPA 0 = Roles

**En Soluciones Díaz:**
```yaml
CAPA 0: Sistema de Roles (G/C/A/SA)

Por qué:
  - Es un sitio web CON CRM
  - Roles determinan TODO (URLs, permisos, flujos)
  - El "guardian de planes" no era el guardian real

Resultado:
  G = Guest (no autenticado)
  C = Cliente (autenticado)
  A = Admin (gestión)
  SA = SuperAdmin (dev)
```

**Para TU app con roles:**
```yaml
Define tus letras:

Ejemplo 1 (e-commerce):
  V = Visitante
  C = Cliente
  VE = Vendedor
  A = Admin

Ejemplo 2 (SaaS):
  P = Público
  F = Free Tier
  PR = Pro
  E = Enterprise
  A = Admin

Ejemplo 3 (educativo):
  E = Estudiante
  P = Profesor
  CO = Coordinador
  A = Admin
```

### Opción B: NO tienes ROLES → CAPA 0 = Guardian Planes

**Caso de uso:** App sin autenticación o lógica simple

**Usar template genérico:**
```yaml
CAPA 0: Guardian de Planes

Función:
  - Antes de ejecutar, validar plan
  - Usar TodoWrite proactivamente
  - Preguntar antes de proceder
  - Proteger contra ejecuciones innecesarias

Implementación:
  - NO tiene URL (pre-ejecución)
  - Activado por keywords: "quiero hacer", "necesito"
  - Responde con plan estructurado antes de actuar
```

### Opción C: HÍBRIDO (Roles + Guardian)

**Caso avanzado:** Roles + validación pre-ejecución

```yaml
CAPA 0A: Sistema de Roles
  - Define quién puede acceder

CAPA 0B: Guardian de Planes
  - Valida qué se va a ejecutar

Ambos coexisten, diferentes propósitos
```

**Output esperado:**
```yaml
# En tu blockchain map

CAPA 0 - [TU DEFINICIÓN]
├── [Descripción de qué es]
├── URL: [N/A o específica]
└── Función: [Explicar propósito]
```

---

## PASO 3: Mapear URLs a NÚMERO+LETRA

### 3.1 Asignar NÚMERO (Profundidad Funcional)

**IMPORTANTE:** NÚMERO = profundidad FUNCIONAL (no física de URL)

**Decisión de Soluciones Díaz:**
```yaml
NÚMERO 1: Puerta (selector, inicio)
  - Punto de entrada a funcionalidad
  - Usuario decide qué hacer
  - Ejemplo: /cotizacion (selector rápida/detallada)

NÚMERO 2: Túnel (formulario, flujo)
  - Proceso guiado paso a paso
  - Usuario completa flujo
  - Ejemplo: /cotizacion/rapida (formulario 5 min)

NÚMERO 3: Control (dashboard, gestión)
  - Centro de control con múltiples acciones
  - Usuario gestiona datos
  - Ejemplo: /mi-dashboard (4 tabs)

NÚMERO 4: Proyección (analytics, futuro)
  - Análisis, tendencias, predicciones
  - Usuario visualiza resultados
  - Ejemplo: /admin/reportes (métricas)
```

**Para TU app:**

**Método: Pregúntate sobre cada URL**

1. **¿Es punto de entrada?** → NÚMERO 1
   ```yaml
   Ejemplos:
     - /dashboard (inicio admin)
     - /productos (catálogo)
     - /checkout (inicio compra)
   ```

2. **¿Es flujo guiado?** → NÚMERO 2
   ```yaml
   Ejemplos:
     - /checkout/paso-1 (wizard)
     - /onboarding (pasos 1-5)
     - /formulario-contacto (multi-step)
   ```

3. **¿Es centro de control?** → NÚMERO 3
   ```yaml
   Ejemplos:
     - /admin/dashboard (widgets)
     - /mi-cuenta (tabs perfil)
     - /panel-control (múltiples acciones)
   ```

4. **¿Es análisis/proyección?** → NÚMERO 4
   ```yaml
   Ejemplos:
     - /analytics (métricas)
     - /reportes (tendencias)
     - /predicciones (ML)
   ```

**Output esperado:**
```yaml
URLs mapeadas a NÚMERO:

1: /dashboard (puerta admin)
1: /productos (puerta catálogo)
2: /checkout (túnel compra)
3: /admin/usuarios (control gestión)
4: /analytics (proyección métricas)
```

---

### 3.2 Asignar LETRA (Contexto de Uso)

**Decisión de Soluciones Díaz:**
```yaml
LETRA A: Admin/Control/Dashboard
  - Acciones de gestión
  - Crear, modificar, eliminar
  - Ejemplo: /admin/servicios

LETRA B: Browse/Lista/Público
  - Navegación y lectura
  - Explorar, buscar, ver
  - Ejemplo: /servicios (público)

LETRA C: History/Auditoría
  - (No usado actualmente en Soluciones Díaz)
  - Reservado para futuro
```

**Para TU app:**

**Define significado de tus letras:**

**Opción 1: Por tipo de acción**
```yaml
A = Action (crear, modificar)
B = Browse (leer, navegar)
C = Control (admin, gestión)
D = Data (reportes, analytics)
```

**Opción 2: Por audiencia**
```yaml
A = Admin
B = Business (vendedor)
C = Customer (cliente)
P = Public (visitante)
```

**Opción 3: Por funcionalidad**
```yaml
A = Authentication
B = Business logic
C = Content management
D = Data analytics
```

**En Soluciones Díaz (ejemplo aplicado):**
```yaml
1A: /cotizacion
  - Número 1 (puerta)
  - Letra A (acción: crear cotización)

2A1: /cotizacion/rapida
  - Número 2 (túnel)
  - Letra A (acción continua)
  - Sufijo 1 (variante rápida)

1B: /mi-dashboard
  - Número 1 (puerta)
  - Letra B (browse: navegar tabs)
```

**Output esperado:**
```yaml
URLs con NÚMERO+LETRA:

1A: /admin/dashboard
2A: /admin/productos/crear
1B: /productos
2B: /producto/[id]
3C: /admin/usuarios
4D: /analytics
```

---

### 3.3 Combinar NÚMERO+LETRA+Descripción

**Formato final:**
```
[NÚMERO][LETRA][sufijo opcional]: [URL] - [Descripción breve]
```

**Ejemplos Soluciones Díaz:**
```yaml
CAPA 1A: /cotizacion - Selector rápida/detallada
CAPA 2A1: /cotizacion/rapida - Formulario 5 min
CAPA 2A2: /cotizacion/detallada - Wizard 10-15 min + fotos
CAPA 1B: /mi-dashboard - Dashboard con 4 tabs
  SUB-NODOS:
    1B.Perfil: Tab perfil usuario
    1B.Cotizaciones: Tab ver cotizaciones
    1B.Mensajes: Tab mensajes admin
    1B.Notificaciones: Tab notificaciones sistema
```

**Para TU app (ejemplo):**
```yaml
CAPA 1P: / - Homepage pública
CAPA 1A: /admin - Dashboard admin
CAPA 2A1: /admin/productos/crear - Crear producto
CAPA 2A2: /admin/productos/editar - Editar producto
CAPA 3A: /admin/productos - Gestión productos
CAPA 1B: /productos - Catálogo público
CAPA 2B: /producto/[id] - Detalle producto
```

---

## PASO 4: Documentar Transacciones (Skills)

**Los 6 tipos de Skills** (del template original):

### Tipo 1: CONTEXTO (1 nodo)
```yaml
Cuándo usar: Documentar UN nodo específico en profundidad
Tamaño: ~15-20KB

Ejemplo Soluciones Díaz:
  - ROL_CLIENTE_CAPA_2A1_COTIZACION_RAPIDA.md
  - Documenta TODO sobre nodo 2A1

Contiene:
  - Componentes del nodo
  - Collections Firebase/DB
  - Transformaciones de data
  - Roles y permisos
  - Límites y validaciones
```

### Tipo 2: FLUJO (A → B unidireccional)
```yaml
Cuándo usar: Info fluye de A a B (no viceversa)
Tamaño: ~5-10KB

Ejemplo Soluciones Díaz:
  - Cliente crea cotización (2A1) → Admin recibe (dashboard)
  - Flujo unidireccional: C → A

Contiene:
  - Origen: Dónde inicia flujo
  - Destino: Dónde termina
  - Data transferida
  - Transformaciones
```

### Tipo 3: WiFi (A ↔ B bidireccional real-time)
```yaml
Cuándo usar: Sincronización LIVE entre 2 nodos
Tamaño: ~10-15KB

Ejemplo Soluciones Díaz:
  - Admin cambia estado cotización ↔ Cliente ve cambio <1s
  - Requiere: Firebase onSnapshot

Contiene:
  - Nodos conectados
  - Mecanismo sync (WebSocket, Firebase, SSE)
  - Frecuencia actualización
  - Manejo conflictos
```

### Tipo 4: CADENA (A → B → C secuencial)
```yaml
Cuándo usar: Transformaciones multi-nodo
Tamaño: ~15-20KB

Ejemplo Soluciones Díaz:
  - Cliente crea cotización → Admin responde → Cliente confirma
  - Patrón: C → A → C (cadena 3 pasos)

Contiene:
  - Cada paso de la cadena
  - Data transformada en cada paso
  - Condiciones de transición
```

### Tipo 5: JOURNEY (Happy path usuario)
```yaml
Cuándo usar: Primer camino del usuario (onboarding)
Tamaño: ~10-15KB

Ejemplo Soluciones Díaz:
  - Guest → Registro → Primera cotización → Dashboard
  - Salta pasos intermedios para facilitar

Contiene:
  - Entrada (inicio)
  - Destino (objetivo)
  - Pasos mínimos
  - Experiencia optimizada
```

### Tipo 6: CONVERGENCIA (Multi → Uno)
```yaml
Cuándo usar: Múltiples fuentes → 1 receptor
Tamaño: ~20-30KB

Ejemplo Soluciones Díaz:
  - Dashboard Admin lee:
    - Cotizaciones (collection)
    - Clientes (collection)
    - Eventos (collection)
    - Configuración (collection)
  - Todo converge en 1 vista

Contiene:
  - Fuentes (múltiples)
  - Receptor (único)
  - Agregación de data
  - Actualización
```

---

### 4.1 Crear Tu Primera Skill

**Recomendación:** Empieza con Tipo 1 (CONTEXTO) del nodo más importante.

**Template básico:**

```markdown
# [NOMBRE DESCRIPTIVO] - Nodo [X][Y]

**Tipo:** Skill Tipo [1-6] - [NOMBRE TIPO]
**Nodo(s):** [Identificación]
**URL(s):** [URLs involucradas]
**Rol(es):** [Quién accede]

---

## 🎯 Propósito

[1-2 párrafos explicando qué documenta esta skill]

---

## 🗺️ Ubicación en Blockchain

```
CAPA [X][Y]: [URL]
  ├── Profundidad: [NÚMERO] ([significado])
  ├── Contexto: [LETRA] ([significado])
  └── Tipo: [Nodo / SUB-NODO]
```

---

## 🔗 Flujo de Información

[Diagrama o descripción del flujo]

Ejemplo:
```
Usuario → Componente → API → Database
  ↓          ↓         ↓        ↓
Input    Validación  Auth    Firestore
```

---

## 📊 Schema de Datos

[Qué collections/tablas/endpoints usa]

Ejemplo:
```typescript
interface Quote {
  id: string;
  clientId: string;
  serviceType: string;
  description: string;
  status: 'NUEVO' | 'CONTACTADO' | 'COTIZADO';
  createdAt: Timestamp;
}
```

---

## 🎯 Componentes Involucrados

[Lista de archivos/componentes]

Ejemplo:
- `src/app/cotizacion/rapida/page.tsx` (UI)
- `src/components/QuoteForm.tsx` (formulario)
- `src/lib/quotes.ts` (lógica)
- `src/app/api/quotes/route.ts` (API)

---

## 🔐 Roles y Permisos

[Quién puede acceder y qué puede hacer]

Ejemplo:
```yaml
Cliente:
  ✅ Puede crear cotización
  ✅ Puede ver SUS cotizaciones
  ❌ NO puede ver cotizaciones otros clientes

Admin:
  ✅ Puede ver TODAS las cotizaciones
  ✅ Puede cambiar estado
  ✅ Puede asignar precio
```

---

## ⚡ Casos de Uso

### Caso 1: [Nombre escenario]
[Descripción paso a paso]

### Caso 2: [Nombre escenario]
[Descripción paso a paso]

---

**Última actualización:** [fecha]
**Creado por:** [nombre] + Claude
**Keywords:** [palabras clave para auto-activación]
```

**Guardar en:** `.claude/skills/[nombre-descriptivo]/SKILL.md`

---

## PASO 5: Crear Blockchain Visual Map

**El mapa maestro ASCII que resume TODO.**

**Template base:**

```markdown
# BLOCKCHAIN VIVIENTE - [TU APP]

Sistema Operativo de Desarrollo con IA

## Analogía: Blockchain Viviente

Cada NODO = Bloque en blockchain
Cada SKILL = Transacción documentada
Cada AGENT = Smart contract vivo

┌─────────────────────────────────────────────────────────────────┐
│  NÚMERO = PROFUNDIDAD ([tu definición])                         │
│  LETRA  = CONTEXTO ([tu definición])                            │
│  CAPA   = URL física única                                      │
└─────────────────────────────────────────────────────────────────┘

---

## CAPA 0 - [TU DEFINICIÓN]

├── [Descripción]
├── URL: [N/A o específica]
└── Función: [Propósito]

---

## CAPA 1 - [FUNCIÓN NIVEL 1]

### 1A: [URL]
├── Descripción: [...]
├── Rol: [...]
└── 🔧 Skill: [nombre-skill].md

### 1B: [URL]
├── Descripción: [...]
├── Rol: [...]
└── 🔧 Skill: [nombre-skill].md

---

## CAPA 2 - [FUNCIÓN NIVEL 2]

### 2A1: [URL]
├── Descripción: [...]
├── Rol: [...]
└── 🔧 Skill: [nombre-skill].md

### 2A2: [URL]
├── Descripción: [...]
├── Rol: [...]
└── 🔧 Skill: [nombre-skill].md

[... continuar con todos tus nodos ...]

---

## TRANSACCIONES DOCUMENTADAS

### Tipo 2 - FLUJO
- [Nombre]: [A] → [B]
- Skill: [nombre].md

### Tipo 3 - WiFi
- [Nombre]: [A] ↔ [B]
- Skill: [nombre].md

[... listar todas tus skills por tipo ...]

---

## ESTADÍSTICAS

```yaml
Total Nodos: X
Total SUB-NODOS: Y
Total Skills: Z
Roles: [listar]
Última actualización: [fecha]
```
```

**Guardar en:** `.claude/skills/blockchain-viviente-visual-map/SKILL.md`

---

## PASO 6: Configurar Persistencia y Recovery

### 6.1 Git como Memoria Externa

```bash
# Asegúrate de que .claude/ está versionado
git add .claude/
git commit -m "🚀 Sistema OS DAK inicializado"
```

**Estructura recomendada:**

```
tu-proyecto/
├── .claude/
│   ├── skills/
│   │   ├── blockchain-viviente-visual-map/SKILL.md
│   │   └── [tus-skills]/
│   ├── agents/ (opcional)
│   ├── sessions/ (auto-generadas por Claude Code)
│   └── knowledge/ (auto-capturadas)
├── .gitignore
└── CLAUDE.md (kernel del OS)
```

**.gitignore:**
```
# Node modules, builds, etc.
node_modules/
.next/
dist/

# Pero NO ignores .claude/
# .claude/ DEBE estar en Git
```

### 6.2 Recovery Automático

**Cómo funciona:**
1. Claude Code crashea (corte luz, cerrar terminal)
2. Reabres Claude Code en tu proyecto
3. Sistema detecta crash (sessions JSONL)
4. Ofrece recovery automático
5. Aceptas
6. Contexto restaurado completo (última conversación + archivos)

**NO requiere configuración manual** (automático en Claude Code CLI)

---

## PASO 7: Testing del Sistema

### 7.1 Test 1: Boot Time

```bash
# Abrir Claude Code en tu proyecto
claude

# Verificar:
✅ Carga CLAUDE.md en <2 segundos
✅ Working Memory <70KB inicial
✅ Skills NO cargadas (load on demand)
```

### 7.2 Test 2: Load on Demand

```yaml
Acción: Menciona keyword de una skill
Usuario: "Revisa la skill de cotización rápida"

Esperado:
  ✅ Claude carga skill automáticamente
  ✅ Muestra contexto relevante
  ✅ No carga todas las skills (solo la mencionada)
```

### 7.3 Test 3: Recovery Post-Crash

```yaml
Pasos:
  1. Crea cambios importantes
  2. git commit -m "test"
  3. Cierra Claude Code forzado (Ctrl+C o cerrar terminal)
  4. Reabrir Claude Code
  5. Sistema ofrece recovery

Esperado:
  ✅ Recovery ofrecido
  ✅ Contexto restaurado (archivos, conversación)
  ✅ Sin pérdida de información
```

### 7.4 Test 4: Comunicación Sistémica

```yaml
Acción: Usa nomenclatura blockchain
Usuario: "Revisa nodo 2A1 y verifica que conecta con 1B.Cotizaciones"

Esperado:
  ✅ Claude entiende nomenclatura
  ✅ Navega directamente a nodos correctos
  ✅ Valida conexión entre nodos
  ✅ Responde usando misma nomenclatura
```

---

## ✅ CHECKLIST FINAL

```yaml
Estructura:
  ✅ .claude/ creado con subdirectorios
  ✅ CLAUDE.md personalizado (kernel)
  ✅ .gitignore configurado (NO ignora .claude/)

Mapeo:
  ✅ URLs identificadas (TODAS)
  ✅ Agrupadas por rol/contexto
  ✅ NODOS vs SUB-NODOS diferenciados
  ✅ NÚMERO+LETRA asignados
  ✅ CAPA 0 definida

Skills:
  ✅ Blockchain Visual Map creado
  ✅ Al menos 1 Skill Tipo 1 (nodo importante)
  ✅ Al menos 1 Skill Tipo 5 (journey usuario)

Persistencia:
  ✅ Git inicializado
  ✅ .claude/ versionado en Git
  ✅ Commit base creado

Testing:
  ✅ Boot <2 segundos
  ✅ Load on demand funciona
  ✅ Recovery probado
  ✅ Comunicación sistémica validada
```

---

## 🎯 PRIMERAS SKILLS RECOMENDADAS

**Orden sugerido:**

1. **Blockchain Visual Map** (obligatorio)
   - Mapa maestro ASCII
   - Visión helicopter de TODO

2. **Journey principal** (Tipo 5)
   - Happy path del primer usuario
   - Ej: Registro → Primera acción → Objetivo

3. **2-3 Nodos críticos** (Tipo 1)
   - Documenta nodos más importantes
   - Profundidad de contexto

4. **1 Flujo importante** (Tipo 2 o 3)
   - Conexión crítica entre nodos
   - Real-time si aplica

---

## 🚨 ERRORES COMUNES Y SOLUCIONES

### Error 1: "Skills no se cargan automáticamente"

**Causa:** Keywords incorrectos o no definidos

**Solución:**
```yaml
1. Al final de cada skill, agregar:
   **Keywords:** cotizacion, rapida, formulario, 2A1

2. Usar keywords que REALMENTE mencionas en conversación

3. NO keywords demasiado genéricos (ej: "page", "component")
```

### Error 2: "Confundo NODOS con SUB-NODOS"

**Causa:** No entender diferencia

**Solución:**
```yaml
Pregunta clave: ¿Cambia la URL?

SÍ → NODOS SEPARADOS
  /dashboard → /perfil (URLs diferentes)

NO → SUB-NODOS
  /dashboard?tab=perfil (misma URL, query param)
  /dashboard (tabs internos sin URL)
```

### Error 3: "Boot lento (>10 segundos)"

**Causa:** CLAUDE.md carga todo upfront

**Solución:**
```yaml
CLAUDE.md debe ser MÍNIMO:
  - Estructura proyecto
  - Comandos básicos
  - Links a skills (NO contenido completo)

Skills se cargan on-demand por keywords
```

### Error 4: "No sé qué NÚMERO asignar"

**Causa:** Confundir profundidad física vs funcional

**Solución:**
```yaml
NO mires niveles de URL:
  /admin/productos/crear = 3 niveles (NO usar 3)

SÍ pregunta función:
  ¿Es puerta/inicio? → 1
  ¿Es flujo/túnel? → 2
  ¿Es control/dashboard? → 3
  ¿Es analytics/proyección? → 4
```

---

## 🎓 PRÓXIMOS PASOS

**Una vez tengas el sistema base:**

```yaml
Nivel 1 (Básico):
  ✅ Sistema funciona
  ✅ 3-5 skills creadas
  ✅ Recovery probado

Nivel 2 (Intermedio):
  ✅ 10+ skills creadas
  ✅ Todos los nodos documentados
  ✅ Agents especializados (opcional)

Nivel 3 (Avanzado):
  ✅ Skills auto-nutriéndose de datos reales
  ✅ Integración con MCP tools
  ✅ Sistema auto-organizado

Nivel 4 (Maestro):
  ✅ Contribuir al repo público
  ✅ Ayudar otros devs
  ✅ Evolucionar metodología
```

---

## 📚 RECURSOS ADICIONALES

```yaml
Repositorios:
  - os-dak-soluciones-diaz-case-study (este)
  - ORIGINAL-TEMPLATE.md (template genérico)

Documentación:
  - DECISIONS-LOG.md (decisiones de Soluciones Díaz)
  - examples/ (casos reales antes/después)
  - templates/ (plantillas reutilizables)

Comunidad:
  - GitHub Issues (preguntas)
  - GitHub Discussions (compartir adaptaciones)
```

---

## 💡 TIPS FINALES

1. **Empieza simple**
   - No documentes TODO de una vez
   - 5 nodos bien documentados > 20 a medias

2. **Itera y mejora**
   - Sistema evoluciona con uso
   - Ajusta nomenclatura si no funciona

3. **Comunica sistémicamente**
   - Usa NÚMERO+LETRA en conversaciones
   - "Revisa nodo 2A1" > "Revisa el archivo de cotización rápida"

4. **Confía en load-on-demand**
   - NO cargues todo en CLAUDE.md
   - Keywords activan skills cuando necesario

5. **Documenta decisiones**
   - Mantén DECISIONS-LOG.md actualizado
   - Futuros tú agradecerán contexto

---

**Creado:** 2025-10-26
**Autor:** Patricio Díaz + Claude
**Para:** Developers adaptando OS DAK
**Tiempo estimado:** 2-4 horas primera vez, 1-2 horas después
**Resultado:** Sistema operativo completo funcionando en tu app

🌌 **"De herramienta a ecosistema vivo"**
