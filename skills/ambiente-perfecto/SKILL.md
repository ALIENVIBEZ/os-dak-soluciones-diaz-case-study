---
name: ambiente-perfecto
description: Auto-mapeo multi-rol de blockchain viviente (4 roles) + detección de gaps documentales + ARTERIAS de optimización (80-140x velocidad)
allowed-tools: Read, Glob, Grep
version: 2.0
---

# 🌟 AMBIENTE PERFECTO - Sistema Auto-Evolutivo Multi-Rol

**Tipo:** Metacognitivo
**Función:** Radar automático de estado del proyecto con soporte multi-rol
**Roles:** Guest, Client, Admin (Desktop/Mobile), Super Admin
**Frecuencia:** Cada commit importante o a demanda
**Inspiración:** dak-chain-ia DEVELOPER_PACK_UNIVERSAL.md

---

## 🎯 PROPÓSITO

Sistema que **automáticamente**:
1. 🗺️ Mapea la arquitectura blockchain viviente completa **por ROL**
2. 🔍 Detecta vacíos documentales (gaps) **segmentados por ROL**
3. ⚡ Identifica ARTERIAS de optimización (80-140x speedup) **específicas por ROL**
4. 🤖 Delega tareas a agentes externos cuando es óptimo
5. 📊 Genera reporte de salud del proyecto (0-100 score) **con breakdown por ROL**
6. 🎯 Sugiere próximas acciones priorizadas por impacto **y ROL**

**Soporte Multi-Rol:**
- 🔴 **GUEST** (NO_AUTENTICADO): Visitantes sin login - 18 URLs públicas
- 🔵 **CLIENT** (CLIENTE): Usuarios registrados - 9 URLs
- 🟢 **ADMIN** (Jonathan - Dueño): Panel admin Desktop + Mobile - 10 URLs
- 🟣 **SUPER_ADMIN** (Patricio - Dev): Herramientas dev/testing - 54 URLs

**Trigger keywords:** "estado del proyecto", "qué falta", "ambiente perfecto", "health check", "gaps"

---

## 🧠 CONCEPTO: ARTERIAS

**Definición:** Pre-carga estratégica de contexto que acelera 80-140x las operaciones repetitivas.

**Analogía Biológica:**
Como las arterias del cuerpo humano transportan sangre oxigenada antes de que los órganos la necesiten, las ARTERIAS del código pre-cargan contexto antes de que los agentes lo soliciten.

**Ejemplo concreto:**
```yaml
ANTES (sin ARTERIA):
  Usuario: "analiza cotizaciones admin"
  → Agente carga blockchain-viviente (15s)
  → Agente carga route-navigator (8s)
  → Agente carga ROL_ADMIN_MOBILE_COTIZACIONES.md (5s)
  → Total: 28s + análisis

DESPUÉS (con ARTERIA "admin-cotizaciones"):
  Usuario: "analiza cotizaciones admin"
  → ARTERIA pre-cargó 3 skills en paralelo (3s)
  → Análisis inmediato
  → Total: 3s + análisis

  Speedup: 28s → 3s = 9.3x ✅
```

**ARTERIAS != Cache:** No es almacenar respuestas, es **predecir qué contexto se necesitará** y pre-cargarlo.

---

## 🗺️ AUTO-MAPEO DE BLOCKCHAIN

### Fase 1: Escaneo Automático

```bash
FUENTES DE VERDAD:
  1. .claude/skills/blockchain-viviente-soluciones-diaz/SKILL.md
  2. .claude/skills/route-navigator/SKILL.md
  3. .claude/core/ROLES_COMPLETO.md (sistema 4 roles)
  4. .claude/core/URLS_REALES_VALIDADAS.md (rutas públicas validadas)
  5. .claude/core/ROL_*_*.md (15 archivos documentados)
  6. Código real en src/app/
```

**Algoritmo de mapeo:**
1. Leer blockchain-viviente → extraer nodos documentados
2. Escanear src/app/ → encontrar rutas físicas
3. **Clasificar rutas por ROL** (Guest, Cliente, Admin, Super Admin)
4. **Detectar dispositivo** (Desktop vs Mobile para Admin)
5. Comparar → detectar gaps (nodos sin docs por rol)
6. Validar capas (1A, 2A1, 2B2, etc.)
7. Generar mapa actualizado multi-rol

### Fase 2: Clasificación de Nodos

```typescript
// Enum de roles del CRM Soluciones Díaz
enum RolType {
  GUEST = 'NO_AUTENTICADO',          // Visitante sin login (18 URLs públicas)
  CLIENT = 'CLIENTE',                 // Usuario registrado
  ADMIN = 'ADMIN',                    // Jonathan (dueño negocio)
  SUPER_ADMIN = 'SUPER_ADMIN'        // Patricio (dev/creador)
}

// Enum de dispositivos (solo para ADMIN)
enum DispositivoType {
  DESKTOP = 'desktop',    // Panel admin desktop (servicios, materiales, calendario, etc.)
  MOBILE = 'mobile'       // Panel admin mobile (cotizaciones, clientes)
}

interface Nodo {
  id: string;                          // "2A1", "3A", "4B2"
  ruta: string;                        // "/cotizacion/rapida"
  rol: RolType;                        // GUEST | CLIENT | ADMIN | SUPER_ADMIN
  dispositivo?: DispositivoType;       // Solo para ADMIN (desktop | mobile)
  capa: number;                        // 1, 2, 3, 4
  tipo: "Puerta" | "Túnel" | "WiFi" | "Dashboard";

  // Estado documental
  tieneSkill: boolean;                 // ¿Existe skill Tipo 1?
  tieneRolDoc: boolean;                // ¿Existe ROL_*_*.md?
  completitudDoc: number;              // 0-100%

  // ARTERIAS detectadas
  esFrecuente: boolean;                // ¿Se accede >10 veces/día?
  enCadena: string[];                  // Otros nodos que se acceden después
  precargar: string[];                 // Skills a pre-cargar
}

// Función para clasificar rutas por rol
function clasificarPorRol(ruta: string): { rol: RolType, dispositivo?: DispositivoType } {
  // Super Admin (dev tools)
  if (ruta.startsWith('/dev-tools') ||
      ruta.startsWith('/super-admin') ||
      ruta.startsWith('/test-') ||
      ruta.startsWith('/debug') ||
      ruta.startsWith('/demo')) {
    return { rol: RolType.SUPER_ADMIN };
  }

  // Admin
  if (ruta.startsWith('/admin')) {
    // Detectar si es Desktop o Mobile por la ruta
    const adminMobileRoutes = ['/admin/cotizaciones', '/admin/clientes'];
    const esAdminMobile = adminMobileRoutes.some(r => ruta.startsWith(r));

    return {
      rol: RolType.ADMIN,
      dispositivo: esAdminMobile ? DispositivoType.MOBILE : DispositivoType.DESKTOP
    };
  }

  // Cliente
  if (ruta.startsWith('/mi-dashboard') ||
      ruta.startsWith('/cotizacion') ||
      ruta.startsWith('/cliente')) {
    return { rol: RolType.CLIENT };
  }

  // Guest (público) - incluye landing pages, legal, auth públicas
  const RUTAS_PUBLICAS_PATTERNS = [
    /^\/$/, // Homepage
    /^\/nosotros/, /^\/sobre-nosotros/, /^\/contacto/,
    /^\/servicios/, /^\/materiales/, /^\/galeria/,
    /^\/auth\/(signin|signup|error|refresh|verify)/,
    /^\/politica-privacidad/, /^\/terminos/, /^\/cookies/,
    /^\/blog\//, /^\/remodelacion-/, /^\/maestro-constructor-/, /^\/emergencia-/,
    /^\/calculadora/
  ];

  if (RUTAS_PUBLICAS_PATTERNS.some(pattern => pattern.test(ruta))) {
    return { rol: RolType.GUEST };
  }

  // Default: Guest
  return { rol: RolType.GUEST };
}
```

### Fase 3: Generación de Mapa Visual

**Output:** ASCII art + Mermaid diagram

```
🏗️ BLOCKCHAIN VIVIENTE - Estado: 26/10/2025

═══════════════════════════════════════════════════════════
ROL: 🔴 GUEST (NO AUTENTICADO) - 18 URLs públicas
═══════════════════════════════════════════════════════════
CAPA 0 (Páginas Públicas)
├─ 0A / (Homepage) ✅ COMPLETO
├─ 0B /servicios ✅ COMPLETO
├─ 0C /materiales ✅ COMPLETO
├─ 0D /blog/* (4 landings SEO) ✅ COMPLETO
└─ 0E /auth/signin ✅ COMPLETO

═══════════════════════════════════════════════════════════
ROL: 🔵 CLIENTE (Usuario Registrado)
═══════════════════════════════════════════════════════════
CAPA 1 (Puertas) - Acceso adicional sobre público
├─ 1A /cotizacion ✅ COMPLETO (puerta entrada)
└─ 1B /mi-dashboard ⚠️ PARCIAL (falta skill Tipo 1)

CAPA 2 (Túneles) - Funcionalidad específica
├─ 2A1 /cotizacion/rapida ⚠️ PARCIAL (falta skill, existe ROL doc)
├─ 2A2 /cotizacion/detallada ✅ COMPLETO
├─ 2B1 /mi-dashboard?tab=perfil ✅ COMPLETO
├─ 2B2 /mi-dashboard?tab=cotizaciones ✅ COMPLETO
├─ 2B3 /mi-dashboard?tab=mensajes ❌ SIN SKILL
└─ 2B4 /mi-dashboard?tab=notificaciones ❌ SIN SKILL

═══════════════════════════════════════════════════════════
ROL: 🟢 ADMIN (Jonathan - Dueño Negocio)
═══════════════════════════════════════════════════════════
CAPA 3 (Control) - Desktop + Mobile

3A - DESKTOP (Gestión completa)
├─ 3A /admin (dashboard) ⚠️ PARCIAL (falta skill + ARTERIA)
├─ 3A /admin/servicios ⚠️ PARCIAL (falta skill)
├─ 3A /admin/materiales ⚠️ PARCIAL (falta skill)
├─ 3A /admin/calendario ⚠️ PARCIAL (falta skill)
├─ 3A /admin/reportes ⚠️ PARCIAL (falta skill)
└─ 3A /admin/configuracion ⚠️ PARCIAL (falta skill)

3B - MOBILE (Gestión clientes/cotizaciones)
├─ 3B /admin/cotizaciones ✅ COMPLETO + ROL doc
└─ 3B /admin/clientes ⚠️ PARCIAL (falta skill)

═══════════════════════════════════════════════════════════
ROL: 🟣 SUPER ADMIN (Patricio - Dev/Creador)
═══════════════════════════════════════════════════════════
CAPA 4 (Dev Tools) - Acceso técnico total
├─ 4A /super-admin ✅ COMPLETO
├─ 4B /dev-tools/* ✅ COMPLETO
└─ 4C /test-* (54 rutas testing) ⚠️ PARCIAL (evaluar cleanup)

═══════════════════════════════════════════════════════════
SCORE GLOBAL: 62/100 🟠 REGULAR
═══════════════════════════════════════════════════════════
Breakdown por rol:
  GUEST: 90/100 ✅ (18/18 URLs documentadas)
  CLIENTE: 70/100 🟡 (ROL docs completas, faltan skills Tipo 1)
  ADMIN: 45/100 🔴 (Desktop sin skills, Mobile parcial)
  SUPER_ADMIN: 80/100 ✅ (Dev tools funcionando)
```

---

## 🔍 DETECCIÓN DE GAPS

### Categorías de Gaps

**1. Skills Tipo 1 Faltantes (CRÍTICO)**
```yaml
Detección:
  - Ruta existe en src/app/
  - NO existe skill en .claude/skills/
  - Alta frecuencia de acceso (logs)

Ejemplo:
  ❌ /admin (dashboard) - NO tiene skill
  ❌ /cotizacion/rapida - NO tiene skill
  ❌ /mi-dashboard?tab=mensajes - NO tiene skill

Impacto: Alto (agentes no saben cómo funciona)
Prioridad: P0 (crear antes de 2 semanas)
```

**2. Documentación ROL Incompleta**
```yaml
Detección:
  - Skill existe
  - ROL_*_*.md parcial o falta
  - <60% completitud

Ejemplo:
  ⚠️ ROL_ADMIN_DESKTOP_DASHBOARD.md - Solo 45% completo

Impacto: Medio (agentes tienen info parcial)
Prioridad: P1 (completar antes de 1 mes)
```

**3. Transacciones WiFi Sin Documentar**
```yaml
Detección:
  - Código tiene navegación A → B
  - NO está en blockchain-viviente
  - Aparece >5 veces en código

Ejemplo:
  ❌ WiFi: /admin/materiales ↔ /materiales (página pública)
  ❌ FLUJO: Admin cambia material → actualiza página pública

Impacto: Medio (flujo oculto)
Prioridad: P1
```

**4. ARTERIAS No Implementadas**
```yaml
Detección:
  - Nodo frecuente (>10 accesos/día)
  - Siempre carga mismas 3+ skills
  - NO existe ARTERIA pre-carga

Ejemplo:
  ⚡ SUGERENCIA: ARTERIA "admin-dashboard"
     Pre-cargar: blockchain-viviente + route-navigator + ROL_ADMIN_DESKTOP_DASHBOARD

Impacto: Alto (velocidad -80%)
Prioridad: P0 (ROI inmediato)
```

---

## 🤖 DELEGACIÓN INTELIGENTE

### Agentes Internos vs Externos

**Agentes INTERNOS (Blockchain):**
- Conocen el proyecto específico
- Documentados en .claude/skills/
- Ejemplos: route-navigator, blockchain-viviente, deploy-manager

**Agentes EXTERNOS (MCP):**
- Herramientas genéricas
- Ahorran tokens (~30k por delegación)
- Ejemplos: Firebase MCP, Playwright MCP, GitHub MCP

### Matriz de Delegación

```yaml
Tarea: "Consultar Firestore users collection"

ANTES (Agente interno):
  1. Cargar firebase-integrator skill (8k tokens)
  2. Cargar credenciales (.env.local)
  3. Escribir código temporal
  4. Ejecutar
  Total: ~12k tokens, 25s

DESPUÉS (Delegar a Firebase MCP):
  1. Llamar mcp__firebase__query("users")
  2. Resultado inmediato
  Total: ~200 tokens, 3s

  Ahorro: 12k tokens, 22s ✅
```

**Regla de Delegación:**
```typescript
function debeDelegar(tarea: string): boolean {
  const palabrasClave = ["firebase", "firestore", "playwright", "github", "browser"];
  const tieneKeyword = palabrasClave.some(k => tarea.toLowerCase().includes(k));
  const esTareaGenerica = !requiereContextoProyecto(tarea);

  return tieneKeyword && esTareaGenerica;
}
```

### Agentes Optimizables (Candidatos a Externo)

```yaml
CANDIDATOS A EXTERNALIZAR:

✅ firebase-integrator → Firebase MCP (ahorro ~30k tokens)
  Tareas delegables:
    - Queries Firestore simples
    - Auth user management
    - Storage uploads básicos

  Mantener interno para:
    - Lógica de negocio compleja
    - Reglas de seguridad específicas

✅ Playwright tests → Playwright MCP (ahorro ~20k tokens)
  Tareas delegables:
    - Testing básico de UI
    - Screenshots
    - Navegación simple

  Mantener interno para:
    - Flujos de usuario específicos (cotización detallada)

⚠️ github-* skills → GitHub MCP (evaluar)
  Actualmente: 5 skills separadas (github-setup, github-secrets, etc.)
  Potencial: Consolidar en GitHub MCP
  Ahorro: ~40k tokens acumulados
```

---

## ⚡ ARTERIAS IDENTIFICADAS (Soluciones Díaz)

### ARTERIA 1: admin-cotizaciones-mobile 🔥 CRÍTICA

**ROL:** ADMIN (Jonathan)
**Dispositivo:** MOBILE
**Trigger:** Usuario menciona "cotizaciones admin", "gestión cotizaciones", "panel admin" + contexto mobile

**Pre-carga (paralelo, 3s total):**
```yaml
Skills específicas ROL ADMIN:
  - blockchain-viviente-soluciones-diaz (solo sección ADMIN)
  - route-navigator (filtro ADMIN + MOBILE)
  - ROL_ADMIN_MOBILE_COTIZACIONES.md
  - cotizaciones-admin-cliente-context

Queries Firestore (cache 5 min):
  - quotes WHERE status IN ['NUEVO', 'LEIDA', 'FINALIZADA'] LIMIT 50
  - users WHERE role = 'client' (nombres rápidos para cards)

Configuración específica MOBILE:
  - Mobile-first flag: true
  - Touch targets: ≥44px (requisito mobile)
  - View mode: cards (optimizado móvil)
  - Sistema 3 estados (NUEVAS/LEIDAS/FINALIZADAS)
```

**Beneficio:**
- Velocidad: 28s → 3s (9.3x) ✅
- Tokens: 45k → 8k (5.6x menos) ✅
- Contexto específico: Solo ADMIN Mobile (no carga Desktop docs)

---

### ARTERIA 2: cliente-dashboard 🎯 IMPORTANTE

**ROL:** CLIENT (Usuario Registrado)
**Dispositivo:** MOBILE (responsive)
**Trigger:** Usuario menciona "mi dashboard", "perfil cliente", "mis cotizaciones"

**Pre-carga (paralelo, 2s total):**
```yaml
Skills específicas ROL CLIENT:
  - blockchain-viviente (solo sección CLIENTE)
  - route-navigator (filtro rol CLIENTE)
  - ROL_CLIENTE_MAPA_COMPLETO.md
  - ROL_CLIENTE_CAPA_2B1_MI_PERFIL.md (si tab=perfil)
  - ROL_CLIENTE_CAPA_2B2_MIS_COTIZACIONES.md (si tab=cotizaciones)

Queries Firestore (cache 2 min):
  - user doc (perfil completo)
  - quotes WHERE clientId = uid LIMIT 10

Configuración específica CLIENT:
  - Mobile responsive: true
  - Tabs: [perfil, cotizaciones, mensajes, notificaciones]
  - Vista: Cliente solo ve sus propias cotizaciones
```

**Beneficio:**
- Velocidad: 18s → 2s (9x) ✅
- Tokens: 32k → 6k (5.3x menos) ✅

---

### ARTERIA 3: deploy-workflow ⚡ CRÍTICA

**ROL:** SUPER_ADMIN (Patricio - Dev/Creador)
**Trigger:** Usuario menciona "deploy", "publicar", "subir cambios", "producción"

**Pre-carga (paralelo, 1s total):**
```yaml
Skills específicas ROL SUPER_ADMIN:
  - deploy-manager
  - firebase-deploy-helper
  - firebase-tooling-reference
  - production-troubleshooter

Estado Git (pre-analizado):
  - git status (archivos modificados)
  - git diff (tipo de cambios)
  - git log -1 (último commit)

Decisión automática script:
  - deploy-rapido.bat (si <5 archivos, solo src/app/)
  - deploy-to-firebase-pro.bat (si package.json cambió)
  - deploy-to-firebase.bat (fallback)
```

**Beneficio:**
- Velocidad: 15s análisis → 1s decisión (15x) ✅
- No preguntar: Decisión automática basada en reglas
- Tiempo total: 12-25 min deploy (no cambia, pero decisión instantánea)

---

### ARTERIA 4: troubleshooting-produccion 🚨 URGENTE

**ROL:** SUPER_ADMIN (Patricio - Dev/Creador)
**Trigger:** Usuario menciona "error producción", "no funciona", "bug", "problema"

**Pre-carga (paralelo, 2s total):**
```yaml
Skills específicas ROL SUPER_ADMIN:
  - production-debugger
  - auth-troubleshooter
  - firebase-auth-helper
  - security-guardian

Logs recientes (pre-fetch):
  - Firebase Functions logs (últimos 30 min)
  - Client errors (window.onerror últimos 30 min)
  - API /health check status

Estado producción:
  - https://soluciones-diaz-9a7c1.web.app (ping)
  - APIs críticas (/api/admin/quotes, /api/client/profile)
  - APIs por ROL (guest, client, admin, super_admin)
```

**Beneficio:**
- Velocidad: 35s → 2s (17.5x) ✅
- Diagnóstico: Inmediato (logs ya cargados)
- MTTR: Mean Time To Repair -80%

---

### ARTERIA 5: analytics-deep-dive 📊 OPCIONAL

**ROL:** ADMIN (Jonathan - Dueño Negocio) o SUPER_ADMIN
**Dispositivo:** DESKTOP (análisis complejo)
**Trigger:** Usuario menciona "analytics", "métricas", "estadísticas", "conversión"

**Pre-carga (paralelo, 4s total):**
```yaml
Skills específicas ROL ADMIN/SUPER_ADMIN:
  - analytics-insights-specialist (con extended thinking 32k)
  - admin-workflow-specialist

Datos GA4 (cache 1h):
  - Eventos últimos 7 días
  - Conversion funnels
  - Top páginas

Queries Firestore (segmentadas por rol):
  - quotes COUNT por estado
  - users COUNT nuevos (últimos 30 días)
  - Métricas por rol (Guest → Client conversion)
```

**Beneficio:**
- Velocidad: 45s → 4s (11.2x) ✅
- Extended thinking: Análisis profundo pre-calculado

---

## 📊 SISTEMA DE SCORING (0-100)

### Fórmula de Health Score

```typescript
function calcularHealthScore(): number {
  const scores = {
    documentacion: calcDocumentacionScore(),      // 30%
    arterias: calcArteriasScore(),                // 25%
    gaps: calcGapsScore(),                        // 20%
    delegacion: calcDelegacionScore(),            // 15%
    testing: calcTestingScore(),                  // 10%
  };

  return (
    scores.documentacion * 0.30 +
    scores.arterias * 0.25 +
    scores.gaps * 0.20 +
    scores.delegacion * 0.15 +
    scores.testing * 0.10
  );
}

// Breakdown multi-rol
function calcularHealthScorePorRol(): Record<RolType, number> {
  return {
    [RolType.GUEST]: calcHealthForRole(RolType.GUEST),
    [RolType.CLIENT]: calcHealthForRole(RolType.CLIENT),
    [RolType.ADMIN]: calcHealthForRole(RolType.ADMIN),
    [RolType.SUPER_ADMIN]: calcHealthForRole(RolType.SUPER_ADMIN),
  };
}

function calcHealthForRole(rol: RolType): number {
  const nodosRol = filtrarNodosPorRol(rol);
  const documentados = nodosRol.filter(n => n.tieneSkill || n.tieneRolDoc);
  const completitud = documentados.length / nodosRol.length;

  return Math.round(completitud * 100);
}

// Detalles de cada subscore

function calcDocumentacionScore(): number {
  const totalNodos = contarNodosBlockchain();
  const nodosDocumentados = contarNodosConSkill();
  const completitud = nodosDocumentados / totalNodos;

  return completitud * 100; // 0-100
}

function calcArteriasScore(): number {
  const nodosFreCuentes = identificarNodosFrecuentes(); // >10 accesos/día
  const arteriasImplementadas = contarArterias();
  const cobertura = arteriasImplementadas / nodosFrecuentes.length;

  return cobertura * 100;
}

function calcGapsScore(): number {
  const gapsCriticos = contarGaps("P0");
  const gapsImportantes = contarGaps("P1");
  const penalty = (gapsCriticos * 10) + (gapsImportantes * 5);

  return Math.max(0, 100 - penalty);
}

// Gap detection por rol
interface Gap {
  id: string;
  ruta: string;
  rol: RolType;
  dispositivo?: DispositivoType;
  tipo: "skill-faltante" | "arteria-faltante" | "wifi-no-documentado" | "doc-incompleta";
  prioridad: "P0" | "P1" | "P2";
  impacto: string;
  accion: string;
  esfuerzo: string;
}

function detectarGapsPorRol(): Record<RolType, Gap[]> {
  const todosLosGaps = detectarTodosLosGaps();

  return {
    [RolType.GUEST]: todosLosGaps.filter(g => g.rol === RolType.GUEST),
    [RolType.CLIENT]: todosLosGaps.filter(g => g.rol === RolType.CLIENT),
    [RolType.ADMIN]: todosLosGaps.filter(g => g.rol === RolType.ADMIN),
    [RolType.SUPER_ADMIN]: todosLosGaps.filter(g => g.rol === RolType.SUPER_ADMIN),
  };
}

function calcDelegacionScore(): number {
  const tareasGenericasPorAgentesInternos = contarTareasGenericasInternas();
  const potencialDelegacion = identificarCandidatosExternos().length;
  const eficiencia = 1 - (tareasGenericasPorAgentesInternos / potencialDelegacion);

  return eficiencia * 100;
}

function calcTestingScore(): number {
  const rutasCriticas = identificarRutasCriticas();
  const rutasConTest = contarRutasConPlaywrightTest();
  const cobertura = rutasConTest / rutasCriticas.length;

  return cobertura * 100;
}
```

### Interpretación del Score

```yaml
90-100: 🟢 EXCELENTE - Ambiente perfecto, todo optimizado
75-89:  🟡 BUENO - Funcionando bien, mejoras menores
60-74:  🟠 REGULAR - Gaps importantes, ARTERIAS faltantes
40-59:  🔴 CRÍTICO - Documentación incompleta, urgente mejorar
0-39:   ⚫ EMERGENCIA - Proyecto sin estructura, refactoring necesario
```

---

## 🔄 AUTO-ACTUALIZACIÓN

### Hook Post-Commit

**Archivo:** `.claude/hooks/ambiente-perfecto-sync.js`

```javascript
// Ejecuta después de cada git commit importante

const TRIGGERS_IMPORTANTE = [
  "feat:", "fix:", "docs:", "refactor:",
  /\.md$/, // Archivos markdown
  /^src\/app\//, // Rutas nuevas
  /\.claude\/skills\//, // Skills nuevos
];

async function ejecutarAmbientePerfecto() {
  console.log("🌟 Ambiente Perfecto - Auto-actualización iniciada");

  // 1. Escanear cambios
  const cambios = await analizarCommit();

  // 2. Actualizar mapa blockchain
  if (cambios.includes("src/app/")) {
    await actualizarMapaBlockchain();
  }

  // 3. Detectar nuevos gaps
  const gaps = await detectarGaps();

  // 4. Re-calcular health score
  const nuevoScore = await calcularHealthScore();

  // 5. Generar reporte
  await generarReporte({
    timestamp: new Date(),
    score: nuevoScore,
    gaps: gaps,
    cambios: cambios,
  });

  console.log(`✅ Health Score: ${nuevoScore}/100`);
}
```

### Frecuencia de Ejecución

```yaml
Automático:
  - Después de cada git commit con keywords importantes
  - Cada 2 horas (cron) si hay sesión activa
  - Al abrir nueva sesión Claude Code

Manual (a demanda):
  - Usuario escribe "ambiente perfecto"
  - Usuario escribe "health check"
  - Usuario escribe "qué falta"
```

---

## 📈 REPORTES GENERADOS

### Reporte Diario

**Archivo:** `.claude/reports/ambiente-perfecto/YYYY-MM-DD.md`

```markdown
# Ambiente Perfecto - Reporte 26/10/2025

## 📊 Health Score: 78/100 🟡 BUENO

### Breakdown General:
- Documentación: 85/100 ✅
- ARTERIAS: 60/100 ⚠️
- Gaps: 75/100 🟡
- Delegación: 70/100 🟡
- Testing: 50/100 🔴

### 📊 Breakdown por ROL:

```yaml
🔴 GUEST (NO AUTENTICADO): 90/100 ✅
  - Rutas: 18 URLs públicas
  - Documentadas: 16/18 (88%)
  - Gaps P0: 0
  - Estado: EXCELENTE - Marketing bien documentado

🔵 CLIENTE (Usuario Registrado): 70/100 🟡
  - Rutas: 9 URLs
  - Documentadas: 6/9 (66%)
  - Gaps P0: 2 (cotización rápida, mi dashboard)
  - Estado: BUENO - Falta detalle en skills Tipo 1

🟢 ADMIN (Jonathan - Dueño): 45/100 🔴
  - Rutas Desktop: 8 URLs
  - Rutas Mobile: 2 URLs
  - Documentadas: 4/10 (40%)
  - Gaps P0: 4 (dashboard, cotizaciones mobile, clientes, reportes)
  - Estado: CRÍTICO - Panel admin necesita skills urgente

🟣 SUPER_ADMIN (Patricio - Dev): 80/100 ✅
  - Rutas: 54 dev/testing
  - Documentadas: 42/54 (77%)
  - Gaps P0: 1 (troubleshooting production)
  - Estado: BUENO - Herramientas dev bien documentadas
```

---

## 🔍 Gaps Detectados por ROL (Total: 12)

### 🔵 ROL CLIENTE - P0 CRÍTICO (2)
1. ❌ /cotizacion/rapida - Skill Tipo 1 incompleta
   - Impacto: Alto (flujo crítico conversión)
   - Acción: Completar skill antes 1 semana

2. ❌ /mi-dashboard - Skill Tipo 1 faltante
   - Impacto: Alto (hub principal cliente)
   - Acción: Crear skill antes 1 semana

### 🟢 ROL ADMIN - P0 CRÍTICO (4)
1. ❌ /admin (dashboard) - NO tiene skill Tipo 1
   - Dispositivo: DESKTOP
   - Impacto: Alto (nodo central admin)
   - Acción: Crear skill antes 5 días

2. ❌ /admin/cotizaciones - Skill Mobile incompleta
   - Dispositivo: MOBILE
   - Impacto: CRÍTICO (nodo más frecuente)
   - Acción: Implementar ARTERIA + skill antes 3 días

3. ❌ /admin/clientes - NO documentada
   - Dispositivo: MOBILE
   - Impacto: Alto
   - Acción: Crear skill antes 2 semanas

4. ❌ WiFi /admin/materiales ↔ /materiales - NO documentada
   - Dispositivo: DESKTOP (admin) ↔ Público
   - Impacto: Flujo oculto entre admin y público
   - Acción: Documentar transacción WiFi

### 🟣 ROL SUPER_ADMIN - P0 CRÍTICO (1)
1. ❌ ARTERIA troubleshooting-produccion - NO implementada
   - Impacto: CRÍTICO (-80% MTTR)
   - Acción: Implementar URGENTE antes 48h

### 🔴 ROL GUEST - P1 IMPORTANTE (3)
1. ⚠️ /servicios - Documentación parcial
2. ⚠️ /galeria - Falta skill optimización imágenes
3. ⚠️ /calculadora - Skill básica, falta métricas

### P2 - MENOR (2)
...

---

## ⚡ ARTERIAS Activas (3/5 recomendadas)

✅ admin-cotizaciones (speedup 9.3x)
✅ cliente-dashboard (speedup 9x)
✅ deploy-workflow (speedup 15x)
❌ troubleshooting-produccion (PENDIENTE)
❌ analytics-deep-dive (PENDIENTE)

---

## 🤖 Delegación Optimizable

Tareas que podrían usar agentes externos:

1. Queries Firestore simples → Firebase MCP (ahorro ~8k tokens/query)
2. Testing UI básico → Playwright MCP (ahorro ~15k tokens/test)
3. GitHub operations → GitHub MCP (ahorro ~12k tokens/op)

Ahorro potencial mensual: ~200k tokens

---

## 🎯 Próximas Acciones Priorizadas (por ROL)

### 🟢 ROL ADMIN - P0 CRÍTICO
1. [P0] Crear skill /admin (dashboard DESKTOP) - ROI: Alto - 2h
2. [P0] Implementar ARTERIA admin-cotizaciones (MOBILE) - ROI: 9.3x speedup - 1h
3. [P0] Documentar /admin/clientes (MOBILE) - ROI: Medio - 1.5h
4. [P0] Documentar WiFi /admin/materiales ↔ /materiales - ROI: Medio - 1h

### 🔵 ROL CLIENTE - P0 CRÍTICO
5. [P0] Completar skill /cotizacion/rapida - ROI: Alto - 1.5h
6. [P0] Crear skill /mi-dashboard - ROI: Alto - 2h
7. [P0] Implementar ARTERIA cliente-dashboard - ROI: 9x speedup - 45min

### 🟣 ROL SUPER_ADMIN - P0 CRÍTICO
8. [P0] Implementar ARTERIA troubleshooting-produccion - ROI: CRÍTICO (17.5x) - 2.5h
9. [P0] Implementar ARTERIA deploy-workflow - ROI: 15x speedup - 1h

### Optimizaciones Generales - P1
10. [P1] Delegar queries Firestore a Firebase MCP - ROI: Alto (ahorro tokens) - 1h

---

Generado automáticamente por ambiente-perfecto skill
```

---

## 🚀 FLUJO DE USO

### Caso 1: Usuario pregunta "¿Qué falta documentar?"

```yaml
Trigger: ambiente-perfecto skill

Ejecución:
  1. Auto-mapear blockchain (Glob src/app/, Read blockchain-viviente)
  2. Clasificar rutas por ROL (Guest, Client, Admin, Super Admin)
  3. Detectar gaps por rol (comparar nodos reales vs documentados)
  4. Clasificar por prioridad (P0, P1, P2)
  5. Calcular impacto (frecuencia acceso, centralidad)
  6. Generar lista priorizada por ROL

Output:
  📊 Gaps detectados: 12 (desglosados por ROL)

  🟢 ROL ADMIN - Gaps P0: 4 críticos
    1. ❌ /admin (dashboard) - CREAR SKILL
       Dispositivo: DESKTOP
       Razón: Nodo central, >50 accesos/día
       Estimación: 2h trabajo
       ROI: Alto

  🔵 ROL CLIENTE - Gaps P0: 2 críticos
    2. ❌ /cotizacion/rapida - COMPLETAR SKILL
       Razón: Flujo conversión crítico
       Estimación: 1.5h trabajo
       ROI: Alto

  🟣 ROL SUPER_ADMIN - Gaps P0: 1 crítico
    3. ❌ ARTERIA troubleshooting-produccion - IMPLEMENTAR
       Razón: Speedup 17.5x, MTTR -80%
       Estimación: 2.5h trabajo
       ROI: CRÍTICO
```

---

### Caso 2: Usuario pregunta "¿Cómo optimizar velocidad?"

```yaml
Trigger: ambiente-perfecto skill (modo ARTERIAS)

Ejecución:
  1. Identificar nodos frecuentes (logs de acceso)
  2. Analizar cadenas de skills cargadas juntas
  3. Calcular tiempo actual vs potencial
  4. Sugerir ARTERIAS por ROI

Output:
  ⚡ ARTERIAS sugeridas: 5 (segmentadas por ROL)

  🟢 ROL ADMIN:
    1. 🔥 admin-cotizaciones-mobile (CRÍTICA)
       Speedup: 9.3x (28s → 3s)
       Ahorro tokens: 37k → 8k
       Dispositivo: MOBILE
       ROI: Inmediato
       Esfuerzo: 1h implementación

  🔵 ROL CLIENTE:
    2. 🎯 cliente-dashboard (IMPORTANTE)
       Speedup: 9x (18s → 2s)
       Ahorro tokens: 32k → 6k
       Dispositivo: MOBILE (responsive)
       ROI: Alto
       Esfuerzo: 45min implementación

  🟣 ROL SUPER_ADMIN:
    3. ⚡ deploy-workflow (CRÍTICA)
       Speedup: 15x (15s → 1s decisión)
       ROI: Inmediato
       Esfuerzo: 1h

    4. 🚨 troubleshooting-produccion (URGENTE)
       Speedup: 17.5x (35s → 2s)
       MTTR: -80%
       ROI: CRÍTICO
       Esfuerzo: 2.5h

  🟡 Multi-ROL:
    5. 📊 analytics-deep-dive (OPCIONAL)
       ROL: ADMIN o SUPER_ADMIN
       Speedup: 11.2x
       Esfuerzo: 1h

  Implementando las 5 ARTERIAS:
    - Velocidad promedio: +850% (8.5x más rápido) ✅
    - Tokens ahorrados: ~180k/mes
    - Esfuerzo total: 6.5h desarrollo
```

---

### Caso 3: Usuario pregunta "¿Delegar a agentes externos?"

```yaml
Trigger: ambiente-perfecto skill (modo DELEGACIÓN)

Ejecución:
  1. Analizar skills actuales
  2. Identificar tareas genéricas (no específicas proyecto)
  3. Comparar con MCPs disponibles
  4. Calcular ahorro potencial

Output:
  🤖 Delegaciones optimizables: 7

  ✅ firebase-integrator → Firebase MCP
     Tareas delegables:
       - Queries simples (GET users, quotes)
       - Auth management básico

     Ahorro: ~30k tokens/mes
     Mantener interno:
       - Lógica negocio compleja
       - Security rules específicas

  ✅ Playwright tests → Playwright MCP
     Tareas delegables:
       - Testing UI básico
       - Screenshots

     Ahorro: ~20k tokens/mes
     Mantener interno:
       - Flujos usuario complejos

  Total ahorro potencial: ~200k tokens/mes (~$4/mes) ✅
```

---

## 🎓 ALGORITMO PRINCIPAL

```typescript
async function ejecutarAmbientePerfecto(modo: string = "full"): Promise<Report> {
  console.log("🌟 Ambiente Perfecto iniciando...");

  // FASE 1: Auto-mapeo multi-rol
  const mapaActual = await autoMapearBlockchain();
  const nodosReales = await escanearSrcApp();

  // Clasificar nodos por ROL (Guest, Client, Admin, Super Admin)
  const nodosPorRol = clasificarNodosPorRol(nodosReales);

  // FASE 2: Detección de gaps por rol
  const gaps = detectarGaps(mapaActual, nodosReales);
  const gapsPorRol = detectarGapsPorRol(); // Segmentados por rol
  const gapsPriorizados = priorizarPorImpacto(gaps);

  // FASE 3: Identificar ARTERIAS por rol
  const nodosFrecuentes = await analizarLogsAcceso();
  const arteriasActuales = await listarArterias();
  const arteriasSugeridas = sugerirArteriasPorRol(nodosFrecuentes, arteriasActuales);

  // FASE 4: Análisis de delegación
  const skillsActuales = await listarSkills();
  const delegacionesOptimas = analizarDelegacion(skillsActuales);

  // FASE 5: Cálculo de health score
  const healthScore = calcularHealthScore({
    documentacion: mapaActual,
    gaps: gaps,
    arterias: arteriasActuales,
    delegacion: delegacionesOptimas,
  });

  // Cálculo por rol
  const healthScorePorRol = calcularHealthScorePorRol();

  // FASE 6: Generación de reporte multi-rol
  const reporte = generarReporte({
    score: healthScore,
    scorePorRol: healthScorePorRol,
    nodosPorRol: nodosPorRol,
    gaps: gapsPriorizados,
    gapsPorRol: gapsPorRol,
    arterias: arteriasSugeridas,
    delegaciones: delegacionesOptimas,
    proximasAcciones: priorizarAccionesPorRol(),
  });

  await guardarReporte(reporte);

  console.log(`✅ Health Score General: ${healthScore}/100`);
  console.log(`   🔴 GUEST: ${healthScorePorRol[RolType.GUEST]}/100`);
  console.log(`   🔵 CLIENT: ${healthScorePorRol[RolType.CLIENT]}/100`);
  console.log(`   🟢 ADMIN: ${healthScorePorRol[RolType.ADMIN]}/100`);
  console.log(`   🟣 SUPER_ADMIN: ${healthScorePorRol[RolType.SUPER_ADMIN]}/100`);

  return reporte;
}
```

---

## 📚 RECURSOS

**Skills relacionadas:**
- blockchain-viviente-soluciones-diaz (base de mapeo)
- route-navigator (rutas por rol)
- deploy-manager (ARTERIA deploy)
- firebase-tooling-reference (delegación Firebase)

**Archivos clave:**
- `.claude/skills/ambiente-perfecto/SKILL.md` (este archivo)
- `.claude/reports/ambiente-perfecto/` (reportes diarios)
- `.claude/hooks/ambiente-perfecto-sync.js` (auto-actualización)

**Inspiración:**
- dak-chain-ia/DEVELOPER_PACK_UNIVERSAL.md
- Concepto ARTERIAS (pre-carga predictiva)
- Sistema auto-evolutivo

---

## ✅ VALIDACIÓN

**Criterios de éxito:**
- ✅ Health score >75 → Sistema saludable
- ✅ Gaps P0 = 0 → Documentación crítica completa
- ✅ ARTERIAS top 3 implementadas → Velocidad optimizada
- ✅ Delegación >50% → Eficiencia tokens maximizada

**Fecha creación:** 26-10-2025
**Última actualización:** 26-10-2025 (Multi-rol: 4 roles support)
**Autor:** Patricio (con referencia dak-chain-ia)
**Status:** ✅ Activo

**Versión:** 2.0
**Changelog:**
- v2.0 (26-10-2025): Soporte multi-rol (Guest, Client, Admin, Super Admin)
  - Detección automática de rol por ruta
  - Device detection (Desktop vs Mobile para Admin)
  - Breakdown de health score por rol
  - Gap detection segmentado por rol
  - ARTERIAS específicas por rol
- v1.0 (26-10-2025): Versión inicial básica (solo 2 roles)
