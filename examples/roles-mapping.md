# ROLES MAPPING - CAPA 0 en Soluciones Díaz

**Ejemplo de adaptación:** CAPA 0 = Sistema de Roles (en vez de Guardian de Planes)

---

## 🎯 Contexto de la Decisión

**Template genérico sugiere:**
```yaml
CAPA 0: Guardian de Planes (pre-ejecución)
  - Valida planes ANTES de ejecutar
  - NO tiene URL
  - Usa TodoWrite proactivamente
```

**Soluciones Díaz adaptó:**
```yaml
CAPA 0: Sistema de Roles (G/C/A/SA)
  - Define quién puede acceder a qué
  - Validado en cada request (middleware)
  - Roles son el guardian REAL del sistema
```

**¿Por qué cambió?**

Porque Soluciones Díaz es un **sitio web CON CRM integrado**, no una herramienta interna. Los **roles determinan TODO** en el sistema: URLs accesibles, permisos Firestore, flujos de usuario, UI mostrada.

---

## 📊 Los 4 Roles del Sistema

### ROL 1: Guest (G) - NO AUTENTICADO

```yaml
Identificación:
  Letra: G
  Firebase: Sin token (request.auth === null)
  Middleware: Pasa sin validación en URLs públicas

URLs accesibles: 18 URLs
  Públicas:
    - / (homepage)
    - /servicios (catálogo)
    - /materiales (catálogo)
    - /nosotros
    - /contacto

  Auth:
    - /auth/signin
    - /auth/signup
    - /auth/verify

  SEO Landings:
    - /blog/remodelacion-casas-punta-arenas-guia-completa
    - /remodelacion-casas-punta-arenas
    - /maestro-constructor-punta-arenas
    - /emergencia-viento-punta-arenas

  Legal:
    - /politica-privacidad
    - /terminos-condiciones
    - /cookies

Puede hacer:
  ✅ Navegar sitio web público
  ✅ Ver servicios y materiales
  ✅ Leer información
  ✅ Registrarse

NO puede hacer:
  ❌ Crear cotizaciones (guardadas)
  ❌ Ver dashboard
  ❌ Acceder /admin/*
  ❌ Recibir notificaciones

Conversión a Cliente:
  - Registrarse con email + contraseña
  - Registrarse con Google OAuth
  - Registrarse con Facebook OAuth
  → Redirige a /mi-dashboard como Cliente
```

---

### ROL 2: Cliente (C) - AUTENTICADO

```yaml
Identificación:
  Letra: C
  Firebase: token.role === 'client'
  Firestore: users/{uid}.role === 'client'
  Middleware: Valida token, permite /mi-dashboard

URLs accesibles: 26 URLs
  Hereda: 18 URLs Guest
  Adicionales:
    - /cotizacion (selector)
    - /cotizacion/rapida (formulario 5 min)
    - /cotizacion/detallada (wizard 10-15 min)
    - /mi-dashboard (dashboard con 4 tabs)
      - ?tab=perfil
      - ?tab=cotizaciones
      - ?tab=mensajes
      - ?tab=notificaciones

Puede hacer:
  ✅ TODO de Guest
  ✅ Crear cotizaciones (rápidas y detalladas)
  ✅ Subir hasta 10 fotos por cotización
  ✅ Ver SOLO SUS cotizaciones
  ✅ Editar SU perfil (nombre, foto, teléfono)
  ✅ Recibir notificaciones del admin
  ✅ Recibir mensajes del admin (SOLO recibe, NO envía)

NO puede hacer:
  ❌ Ver cotizaciones de otros clientes
  ❌ Modificar precio de cotizaciones
  ❌ Cambiar estado de cotizaciones
  ❌ Acceder /admin/*
  ❌ Enviar mensajes a admin (solo recibe)

Firestore Rules:
  match /quotes/{quoteId} {
    // Cliente puede leer solo SUS cotizaciones
    allow read: if request.auth.uid == resource.data.clientId;

    // Cliente puede crear
    allow create: if request.auth.uid != null;

    // Cliente puede actualizar solo ciertos campos
    allow update: if request.auth.uid == resource.data.clientId
                  && !request.resource.data.diff(resource.data).affectedKeys()
                     .hasAny(['price', 'status', 'adminNotes']);
  }
```

---

### ROL 3: Admin (A) - JONATHAN

```yaml
Identificación:
  Letra: A
  Firebase: token.role === 'admin'
  Firestore: users/{uid}.role === 'admin'
  Email: jonathan@solucionesdiaz.cl (configurado manualmente)

Dispositivos:
  - iPhone (90% del tiempo - terreno)
  - Computador (10% del tiempo - oficina)

URLs accesibles:
  Mobile: 28 URLs (hereda 26 Cliente + 2 mobile-optimized)
  Desktop: 34 URLs (hereda 28 Mobile + 6 desktop-only)

URLs Mobile-Optimized (iPhone):
  - /admin/cotizaciones
    - Touch targets ≥ 44px (Apple HIG)
    - Cards (NO tabla)
    - Llamar/WhatsApp 1 tap
    - Marcar "Contactado" 1 tap
    - Sin scroll horizontal

  - /admin/clientes
    - Sistema VIP automático (3 tiers)
    - Llamar/WhatsApp mensajes contextuales
    - Búsqueda multi-campo
    - Google Maps integración

URLs Desktop-Only (Computador):
  - /admin (dashboard widgets)
  - /admin/servicios (CRUD servicios públicos)
  - /admin/materiales (CRUD materiales públicos)
  - /admin/calendario (eventos multi-día)
  - /admin/reportes (métricas, CSV/PDF export)
  - /admin/configuracion (settings sistema)

Puede hacer:
  ✅ TODO de Cliente
  ✅ Ver TODAS las cotizaciones (sin filtro userId)
  ✅ Cambiar estados: NUEVO → CONTACTADO → COTIZADO → CONTRATADO
  ✅ Asignar precios a cotizaciones
  ✅ Enviar mensajes a clientes
  ✅ Enviar notificaciones
  ✅ Ver todos los clientes registrados
  ✅ Editar servicios página pública (/servicios)
  ✅ Editar materiales página pública (/materiales)
  ✅ Gestionar calendario de proyectos
  ✅ Ver reportes y estadísticas
  ✅ Exportar datos (CSV, PDF)

NO puede hacer:
  ❌ Acceder /superadmin/* (solo Patricio)
  ❌ Modificar código fuente
  ❌ Modificar Firestore Security Rules
  ❌ Hacer deploy manual
  ❌ Acceder Firebase Console

Firestore Rules:
  match /quotes/{quoteId} {
    // Admin puede leer y escribir TODO
    allow read, write: if get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
  }

Workflow Mobile (terreno):
  1. Jonathan abre iPhone
  2. Abre /admin/cotizaciones
  3. Ve 5 cotizaciones nuevas (cards)
  4. Tap en card → detalles + fotos
  5. Tap "Llamar" → iPhone abre app teléfono
  6. Llama al cliente
  7. Después: Tap "Contactado" → estado actualizado
  8. Siguiente cotización

  Tiempo: ~2 minutos por cotización
  Taps: ~4 por cotización
```

---

### ROL 4: SuperAdmin (SA) - PATRICIO

```yaml
Identificación:
  Letra: SA
  Firebase: token.role === 'admin' && token.isSuperAdmin === true
  Firestore: users/{uid}.role === 'admin' && users/{uid}.isSuperAdmin === true
  Email: patricio@solucionesdiaz.cl

Dispositivo:
  - Desktop + Terminal + VS Code (90%)
  - Mobile (10% - testing)

URLs accesibles: TODAS
  Hereda: 34 URLs Admin Desktop
  Adicionales:
    - /superadmin/* (DevTools - futuro)
    - Firebase Console (externo)
    - GitHub repository
    - Vercel/Firebase Hosting dashboard

Puede hacer:
  ✅ TODO de Admin
  ✅ Acceso Firebase Console
  ✅ Modificar código fuente (VS Code)
  ✅ Modificar Firestore Security Rules
  ✅ Crear Firebase Functions
  ✅ Deploy manual (Firebase Hosting)
  ✅ Acceso a logs del sistema
  ✅ Modificar configuraciones técnicas
  ✅ Crear/eliminar otros admins (vía script)
  ✅ Acceso a GitHub repository
  ✅ Debugging avanzado (Chrome DevTools en producción)

NO puede (auto-restricciones):
  ⚠️  NO deploy sin testing local
  ⚠️  NO modificar producción directamente (usar staging)
  ⚠️  NO eliminar datos sin backup
  ⚠️  NO commitear credenciales

Firestore Rules:
  // SuperAdmin puede hacer TODO
  allow read, write: if get(/databases/$(database)/documents/users/$(request.auth.uid)).data.isSuperAdmin == true;

Casos de uso:
  - Debugging errores en producción
  - Deploy de nuevas features
  - Modificar estructura Firestore
  - Crear nuevos endpoints API
  - Optimización de performance
```

---

## 🔐 Implementación Técnica

### Middleware (src/middleware.ts)

```typescript
export async function middleware(request: NextRequest) {
  const pathname = request.nextUrl.pathname;

  // 1. GUEST: Rutas públicas (sin validación)
  const publicRoutes = ['/', '/servicios', '/materiales', '/contacto'];
  if (publicRoutes.some(route => pathname.startsWith(route))) {
    return NextResponse.next();
  }

  // 2. Verificar token
  const token = request.cookies.get('auth-token');
  if (!token) {
    return NextResponse.redirect(new URL('/auth/signin', request.url));
  }

  // 3. Decodificar token
  const decoded = await verifyToken(token.value);
  const userRole = decoded.rol?.toString().trim().toUpperCase();

  // 4. SUPERADMIN: Proteger /superadmin
  if (pathname.startsWith('/superadmin')) {
    const isSuperAdmin = decoded.isSuperAdmin === true;
    if (!isSuperAdmin) {
      return NextResponse.redirect(new URL('/admin', request.url));
    }
    return NextResponse.next();
  }

  // 5. ADMIN: Proteger /admin
  if (pathname.startsWith('/admin')) {
    const allowedRoles = ['ADMIN', 'SUPERADMIN'];
    if (!userRole || !allowedRoles.includes(userRole)) {
      return NextResponse.redirect(new URL('/mi-dashboard', request.url));
    }
    return NextResponse.next();
  }

  // 6. CLIENTE: Rutas autenticadas
  if (pathname.startsWith('/mi-dashboard') || pathname.startsWith('/cotizacion')) {
    // Cualquier usuario autenticado puede acceder
    return NextResponse.next();
  }

  return NextResponse.next();
}
```

### Custom Claims (Firebase Functions)

```typescript
// Cloud Function para asignar roles
export const setUserRole = functions.https.onCall(async (data, context) => {
  // Solo SuperAdmin puede ejecutar
  const callerUid = context.auth?.uid;
  const callerClaims = context.auth?.token;

  if (callerClaims?.isSuperAdmin !== true) {
    throw new functions.https.HttpsError('permission-denied', 'Only SuperAdmin');
  }

  const { uid, role, isSuperAdmin } = data;

  // Asignar custom claims
  await admin.auth().setCustomUserClaims(uid, {
    rol: role.toUpperCase(),
    isSuperAdmin: isSuperAdmin === true,
  });

  // Actualizar Firestore
  await admin.firestore().collection('users').doc(uid).update({
    role: role,
    isSuperAdmin: isSuperAdmin === true,
  });

  return { success: true };
});
```

### Hook React (useUserRole)

```typescript
// src/hooks/useUserRole.ts
export function useUserRole() {
  const { user } = useAuth();

  const isGuest = !user;
  const isClient = user?.role === 'client';
  const isAdmin = user?.role === 'admin';
  const isSuperAdmin = user?.isSuperAdmin === true;

  return {
    isGuest,
    isClient,
    isAdmin,
    isSuperAdmin,
    role: user?.role || 'guest',
  };
}

// Uso en componentes
const { isAdmin, isSuperAdmin } = useUserRole();

if (isAdmin || isSuperAdmin) {
  return <AdminPanel />;
}

if (isClient) {
  return <ClientDashboard />;
}

return <PublicPage />;
```

---

## 📊 Matriz de Permisos

| Funcionalidad | Guest | Cliente | Admin | SuperAdmin |
|---------------|-------|---------|-------|------------|
| Ver sitio público | ✅ | ✅ | ✅ | ✅ |
| Crear cotización | ❌ | ✅ | ✅ | ✅ |
| Ver sus cotizaciones | ❌ | ✅ | ✅ | ✅ |
| Ver todas cotizaciones | ❌ | ❌ | ✅ | ✅ |
| Cambiar estado cotización | ❌ | ❌ | ✅ | ✅ |
| Editar contenido web | ❌ | ❌ | ✅ | ✅ |
| Acceso Firebase Console | ❌ | ❌ | ❌ | ✅ |
| Deploy | ❌ | ❌ | ❌ | ✅ |

---

## 🎯 Por Qué CAPA 0 = Roles Funciona

### 1. Refleja Código Real

```yaml
Middleware valida:
  if (userRole === 'admin') { /* allow */ }

Firestore Rules valida:
  if resource.data.role == 'admin'

Claude Code documenta:
  CAPA 0 - Sistema de Roles (A = Admin)

→ 100% alineación código ↔ documentación
```

### 2. Roles Determinan TODO

```yaml
URLs accesibles:
  Guest: 18 URLs
  Cliente: 26 URLs (18 + 8)
  Admin: 34 URLs (26 + 8)

Permisos Firestore:
  Cliente: WHERE clientId == userId
  Admin: SIN WHERE (todos los docs)

UI Mostrada:
  if (isAdmin) → Mostrar AdminPanel
  if (isClient) → Mostrar ClientDashboard

Flujos:
  Guest → Cliente: Requiere registro
  Cliente → Admin: NO (role fixed, solo Patricio puede asignar)
```

### 3. Guardian REAL del Sistema

```yaml
Cada request pasa por:
  1. Middleware: ¿Autenticado? ¿Rol correcto?
  2. API Route: Verificar token.role
  3. Firestore Rules: Validar role en write

Roles son el GUARDIAN que valida TODO

Guardian de Planes (template) era abstracto:
  "Validar plan antes de ejecutar"

Roles (adaptado) es concreto:
  "Validar QUIÉN puede ejecutar QUÉ"
```

---

## 🚀 Uso en Conversaciones con Claude

**Sin CAPA 0 definida:**
```yaml
Usuario: "Necesito agregar campo a cotizaciones"
Claude: "¿Quién puede acceder? ¿Todos o solo admins?"
Usuario: "Solo clientes autenticados"
Claude: "¿Cómo valido eso?"
Usuario: "Tenemos roles en Firebase"
Claude: "¿Qué roles?"
...
→ 5-10 mensajes para ubicar contexto
```

**Con CAPA 0 = Roles:**
```yaml
Usuario: "Necesito agregar campo a cotizaciones"
Claude: "Entendido. Nodo 2A1 (Cliente crea cotización).

CAPA 0: ROL C (Cliente)
  - Puede crear cotizaciones
  - Firestore Rules: allow create if request.auth.uid != null

Archivos afectados:
  - src/app/cotizacion/rapida/page.tsx (UI)
  - src/app/api/quotes/route.ts (validar token.role === 'client')
  - Firestore schema: quotes collection

¿Proceder?"

→ 1 mensaje con contexto completo
```

---

## 📚 Documentación Relacionada

```yaml
Archivos complementarios:
  - ROLES_COMPLETO.md (4 roles detallados)
  - DECISIONS-LOG.md (por qué CAPA 0 = Roles)
  - ADAPTATION-GUIDE.md (cómo adaptar CAPA 0)

Skills por rol:
  - ROL_CLIENTE_*.md (15 skills)
  - ROL_ADMIN_MOBILE_*.md (2 skills)
  - ROL_ADMIN_DESKTOP_*.md (6 skills)

Código:
  - src/middleware.ts (validación roles)
  - firestore.rules (Security Rules por rol)
  - src/hooks/useUserRole.ts (hook React)
```

---

## 🎓 Lecciones para Adaptación

### ¿Tu App Tiene Roles? → CAPA 0 = Roles

```yaml
Casos donde CAPA 0 = Roles es óptimo:
  ✅ Sitio web público + área autenticada
  ✅ CRM/Admin panel
  ✅ E-commerce (visitante, cliente, vendedor, admin)
  ✅ SaaS con tiers (free, pro, enterprise)
  ✅ Plataforma educativa (estudiante, profesor, admin)

Implementación:
  1. Define tus roles (3-5 roles típico)
  2. Asigna LETRA a cada rol (G, C, A, etc.)
  3. Documenta URLs accesibles por rol
  4. Mapea a código (middleware, Security Rules)
```

### ¿Tu App NO Tiene Roles? → CAPA 0 = Guardian Planes

```yaml
Casos donde Guardian de Planes es óptimo:
  ✅ Herramienta interna (sin usuarios externos)
  ✅ App simple (5-10 URLs, todos acceso igual)
  ✅ Prototipo (auth no implementado aún)
  ✅ CLI tool (no tiene usuarios en sentido tradicional)

Implementación:
  1. CAPA 0: Guardian de Planes
  2. Función: Validar plan antes de ejecutar
  3. Usar TodoWrite proactivamente
  4. NO mapear a roles (no existen)
```

### Híbrido (Avanzado)

```yaml
Casos donde ambos coexisten:
  ✅ App compleja con roles Y validación pre-ejecución

Implementación:
  CAPA 0A: Sistema de Roles (quién puede acceder)
  CAPA 0B: Guardian de Planes (qué se va a ejecutar)

Ejemplo:
  Usuario es Admin (CAPA 0A permite acceso)
  Pero antes de ejecutar DELETE masivo, validar plan (CAPA 0B)
```

---

**Creado:** 2025-10-26
**Autor:** Patricio Díaz + Claude
**Propósito:** Demostrar adaptación CAPA 0 con ejemplo real
**Para:** Developers adaptando OS DAK a apps con roles

🌌 **"CAPA 0 debe ser el guardian REAL de tu sistema, no el concepto genérico"**
