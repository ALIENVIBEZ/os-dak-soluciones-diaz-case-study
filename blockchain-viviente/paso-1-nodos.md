# 🧬 Blockchain Viviente - Nodos (NÚMERO+LETRA+CAPA)

**App**: Soluciones Díaz CRM (Construcción)
**Fecha**: 27-10-2025
**Versión**: 1.0 - PASO 1 (Base fundamental)

---

## 📐 Notación: NÚMERO+LETRA+CAPA

```yaml
NÚMERO: Profundidad funcional (qué tan adentro estás)
  1 = Entrada/Puerta (landing, inicio, selector)
  2 = Intermedio/Túnel (formularios, procesos)
  3 = Control/Dashboard (gestión, administración)
  4 = Proyección/Analytics (futuro, análisis)

LETRA: Contexto/Tipo de página
  A = Acción/Admin/Control
  B = Browse/Lista/Dashboard
  C = Configuration/Settings
  D = Data/Display

CAPA: Seguridad/Rol (definido por Guardian CAPA 0)
  - Determinado por rol que accede
  - GUEST, CLIENT, ADMIN, SUPER_ADMIN
```

---

## 🗺️ NODOS MAPEADOS POR ROL

### 🔴 ROL GUEST (No Autenticado) - CAPA GUEST

#### Nivel 1 - Entradas/Puertas

**1A - Homepage**
```yaml
Nodo: 1A.GUEST
URL: /
Profundidad: 1 (entrada principal)
Tipo: A (acción - punto decisión)
Rol: GUEST
Descripción: Landing page marketing
```

**1B - Nosotros**
```yaml
Nodo: 1B.GUEST
URL: /nosotros
Profundidad: 1 (entrada)
Tipo: B (browse/display)
Rol: GUEST
Descripción: Información empresa
```

**1B - Servicios**
```yaml
Nodo: 1B.GUEST.1
URL: /servicios
Profundidad: 1
Tipo: B (browse)
Rol: GUEST
Descripción: Catálogo servicios
```

**1B - Materiales**
```yaml
Nodo: 1B.GUEST.2
URL: /materiales
Profundidad: 1
Tipo: B (browse)
Rol: GUEST
Descripción: Catálogo materiales
```

**1B - Galería**
```yaml
Nodo: 1B.GUEST.3
URL: /galeria
Profundidad: 1
Tipo: B (browse)
Rol: GUEST
Descripción: Portfolio trabajos
```

**1A - Calculadora**
```yaml
Nodo: 1A.GUEST.1
URL: /calculadora
Profundidad: 1
Tipo: A (acción - calcular)
Rol: GUEST
Descripción: Calculadora cotización rápida
```

**1A - Auth Signin**
```yaml
Nodo: 1A.GUEST.2
URL: /auth/signin
Profundidad: 1
Tipo: A (acción - login)
Rol: GUEST
Descripción: Página de inicio de sesión
```

**1D - Info Pages**
```yaml
Nodo: 1D.GUEST.1
URL: /politica-privacidad
Profundidad: 1
Tipo: D (data/display)
Rol: GUEST

Nodo: 1D.GUEST.2
URL: /terminos-condiciones

Nodo: 1D.GUEST.3
URL: /cookies
```

#### Nivel 2 - SEO Landings (Túneles de conversión)

**2A - SEO Landings**
```yaml
Nodo: 2A.GUEST.1
URL: /blog/remodelacion-casas-punta-arenas-guia-completa
Profundidad: 2 (intermedio - contenido)
Tipo: A (acción - conversión)
Rol: GUEST
Descripción: Landing SEO para conversión

Nodo: 2A.GUEST.2
URL: /remodelacion-casas-punta-arenas

Nodo: 2A.GUEST.3
URL: /maestro-constructor-punta-arenas

Nodo: 2A.GUEST.4
URL: /emergencia-viento-punta-arenas
```

#### Nivel 2 - Auth Flow

**2A - Auth Process**
```yaml
Nodo: 2A.GUEST.5
URL: /auth/verify
Profundidad: 2
Tipo: A (acción - verificar)
Rol: GUEST
Descripción: Verificación email

Nodo: 2A.GUEST.6
URL: /auth/resend-verification

Nodo: 2A.GUEST.7
URL: /auth/error

Nodo: 2A.GUEST.8
URL: /auth/unauthorized
```

---

### 🔵 ROL CLIENTE (Usuario Registrado) - CAPA CLIENT

#### Nivel 1 - Selector/Entrada

**1A - Cotización Selector**
```yaml
Nodo: 1A.CLIENT
URL: /cotizacion
Profundidad: 1 (puerta - selector)
Tipo: A (acción - elegir tipo)
Rol: CLIENT
Descripción: Selector tipo de cotización
```

#### Nivel 2 - Formularios (Túneles)

**2A1 - Cotización Rápida**
```yaml
Nodo: 2A1.CLIENT
URL: /cotizacion/rapida
Profundidad: 2 (túnel - formulario)
Tipo: A (acción - llenar)
Variante: 1 (rápida - 3 min)
Rol: CLIENT
Descripción: Formulario cotización express
```

**2A2 - Cotización Detallada**
```yaml
Nodo: 2A2.CLIENT
URL: /cotizacion/detallada
Profundidad: 2
Tipo: A (acción)
Variante: 2 (detallada - 10 min)
Rol: CLIENT
Descripción: Formulario cotización completa
```

**2A3 - Cotización Wizard**
```yaml
Nodo: 2A3.CLIENT
URL: /cotizacion/wizard
Profundidad: 2
Tipo: A (acción)
Variante: 3 (wizard - paso a paso)
Rol: CLIENT
Descripción: Cotización guiada
```

**2A4 - Cotización Optimizada**
```yaml
Nodo: 2A4.CLIENT
URL: /cotizacion/optimizada
Profundidad: 2
Tipo: A (acción)
Variante: 4 (ML-assisted)
Rol: CLIENT
Descripción: Cotización con IA
```

**2A - Confirmación**
```yaml
Nodo: 2A.CLIENT.CONFIRM
URL: /cotizacion/confirmacion/[id]
Profundidad: 2
Tipo: A (acción - confirmar)
Rol: CLIENT
Descripción: Confirmación post-envío
```

#### Nivel 3 - Dashboard Cliente (Control personal)

**3B - Mi Dashboard**
```yaml
Nodo: 3B.CLIENT
URL: /mi-dashboard
Profundidad: 3 (dashboard - control)
Tipo: B (browse - hub central)
Rol: CLIENT
Descripción: Dashboard personal cliente

Sub-vistas (tabs):
  - 3B.CLIENT.1 → ?tab=perfil (Mi Perfil)
  - 3B.CLIENT.2 → ?tab=cotizaciones (Mis Cotizaciones)
  - 3B.CLIENT.3 → ?tab=mensajes (Mensajes)
  - 3B.CLIENT.4 → ?tab=notificaciones (Notificaciones)

Nota: Tabs son estados internos, no nodos separados
```

---

### 🟢 ROL ADMIN (Jonathan - Dueño) - CAPA ADMIN

#### Nivel 3 - Dashboard Admin (Control negocio)

**3A - Admin Dashboard (Desktop)**
```yaml
Nodo: 3A.ADMIN.DESKTOP
URL: /admin
Profundidad: 3 (dashboard principal)
Tipo: A (admin/control)
Dispositivo: DESKTOP
Rol: ADMIN
Descripción: Panel principal administración
```

**3B - Servicios CRUD (Desktop)**
```yaml
Nodo: 3B.ADMIN.DESKTOP.1
URL: /admin/servicios
Profundidad: 3
Tipo: B (browse/manage)
Dispositivo: DESKTOP
Rol: ADMIN
Descripción: Gestión catálogo servicios
```

**3B - Materiales CRUD (Desktop)**
```yaml
Nodo: 3B.ADMIN.DESKTOP.2
URL: /admin/materiales
Profundidad: 3
Tipo: B (browse/manage)
Dispositivo: DESKTOP
Rol: ADMIN
Descripción: Gestión catálogo materiales
```

**3B - Calendario (Desktop)**
```yaml
Nodo: 3B.ADMIN.DESKTOP.3
URL: /admin/calendario
Profundidad: 3
Tipo: B (browse/schedule)
Dispositivo: DESKTOP
Rol: ADMIN
Descripción: Calendario obras y eventos
```

**3C - Configuración (Desktop)**
```yaml
Nodo: 3C.ADMIN.DESKTOP
URL: /admin/configuracion
Profundidad: 3
Tipo: C (configuration)
Dispositivo: DESKTOP
Rol: ADMIN
Descripción: Configuración general sistema
```

**3D - Reportes (Desktop)**
```yaml
Nodo: 3D.ADMIN.DESKTOP
URL: /admin/reportes
Profundidad: 3
Tipo: D (data/analytics)
Dispositivo: DESKTOP
Rol: ADMIN
Descripción: Reportes y analytics
```

**3B - Cliente Detalle (Desktop)**
```yaml
Nodo: 3B.ADMIN.DESKTOP.4
URL: /admin/clientes/[id]
Profundidad: 3
Tipo: B (browse individual)
Dispositivo: DESKTOP
Rol: ADMIN
Descripción: Vista detallada cliente específico
```

#### Admin Mobile (Gestión móvil)

**3B - Cotizaciones Mobile**
```yaml
Nodo: 3B.ADMIN.MOBILE.1
URL: /admin/cotizaciones
Profundidad: 3
Tipo: B (browse/manage)
Dispositivo: MOBILE
Rol: ADMIN
Descripción: Gestión cotizaciones desde móvil

Sistema 3 estados:
  - NUEVAS (sin revisar)
  - LEÍDAS (en proceso)
  - FINALIZADAS (completadas)
```

**3B - Clientes Mobile**
```yaml
Nodo: 3B.ADMIN.MOBILE.2
URL: /admin/clientes
Profundidad: 3
Tipo: B (browse/manage)
Dispositivo: MOBILE
Rol: ADMIN
Descripción: Gestión clientes desde móvil
```

---

### 🟣 ROL SUPER_ADMIN (Patricio - Developer) - CAPA SUPER_ADMIN

#### Nivel 4 - Dev Tools (Proyección/Testing)

**4A - Dev Tools Principal**
```yaml
Nodo: 4A.SUPER_ADMIN
URL: /dev-tools
Profundidad: 4 (tools - desarrollo)
Tipo: A (admin/control dev)
Rol: SUPER_ADMIN
Descripción: Herramientas desarrollo principal
```

**4A - Dev Evolution**
```yaml
Nodo: 4A.SUPER_ADMIN.1
URL: /dev-tools/evolution
Profundidad: 4
Tipo: A (análisis)
Rol: SUPER_ADMIN
Descripción: Análisis evolución proyecto
```

**4A - Knowledge Graph**
```yaml
Nodo: 4A.SUPER_ADMIN.2
URL: /dev-tools/knowledge-graph
Profundidad: 4
Tipo: A (visualización)
Rol: SUPER_ADMIN
Descripción: Mapa conocimiento proyecto
```

**4A - Super Admin Panel**
```yaml
Nodo: 4A.SUPER_ADMIN.3
URL: /super-admin
Profundidad: 4
Tipo: A (control total)
Rol: SUPER_ADMIN
Descripción: Panel súper administrador

Nodo: 4A.SUPER_ADMIN.4
URL: /super-admin/testing

Nodo: 4A.SUPER_ADMIN.5
URL: /super-admin-simple
```

#### Nivel 4 - Test Routes (QA/Testing)

**4D - Test Routes (20+ rutas)**
```yaml
Patrón: 4D.SUPER_ADMIN.TEST.*
URLs: /test-* (múltiples)
Profundidad: 4
Tipo: D (testing/data)
Rol: SUPER_ADMIN

Ejemplos:
  4D.SUPER_ADMIN.TEST.1 → /test-accessibility
  4D.SUPER_ADMIN.TEST.2 → /test-app-navigation
  4D.SUPER_ADMIN.TEST.3 → /test-basic
  4D.SUPER_ADMIN.TEST.4 → /test-dashboard
  4D.SUPER_ADMIN.TEST.5 → /test-design-system
  ...hasta 20+
```

**4D - Debug Routes**
```yaml
Patrón: 4D.SUPER_ADMIN.DEBUG.*
URLs: /debug-* (múltiples)
Profundidad: 4
Tipo: D (debugging)
Rol: SUPER_ADMIN

Ejemplos:
  4D.SUPER_ADMIN.DEBUG.1 → /debug-auth
  4D.SUPER_ADMIN.DEBUG.2 → /debug-quotes
  4D.SUPER_ADMIN.DEBUG.3 → /debug/oauth
```

**4D - Demo Routes**
```yaml
Patrón: 4D.SUPER_ADMIN.DEMO.*
URLs: /demo-* y /demos/* (múltiples)
Profundidad: 4
Tipo: D (demonstración)
Rol: SUPER_ADMIN

Ejemplos:
  4D.SUPER_ADMIN.DEMO.1 → /demo-navigation
  4D.SUPER_ADMIN.DEMO.2 → /demos/anchor-system
  4D.SUPER_ADMIN.DEMO.3 → /demos/content-row
  ...hasta 10+
```

---

## 📊 Resumen de Nodos

### Por Profundidad

```yaml
Nivel 1 (Entrada/Puerta): 12 nodos
  - GUEST: 9 nodos
  - CLIENT: 1 nodo (selector)
  - ADMIN: 0
  - SUPER_ADMIN: 0

Nivel 2 (Intermedio/Túnel): 14 nodos
  - GUEST: 8 nodos (SEO + Auth)
  - CLIENT: 5 nodos (formularios + confirmación)
  - ADMIN: 0
  - SUPER_ADMIN: 0

Nivel 3 (Control/Dashboard): 12 nodos
  - GUEST: 0
  - CLIENT: 1 nodo (mi-dashboard con 4 tabs)
  - ADMIN: 9 nodos (7 Desktop + 2 Mobile)
  - SUPER_ADMIN: 0

Nivel 4 (Proyección/Dev): 40+ nodos
  - GUEST: 0
  - CLIENT: 0
  - ADMIN: 0
  - SUPER_ADMIN: 40+ (dev tools + tests + demos)
```

### Por Rol

```yaml
GUEST: 17 nodos (niveles 1-2)
CLIENT: 7 nodos (niveles 1-3)
ADMIN: 9 nodos (nivel 3)
SUPER_ADMIN: 40+ nodos (nivel 4)

Total: ~73 nodos principales (excluyendo duplicados/deprecated)
```

### Por Tipo

```yaml
Tipo A (Acción/Control): ~35 nodos
Tipo B (Browse/Lista): ~20 nodos
Tipo C (Configuration): ~3 nodos
Tipo D (Data/Display): ~15 nodos
```

---

## 🚨 Nodos NO Autorizados (Eliminar)

```yaml
GUEST - 22 URLs NO autorizadas:
  - Duplicadas: /sobre-nosotros, /privacidad, /terminos
  - Auth debug: /auth/refresh, /auth/status, /auth/troubleshoot, etc. (11 rutas)
  - Preview: /servicios/preview, /materiales/preview
  - Utility: /offline, /simple, /system-status, etc. (6 rutas)

ADMIN - 3 URLs NO autorizadas:
  - /admin-fix (debug)
  - /admin-test (testing)
  - /admin/temp-login (bypass auth)

Acción: ELIMINAR urgente (antes 24h)
```

---

## 🎯 Nodos Críticos (Top 10)

```yaml
Por frecuencia/impacto:

1. 1A.GUEST (/) - Homepage - Entrada principal
2. 1A.CLIENT (/cotizacion) - Selector cotización
3. 2A1.CLIENT (/cotizacion/rapida) - Form rápido
4. 2A2.CLIENT (/cotizacion/detallada) - Form detallado
5. 3B.CLIENT (/mi-dashboard) - Dashboard cliente
6. 3A.ADMIN.DESKTOP (/admin) - Dashboard admin
7. 3B.ADMIN.MOBILE.1 (/admin/cotizaciones) - Gestión móvil
8. 1A.GUEST.2 (/auth/signin) - Login
9. 3B.ADMIN.DESKTOP.1 (/admin/servicios) - CRUD servicios
10. 3B.ADMIN.DESKTOP.2 (/admin/materiales) - CRUD materiales
```

---

## ✅ Validación Nodos

```yaml
Total nodos mapeados: ~73 principales
URLs analizadas: 99
Duplicados/deprecated detectados: 26
Nodos únicos válidos: 73

Cobertura por rol:
  GUEST: 100% (17/17)
  CLIENT: 100% (7/7)
  ADMIN: 100% (9/9)
  SUPER_ADMIN: 95% (40/42 - algunos agrupados por patrón)

Estado: PASO 1 COMPLETADO ✅
Próximo paso: PASO 2 - Mapear CAPA 0 (Guardian)
```

---

**Creado**: 27-10-2025
**Metodología**: Descubrimiento Patricio - Orden secuencial correcto
**Siguiente**: Documentar CAPA 0 antes de crear agentes

