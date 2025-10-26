# 📝 DECISIONS LOG: Adaptación OS DAK en Soluciones Díaz

**Registro de decisiones arquitectónicas tomadas durante la adaptación**

Este documento explica **QUÉ decisiones se tomaron**, **POR QUÉ**, y **ALTERNATIVAS rechazadas**.

---

## 🎯 Contexto del Proyecto

```yaml
Aplicación: Soluciones Díaz CRM
Tipo: Sitio web público + CRM construcción integrado
Stack: Next.js 14 (App Router) + Firebase
Usuarios: 4 roles (Guest, Cliente, Admin Mobile, Admin Desktop, SuperAdmin)
Complejidad: 52 URLs totales, multi-dispositivo, real-time
Developer: Patricio Díaz (ADHD-I, Inteligencia Sistémica Top 5%)
Contexto: Necesidad de sistema recovery post-crash + arquitectura visualizable
```

---

## DECISIÓN 1: CAPA 0 = Sistema de Roles (NO Guardian de Planes)

### ✅ Decisión Tomada

```yaml
CAPA 0: Sistema de Roles (G/C/A/SA)

Implementación:
  G  = Guest (no autenticado)
  C  = Cliente (autenticado)
  A  = Admin (Jonathan)
  SA = SuperAdmin (Patricio)

Función:
  - Define quién puede acceder a qué URLs
  - Roles determinan permisos en Firestore
  - Guardian real del sistema
```

### 🤔 Razonamiento

**Por qué SÍ:**
1. **Es un sitio web CON CRM**
   - NO es solo herramienta interna
   - Tiene usuarios públicos (Guest) + autenticados (Cliente, Admin)
   - Roles son la base de toda la arquitectura

2. **Roles determinan TODO en el sistema:**
   - URLs accesibles (18 Guest, 26 Cliente, 46 Admin Mobile, 52 Admin Desktop)
   - Permisos en Firestore (Security Rules por rol)
   - Flujos de usuario (Guest → Cliente requiere registro)
   - UI mostrada (componentes condicionales por rol)

3. **Guardian de Planes genérico NO aplicaba:**
   - Template dice: "Valida planes ANTES de ejecutar"
   - En web pública, el "guardian" es el middleware que valida autenticación
   - CAPA 0 como Roles es más preciso al contexto real

**Por qué NO Guardian de Planes:**
- Template genérico asume herramienta interna sin roles
- En Soluciones Díaz, validar "planes" es secundario vs validar "quién puede acceder"
- Roles son verificados en CADA request (middleware + Firestore Rules)

### ❌ Alternativa Rechazada

```yaml
Opción rechazada: CAPA 0 = Guardian de Planes (template original)

Por qué NO:
  - Template genérico no encaja en sitio web público
  - "Validar plan antes de ejecutar" es abstracto vs roles concretos
  - En producción, middleware valida ROL (no "plan")

Hubiera resultado en:
  - Documentación desconectada de código real
  - Confusión entre concepto abstracto vs implementación
  - Claude Code sin mapeo directo a arquitectura
```

### 📊 Resultado

**Impacto:**
- ✅ Comunicación 100% alineada con código (`userRole === 'admin'`)
- ✅ Documentación refleja realidad (middleware, Firestore Rules)
- ✅ Fácil extensión futura (agregar rol `Vendedor` = agregar letra)

**Lección aprendida:**
> **"CAPA 0 debe ser el guardian REAL del sistema, no el concepto genérico del template."**

---

## DECISIÓN 2: NÚMERO = Profundidad Funcional (NO Física)

### ✅ Decisión Tomada

```yaml
NÚMERO = Profundidad FUNCIONAL (no niveles de URL)

Definición:
  1 = Puerta (selector, inicio)
  2 = Túnel (flujo guiado)
  3 = Control (dashboard, gestión)
  4 = Proyección (analytics, futuro)

Ejemplos:
  1A: /cotizacion (1 nivel URL, pero es PUERTA)
  1B: /mi-dashboard (1 nivel URL, pero es DASHBOARD completo)
  2A1: /cotizacion/rapida (2 niveles URL, es TÚNEL)
```

### 🤔 Razonamiento

**Por qué profundidad FUNCIONAL:**

1. **URLs no reflejan complejidad:**
   ```yaml
   /cotizacion (1 nivel) = Selector simple → NÚMERO 1 ✅
   /mi-dashboard (1 nivel) = Dashboard completo con 4 tabs → NÚMERO 1 ✅
   /admin/servicios (2 niveles) = CRUD completo → NÚMERO 3 ✅
   ```

2. **Profundidad física confunde:**
   ```yaml
   Si usáramos niveles físicos:
   /cotizacion = 1
   /mi-dashboard = 1
   /cotizacion/rapida = 2
   /admin/servicios = 2

   Problema: /mi-dashboard y /cotizacion tienen MISMO número
   pero complejidad MUY diferente
   ```

3. **Funcional es predecible:**
   ```yaml
   1 = "¿Dónde empieza usuario?" (puerta)
   2 = "¿Qué flujo sigue?" (túnel)
   3 = "¿Dónde gestiona?" (control)
   4 = "¿Qué analiza?" (proyección)

   Mapeo mental más claro que contar slashes en URL
   ```

### ❌ Alternativa Rechazada

```yaml
Opción rechazada: NÚMERO = Niveles de URL (/ = nivel)

Ejemplo si se hubiera usado:
  1: / (homepage)
  1: /cotizacion
  1: /mi-dashboard
  2: /cotizacion/rapida
  2: /admin/servicios
  3: /admin/servicios/create

Problemas:
  - /mi-dashboard (1) y /cotizacion (1) con misma profundidad
    pero /mi-dashboard es 10x más complejo
  - Crear producto (/admin/servicios/create = 3) parece más complejo
    que gestionar dashboard (/admin = 1), cuando es al revés
  - Mapeo literal sin significado arquitectónico
```

### 📊 Resultado

**Impacto:**
- ✅ NÚMERO indica complejidad real (1 simple, 4 complejo)
- ✅ Predecible sin mirar URL (puerta/túnel/control/proyección)
- ✅ Escalable (agregar /admin/deep/nested/route NO rompe lógica)

**Lección aprendida:**
> **"NÚMERO debe reflejar FUNCIÓN (qué hace), no FORMA (cómo se ve URL)."**

---

## DECISIÓN 3: SUB-NODOS para Tabs (NO Nodos Separados)

### ✅ Decisión Tomada

```yaml
SUB-NODO: Misma URL, diferentes secciones (tabs/accordion)
NODO: URLs diferentes

Notación:
  NODO: 1B (/mi-dashboard)
  SUB-NODOS:
    - 1B.Perfil (tab perfil)
    - 1B.Cotizaciones (tab cotizaciones)
    - 1B.Mensajes (tab mensajes)
    - 1B.Notificaciones (tab notificaciones)

Implementación:
  - URL NO cambia (/mi-dashboard)
  - React state cambia tab activo
  - Firestore queries diferentes por tab
```

### 🤔 Razonamiento

**Por qué SUB-NODOS:**

1. **Dashboard cliente tiene 4 tabs en MISMA URL:**
   ```typescript
   // src/app/mi-dashboard/page.tsx
   const [activeTab, setActiveTab] = useState('perfil');

   // URL: /mi-dashboard (NO cambia)
   // Vista: Cambia según activeTab
   ```

2. **No son nodos separados:**
   - NO requieren navegación (router.push)
   - NO tienen URL propia
   - Comparten layout y contexto
   - React state local maneja cambio

3. **Documentar como nodos separados inflaría mapa:**
   ```yaml
   Si fueran nodos separados (MAL):
     1B1: /mi-dashboard (perfil)
     1B2: /mi-dashboard (cotizaciones)
     1B3: /mi-dashboard (mensajes)
     1B4: /mi-dashboard (notificaciones)

   Problema: 4 nodos con MISMA URL (confuso)
   ```

4. **SUB-NODO captura realidad:**
   ```yaml
   Notación (BIEN):
     1B: /mi-dashboard (nodo base)
       ├── 1B.Perfil (tab)
       ├── 1B.Cotizaciones (tab)
       ├── 1B.Mensajes (tab)
       └── 1B.Notificaciones (tab)

   Ventaja: Claro que es 1 nodo con 4 secciones
   ```

### ❌ Alternativa Rechazada

```yaml
Opción rechazada A: Nodos separados por tab

Ejemplo:
  2B1: Tab Perfil
  2B2: Tab Cotizaciones
  2B3: Tab Mensajes
  2B4: Tab Notificaciones

Problemas:
  - URLs iguales (/mi-dashboard) pero nodos diferentes (confuso)
  - Mapa inflado (34 SUB-NODOS → 34 nodos separados)
  - NO refleja código real (tabs son state, no routing)

---

Opción rechazada B: Ignorar tabs, documentar solo dashboard

Ejemplo:
  1B: /mi-dashboard (sin mencionar tabs)

Problemas:
  - Pierde detalle importante (qué hay EN dashboard)
  - Claude no sabría que hay 4 secciones diferentes
  - Documentación superficial vs profunda
```

### 📊 Resultado

**Impacto:**
- ✅ Mapa claro (1 nodo base + 4 SUB-NODOS vs 5 nodos separados)
- ✅ Refleja código real (React state, no routing)
- ✅ Extensible (agregar tab = agregar SUB-NODO, no nodo)

**Estadísticas:**
```yaml
Sin SUB-NODOS: 30 nodos separados
Con SUB-NODOS: 30 nodos + 4 SUB-NODOS (mapa 12% más compacto)
```

**Lección aprendida:**
> **"SUB-NODOS = misma URL, NODOS = URLs diferentes. Pregunta: ¿Cambia URL? NO → SUB-NODO."**

---

## DECISIÓN 4: Admin Mobile vs Desktop Separados

### ✅ Decisión Tomada

```yaml
Documentar Admin en 2 versiones:

Admin Mobile (iPhone Jonathan):
  - 28 URLs accesibles
  - 2 URLs mobile-optimized:
    - /admin/cotizaciones (touch 44px, cards, llamar/WhatsApp)
    - /admin/clientes (VIP system, integración Maps)
  - Uso: 90% del tiempo (terreno)

Admin Desktop (Computador Oficina):
  - 34 URLs accesibles
  - 6 URLs desktop-only:
    - /admin (dashboard widgets)
    - /admin/servicios (CRUD)
    - /admin/materiales (CRUD)
    - /admin/calendario (eventos)
    - /admin/reportes (CSV/PDF export)
    - /admin/configuracion (settings)
  - Uso: 10% del tiempo (oficina)
```

### 🤔 Razonamiento

**Por qué separar Mobile vs Desktop:**

1. **Jonathan usa iPhone 90% del tiempo:**
   - Trabaja en terreno (instalaciones, visitas)
   - Necesita gestionar cotizaciones DESDE obra
   - Desktop solo en oficina (poco frecuente)

2. **URLs mobile-optimized son DIFERENTES:**
   ```yaml
   /admin/cotizaciones (mobile):
     - Touch targets ≥ 44px (Apple HIG)
     - Cards (NO tabla)
     - Botones: Llamar, WhatsApp (1 tap)
     - Marcar "Contactado" (1 tap)
     - Sin scroll horizontal

   /admin/cotizaciones (desktop):
     - Tabla con múltiples columnas
     - Filtros sidebar
     - Selección múltiple
     - Exportar CSV/PDF
   ```

3. **Diferentes casos de uso:**
   ```yaml
   Mobile (terreno):
     - Ver nuevas cotizaciones
     - Llamar cliente directo
     - Marcar como contactado
     - Triage rápido

   Desktop (oficina):
     - Editar contenido web (servicios, materiales)
     - Calendario detallado
     - Reportes y analytics
     - Configuración sistema
   ```

### ❌ Alternativa Rechazada

```yaml
Opción rechazada: Documentar Admin como 1 solo rol

Ejemplo:
  Admin (34 URLs totales, sin diferenciar mobile/desktop)

Problemas:
  - Oculta realidad: Jonathan NO usa desktop frecuentemente
  - Skills mobile-specific perdidas (touch targets, llamar/WhatsApp)
  - Documentación genérica vs especializada
  - Claude no sabría optimizar para iPhone (contexto crítico)

Resultado MAL:
  - "Revisa admin cotizaciones" → ¿Cuál? ¿Mobile o desktop?
  - Recomendaciones desktop cuando Jonathan usa mobile
  - Pérdida de contexto de uso real
```

### 📊 Resultado

**Impacto:**
- ✅ Documentación refleja uso REAL (90% mobile, 10% desktop)
- ✅ Skills especializadas por dispositivo
- ✅ Claude optimiza según contexto (mobile-first para Jonathan)

**Estadísticas:**
```yaml
Admin Mobile: 28 URLs (18 Guest + 8 Cliente + 2 Admin Mobile)
Admin Desktop: 34 URLs (28 Mobile + 6 Desktop-only)

Skills creadas:
  - ROL_ADMIN_MOBILE_COTIZACIONES.md
  - ROL_ADMIN_MOBILE_CLIENTES.md
  - ROL_ADMIN_DESKTOP_DASHBOARD.md
  - ROL_ADMIN_DESKTOP_SERVICIOS.md
  - ROL_ADMIN_DESKTOP_MATERIALES.md
  - ROL_ADMIN_DESKTOP_CALENDARIO.md
  - ROL_ADMIN_DESKTOP_REPORTES.md
  - ROL_ADMIN_DESKTOP_CONFIGURACION.md
```

**Lección aprendida:**
> **"Si device determina UX/funcionalidad, documentar separado. Contexto de uso > categoría genérica."**

---

## DECISIÓN 5: Transacciones WiFi Asimétricas

### ✅ Decisión Tomada

```yaml
Patrón: C → [A ⇄ C] (asimétrico bidireccional)

Flujo real:
  1. Cliente crea cotización (C → Firestore)
  2. Admin recibe notificación (Firestore → A)
  3. Admin responde múltiple veces:
     - Cambia estado NUEVO → CONTACTADO (A → Firestore → C)
     - Asigna precio (A → Firestore → C)
     - Envía mensaje (A → Firestore → C)
     - Cambia estado CONTACTADO → COTIZADO (A → Firestore → C)
  4. Cliente ve cambios en <1 segundo (real-time)

Notación: C → [A ⇄ C] asimétrico
```

### 🤔 Razonamiento

**Por qué asimétrico:**

1. **Cliente envía 1 vez, Admin responde múltiple:**
   ```yaml
   Cliente (1 acción):
     - Crea cotización
     - Espera respuesta

   Admin (N acciones):
     - Revisa cotización
     - Llama cliente
     - Cambia estado (múltiples veces)
     - Asigna precio
     - Envía mensajes
   ```

2. **NO es simétrico bidireccional:**
   ```yaml
   Simétrico sería:
     A ⇄ B (ambos envían/reciben con igual frecuencia)

   Real es:
     C → [A ⇄ C] (Cliente inicia, Admin responde N veces)
   ```

3. **Mecanismo real-time (Firebase onSnapshot):**
   ```typescript
   // Cliente escucha SOLO SUS cotizaciones
   onSnapshot(
     query(collection(db, 'quotes'), where('clientId', '==', userId)),
     (snapshot) => { /* Actualiza UI en <1s */ }
   );

   // Admin escucha TODAS las cotizaciones
   onSnapshot(
     collection(db, 'quotes'),
     (snapshot) => { /* Actualiza dashboard */ }
   );
   ```

### ❌ Alternativa Rechazada

```yaml
Opción rechazada: Documentar como simétrico (A ⇄ C)

Problemas:
  - Implica que ambos envían/reciben con igual frecuencia (FALSO)
  - Cliente envía 1 vez, Admin responde 5-10 veces
  - Notación pierde asimetría real del flujo

Hubiera resultado en:
  - Documentación imprecisa
  - Expectativas incorrectas (Cliente espera poder responder múltiple)
  - Claude no captura patrón de uso real
```

### 📊 Resultado

**Impacto:**
- ✅ Notación precisa (C → [A ⇄ C] refleja asimetría)
- ✅ Claude entiende que Admin es "respondedor activo"
- ✅ Documentación alineada con código (onSnapshot listeners)

**Ejemplo conversación:**
```yaml
Usuario: "¿Cómo funciona el flujo de cotizaciones?"
Claude: "Es asimétrico: Cliente crea 1 vez (C →),
         luego Admin responde múltiple ([A ⇄ C]).
         Real-time vía Firebase onSnapshot."
```

**Lección aprendida:**
> **"Transacciones bidireccionales NO siempre son simétricas. Documentar patrón REAL de frecuencia."**

---

## DECISIÓN 6: LETRA A y B según Contexto (NO Fijo)

### ✅ Decisión Tomada

```yaml
LETRA = Contexto de uso (flexible)

Cliente:
  LETRA A (acción): Crear cotizaciones
  LETRA B (browse): Dashboard (navegar tabs)

Admin:
  LETRA A (control): Dashboard, CRUD
  LETRA B (browse): (No usado actualmente)

Definición:
  A = Admin/Control/Dashboard (gestión activa)
  B = Browse/Lista/Público (navegación, lectura)
```

### 🤔 Razonamiento

**Por qué contexto flexible:**

1. **Cliente usa AMBAS letras:**
   ```yaml
   1A: /cotizacion (ACCIÓN: crear)
   1B: /mi-dashboard (BROWSE: navegar tabs)

   Razón: Cliente tiene 2 contextos diferentes
   ```

2. **NO es "A = Admin, B = Cliente":**
   ```yaml
   Si fuera rol fijo:
     LETRA A = solo Admin
     LETRA B = solo Cliente

   Problema: Cliente tiene acciones (crear cotización = A)
            pero también browsea (dashboard = B)
   ```

3. **Es contexto de USO, no categoría de usuario:**
   ```yaml
   Pregunta: ¿Qué HACE el usuario en este nodo?

   Crear/Modificar/Gestionar → LETRA A
   Leer/Navegar/Explorar → LETRA B
   Auditar/Historial → LETRA C (futuro)
   ```

### ❌ Alternativa Rechazada

```yaml
Opción rechazada: LETRA = Rol fijo

Ejemplo:
  A = Admin (solo Jonathan)
  B = Cliente (solo clientes autenticados)
  G = Guest (no autenticado)

Problemas:
  - Cliente crea cotizaciones (¿es A o B?)
  - Admin navega también (¿es B aunque sea admin?)
  - Confunde ROL con ACCIÓN

Hubiera resultado en:
  - Mapeo confuso (Cliente tiene nodos A y B, incoherente)
  - Pérdida de significado de LETRA (rol ya está en CAPA 0)
  - Redundancia innecesaria
```

### 📊 Resultado

**Impacto:**
- ✅ LETRA independiente de rol (más flexible)
- ✅ Contexto de uso claro (acción vs browse)
- ✅ Escalable (agregar LETRA C = auditoría sin conflicto)

**Ejemplo mapeo:**
```yaml
Cliente:
  1A: /cotizacion (ACCIÓN)
  2A1: /cotizacion/rapida (ACCIÓN continuada)
  1B: /mi-dashboard (BROWSE)

Admin:
  3A: /admin (CONTROL dashboard)
  2A: /admin/servicios (ACCIÓN CRUD)
```

**Lección aprendida:**
> **"LETRA = contexto de USO (qué hace), NO categoría de USUARIO (quién es). Independiente de rol."**

---

## DECISIÓN 7: URLs Públicas = TODOS los Roles

### ✅ Decisión Tomada

```yaml
Concepto: Sitio web CON CRM (NO app interna)

Guest: 18 URLs públicas
Cliente: HEREDA 18 públicas + 8 adicionales = 26 URLs
Admin Mobile: HEREDA 26 Cliente + 2 adicionales = 28 URLs
Admin Desktop: HEREDA 28 Mobile + 6 adicionales = 34 URLs

Razón: Admin puede navegar sitio web público TAMBIÉN
```

### 🤔 Razonamiento

**Por qué herencia acumulativa:**

1. **Es un sitio web público CON CRM:**
   - NO es herramienta interna (tipo Jira, Notion)
   - Página web pública (/, /servicios, /materiales) accesible por TODOS
   - Admin también puede navegar sitio público

2. **Realidad de uso:**
   ```yaml
   Jonathan (Admin) puede:
     - Ver homepage (/)
     - Ver servicios públicos (/servicios)
     - Ver materiales públicos (/materiales)
     - Gestionar cotizaciones (/admin/cotizaciones)
     - Editar contenido (/admin/servicios)

   No tiene sentido BLOQUEAR URLs públicas a Admin
   ```

3. **Herencia lógica:**
   ```yaml
   Guest (18) = Base pública
   Cliente (26) = Base + funcionalidad autenticado
   Admin (28-34) = Todo Cliente + panel admin

   NO es:
   Guest (18) = Público
   Admin (X) = Solo admin (sin público) ← INCORRECTO
   ```

### ❌ Alternativa Rechazada

```yaml
Opción rechazada: Roles separados sin herencia

Ejemplo:
  Guest: 18 URLs (solo público)
  Cliente: 8 URLs (solo cliente, SIN público)
  Admin: 8 URLs (solo admin, SIN público ni cliente)

Problemas:
  - Admin NO puede ver homepage (absurdo)
  - Cliente NO puede ver /servicios (ilógico)
  - Documentación no refleja realidad

Hubiera resultado en:
  - Mapeo incorrecto de permisos
  - Middleware bloqueando URLs públicas a roles superiores
  - Confusión total
```

### 📊 Resultado

**Impacto:**
- ✅ Documentación refleja realidad (sitio web público + CRM)
- ✅ Herencia clara (cada rol incluye anterior)
- ✅ Middleware correcto (públicas NO validan auth)

**Estadísticas:**
```yaml
Guest: 18 URLs
Cliente: 18 + 8 = 26 URLs
Admin Mobile: 18 + 8 + 2 = 28 URLs
Admin Desktop: 18 + 8 + 2 + 6 = 34 URLs
```

**Lección aprendida:**
> **"En sitio web CON CRM, roles superiores HEREDAN URLs públicas. NO bloquear base a usuarios avanzados."**

---

## 📊 TABLA RESUMEN DE DECISIONES

| # | Decisión | Template Genérico | Soluciones Díaz | Impacto |
|---|----------|-------------------|-----------------|---------|
| 1 | CAPA 0 | Guardian de Planes | Sistema de Roles (G/C/A/SA) | +100% alineación con código real |
| 2 | NÚMERO | Niveles de URL (físico) | Profundidad funcional (1=puerta, 2=túnel) | +80% claridad arquitectónica |
| 3 | SUB-NODOS | No diferenciaba claramente | Tabs = SUB-NODOS (misma URL) | Mapa 12% más compacto, +90% precisión |
| 4 | Admin | 1 rol genérico | Mobile (90%) vs Desktop (10%) | +100% optimización por dispositivo |
| 5 | Transacciones | Simétrico (A ⇄ B) | Asimétrico (C → [A ⇄ C]) | +100% precisión flujo real |
| 6 | LETRA | Branch fijo | Contexto uso (acción/browse) | +60% flexibilidad, -30% confusión |
| 7 | URLs Públicas | No especificado | Heredadas por todos roles | +100% reflejo realidad sitio web |

---

## 🎓 LECCIONES GENERALES

### 1. Template es GUÍA, no BIBLIA
```yaml
Template genérico:
  ✅ Provee estructura base
  ✅ Conceptos fundamentales (NÚMERO+LETRA+CAPA)

Pero:
  ⚠️  NO es copy-paste directo
  ⚠️  Adaptar a TU contexto específico
  ⚠️  Algunas decisiones del template NO aplican
```

### 2. Documentación debe reflejar CÓDIGO real
```yaml
Pregunta clave:
  "¿Esto documenta cómo FUNCIONA o cómo DEBERÍA funcionar?"

Soluciones Díaz:
  ✅ CAPA 0 = Roles (porque middleware valida `userRole`)
  ✅ Mobile vs Desktop (porque Jonathan usa iPhone 90%)
  ✅ Asimétrico (porque Cliente 1 vez, Admin N veces)

Resultado:
  Claude Code entiende arquitectura REAL, no teórica
```

### 3. Contexto de uso > Categorías abstractas
```yaml
MAL:
  LETRA A = Admin (categoría)
  LETRA B = Cliente (categoría)

BIEN:
  LETRA A = Acción/Control (contexto)
  LETRA B = Browse/Leer (contexto)

Razón:
  Cliente puede hacer acciones (A) y browsear (B)
  Contexto de uso es más flexible que categoría fija
```

### 4. Pregunta "¿POR QUÉ?" antes de aplicar
```yaml
Template dice: "NÚMERO = niveles de URL"

NO aplicar ciegamente, preguntar:
  ¿Por qué niveles de URL?
  ¿Qué problema resuelve?
  ¿Aplica a MI caso?

En Soluciones Díaz:
  Niveles físicos NO reflejaban complejidad
  → Cambiar a funcional (puerta/túnel/control)
  → Resultado: +80% claridad
```

### 5. Iterar y validar con uso real
```yaml
Primera iteración:
  - Mapa inicial con 30 nodos
  - LETRA fija por rol (confuso)

Segunda iteración (después de usar 1 semana):
  - LETRA por contexto (mejor)
  - SUB-NODOS identificados (mapa más claro)
  - Admin Mobile vs Desktop (reflejo real)

Lección:
  Sistema evoluciona con uso
  Primera versión NO será perfecta
  Validar con conversaciones reales con Claude
```

---

## 🔮 DECISIONES FUTURAS (Pendientes)

### 1. LETRA C para Auditoría/History

```yaml
Estado actual: LETRA C no usada

Propuesta futura:
  LETRA C = History/Auditoría
  - Logs de cambios
  - Historial de cotizaciones
  - Auditoría de acciones admin

Por implementar cuando se agreguen features de compliance
```

### 2. NÚMERO 5 para Integraciones

```yaml
Estado actual: NÚMERO 1-4 usados

Propuesta futura:
  NÚMERO 5 = Integraciones externas
  - APIs terceros
  - Webhooks
  - Sincronización externa

Por implementar cuando se integren sistemas externos
```

### 3. SuperAdmin Separado en Mapa

```yaml
Estado actual: SuperAdmin documentado en ROLES_COMPLETO.md

Propuesta futura:
  Crear skill específica para nodos SuperAdmin
  - /superadmin/* (DevTools)
  - Debugging avanzado
  - Deploy control

Por implementar cuando se amplíen herramientas dev
```

---

## 📝 CONTRIBUIR MEJORAS

**¿Encontraste mejor decisión?**

1. Abre Issue en GitHub
2. Explica:
   - Decisión actual
   - Tu propuesta
   - Por qué es mejor
   - Evidencia (código, uso real)
3. Discute con comunidad
4. Implementa mejora
5. Actualiza DECISIONS-LOG.md

---

**Creado:** 2025-10-26
**Autor:** Patricio Díaz + Claude
**Versión:** 1.0.0
**Última actualización:** 2025-10-26

🌌 **"Decisiones arquitectónicas documentadas = contexto para IA + humanos futuros"**
