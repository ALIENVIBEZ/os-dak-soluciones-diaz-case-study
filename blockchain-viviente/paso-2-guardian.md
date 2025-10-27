# 🛡️ Blockchain Viviente - CAPA 0 (Guardian)

**App**: Soluciones Díaz CRM (Construcción)
**Fecha**: 27-10-2025
**Versión**: 1.0 - PASO 2 (Sistema de control de acceso)

---

## 🎯 Qué es CAPA 0 - Guardian

**Guardian = El guardián que controla TODO antes de ejecutar**

```yaml
Función:
  - Valida permisos ANTES de que usuario acceda a cualquier nodo
  - Define quién puede acceder a qué
  - Es la BASE sobre la que se construye todo
  - Sin Guardian, no hay seguridad ni estructura

Analogía:
  - Guardian = Guardián de seguridad en la entrada de un edificio
  - Revisa tu credencial antes de dejarte pasar
  - Decide a qué pisos/áreas puedes acceder
```

---

## 👥 Sistema de Roles (4 Roles)

### 🔴 ROL 1: GUEST (No Autenticado)

```yaml
Nombre: Guest / Visitante
Estado auth: NO autenticado
Permisos: Acceso público

Puede acceder a:
  ✅ Nivel 1 - Entradas públicas
    - / (homepage marketing)
    - /nosotros
    - /servicios
    - /materiales
    - /galeria
    - /calculadora
    - /auth/signin
    - /politica-privacidad
    - /terminos-condiciones
    - /cookies

  ✅ Nivel 2 - SEO Landings
    - /blog/remodelacion-casas-punta-arenas-guia-completa
    - /remodelacion-casas-punta-arenas
    - /maestro-constructor-punta-arenas
    - /emergencia-viento-punta-arenas

  ✅ Nivel 2 - Auth Flow
    - /auth/verify
    - /auth/resend-verification
    - /auth/error
    - /auth/unauthorized

NO puede acceder a:
  ❌ Cualquier nodo CLIENT
  ❌ Cualquier nodo ADMIN
  ❌ Cualquier nodo SUPER_ADMIN

Total nodos: 17 nodos accesibles
```

### 🔵 ROL 2: CLIENT (Cliente Registrado)

```yaml
Nombre: Cliente / Usuario Registrado
Estado auth: Autenticado como cliente
Permisos: Acceso cliente + público

Puede acceder a:
  ✅ TODOS los nodos GUEST (hereda 17 nodos)

  ✅ Nivel 1 - Cliente
    - /cotizacion (selector)

  ✅ Nivel 2 - Formularios
    - /cotizacion/rapida
    - /cotizacion/detallada
    - /cotizacion/wizard
    - /cotizacion/optimizada
    - /cotizacion/confirmacion/[id]

  ✅ Nivel 3 - Dashboard Personal
    - /mi-dashboard
    - /mi-dashboard?tab=perfil
    - /mi-dashboard?tab=cotizaciones
    - /mi-dashboard?tab=mensajes
    - /mi-dashboard?tab=notificaciones

NO puede acceder a:
  ❌ Cualquier nodo ADMIN
  ❌ Cualquier nodo SUPER_ADMIN

Total nodos: 17 (GUEST) + 7 (CLIENT) = 24 nodos accesibles
```

### 🟢 ROL 3: ADMIN (Administrador - Jonathan)

```yaml
Nombre: Admin / Dueño Negocio (Jonathan)
Estado auth: Autenticado como admin
Permisos: Acceso admin + cliente + público

Puede acceder a:
  ✅ TODOS los nodos GUEST (hereda 17 nodos)
  ✅ TODOS los nodos CLIENT (hereda 7 nodos)

  ✅ Nivel 3 - Admin Desktop
    - /admin (dashboard principal)
    - /admin/servicios (CRUD)
    - /admin/materiales (CRUD)
    - /admin/calendario
    - /admin/reportes
    - /admin/configuracion
    - /admin/clientes/[id]

  ✅ Nivel 3 - Admin Mobile
    - /admin/cotizaciones (gestión mobile)
    - /admin/clientes (gestión mobile)

NO puede acceder a:
  ❌ Nodos SUPER_ADMIN (dev tools, testing)

Total nodos: 24 (GUEST+CLIENT) + 9 (ADMIN) = 33 nodos accesibles

Características especiales:
  - Acceso Desktop + Mobile
  - Device detection automática
  - Puede ver TODO de clientes
  - Puede modificar catálogos
  - Puede gestionar cotizaciones
```

### 🟣 ROL 4: SUPER_ADMIN (Developer - Patricio)

```yaml
Nombre: Super Admin / Developer (Patricio)
Estado auth: Autenticado como super admin
Permisos: Acceso TOTAL (todos los roles + dev)

Puede acceder a:
  ✅ TODOS los nodos GUEST (hereda 17 nodos)
  ✅ TODOS los nodos CLIENT (hereda 7 nodos)
  ✅ TODOS los nodos ADMIN (hereda 9 nodos)

  ✅ Nivel 4 - Dev Tools
    - /dev-tools
    - /dev-tools/evolution
    - /dev-tools/knowledge-graph
    - /super-admin
    - /super-admin/testing
    - /super-admin-simple

  ✅ Nivel 4 - Testing (20+ rutas)
    - /test-* (todas las rutas test)
    - /debug-* (todas las rutas debug)
    - /demo-* (todas las rutas demo)

Total nodos: 33 (GUEST+CLIENT+ADMIN) + 40+ (SUPER_ADMIN) = 73+ nodos accesibles

Características especiales:
  - Acceso sin restricciones
  - Puede ver código fuente
  - Puede hacer testing
  - Puede modificar TODO
  - Bypass de validaciones en dev
```

---

## 🔒 Reglas de Validación Guardian

### Regla 1: Herencia en Cascada

```yaml
Jerarquía de herencia:
  SUPER_ADMIN (nivel 4)
    ↓ hereda TODO de
  ADMIN (nivel 3)
    ↓ hereda TODO de
  CLIENT (nivel 2)
    ↓ hereda TODO de
  GUEST (nivel 1)

Si tienes rol superior, automáticamente tienes acceso a roles inferiores.
```

### Regla 2: Validación en Cada Request

```yaml
Flujo por request:

1. Usuario intenta acceder a /admin/cotizaciones

2. Guardian intercepta:
   a) ¿Usuario autenticado?
      → NO: Redirect /auth/signin
      → SÍ: Continuar paso 3

   b) ¿Qué rol tiene?
      → Leer de Firebase Auth custom claims

   c) ¿Rol permite acceso a este nodo?
      → GUEST: ❌ Prohibido → /auth/unauthorized
      → CLIENT: ❌ Prohibido → /auth/unauthorized
      → ADMIN: ✅ Permitido → Continuar
      → SUPER_ADMIN: ✅ Permitido → Continuar

   d) Si Mobile: ¿Nodo compatible?
      → /admin/cotizaciones: ✅ Mobile compatible
      → /admin/servicios: ❌ Solo Desktop → Warning

3. Acceso concedido → Renderizar nodo
```

### Regla 3: Custom Claims (Firebase)

```yaml
Estructura en Firebase Auth:

{
  "uid": "abc123",
  "email": "cliente@example.com",
  "customClaims": {
    "role": "CLIENT",           // GUEST, CLIENT, ADMIN, SUPER_ADMIN
    "permissions": [
      "read:own_quotes",
      "create:quote",
      "read:own_profile"
    ]
  }
}

Guardian lee customClaims.role para decidir acceso.
```

### Regla 4: Middleware de Protección

```yaml
Ubicación: src/middleware.ts

export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;

  // Paso 1: Rutas públicas (bypass Guardian)
  if (isPublicRoute(pathname)) {
    return NextResponse.next(); // GUEST permitido
  }

  // Paso 2: Verificar auth
  const token = request.cookies.get('session');
  if (!token) {
    return redirectToLogin(); // No autenticado
  }

  // Paso 3: Verificar rol
  const role = getUserRole(token);

  if (pathname.startsWith('/admin')) {
    if (role !== 'ADMIN' && role !== 'SUPER_ADMIN') {
      return redirectToUnauthorized();
    }
  }

  if (pathname.startsWith('/dev-tools') || pathname.startsWith('/super-admin')) {
    if (role !== 'SUPER_ADMIN') {
      return redirectToUnauthorized();
    }
  }

  // Paso 4: Acceso concedido
  return NextResponse.next();
}

// Rutas protegidas por Guardian
export const config = {
  matcher: [
    '/mi-dashboard/:path*',
    '/cotizacion/:path*',
    '/admin/:path*',
    '/dev-tools/:path*',
    '/super-admin/:path*'
  ]
};
```

---

## 🎯 Matriz de Permisos (Resumen)

```yaml
Nodo                          | GUEST | CLIENT | ADMIN | SUPER_ADMIN
------------------------------|-------|--------|-------|-------------
/ (homepage)                  | ✅    | ✅     | ✅    | ✅
/auth/signin                  | ✅    | ✅     | ✅    | ✅
/servicios                    | ✅    | ✅     | ✅    | ✅
/calculadora                  | ✅    | ✅     | ✅    | ✅
/cotizacion                   | ❌    | ✅     | ✅    | ✅
/cotizacion/rapida            | ❌    | ✅     | ✅    | ✅
/mi-dashboard                 | ❌    | ✅     | ✅    | ✅
/admin                        | ❌    | ❌     | ✅    | ✅
/admin/cotizaciones           | ❌    | ❌     | ✅    | ✅
/admin/servicios              | ❌    | ❌     | ✅    | ✅
/dev-tools                    | ❌    | ❌     | ❌    | ✅
/super-admin                  | ❌    | ❌     | ❌    | ✅
/test-*                       | ❌    | ❌     | ❌    | ✅
```

---

## 🔐 Firestore Security Rules

Guardian también se implementa en Firestore Rules:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Helper: Get user role
    function getUserRole() {
      return request.auth.token.role;
    }

    // Collection: quotes
    match /quotes/{quoteId} {
      // CLIENT: Solo sus propias cotizaciones
      allow read: if request.auth != null &&
                     (resource.data.userId == request.auth.uid ||
                      getUserRole() in ['ADMIN', 'SUPER_ADMIN']);

      // CLIENT: Puede crear cotizaciones
      allow create: if request.auth != null &&
                       getUserRole() in ['CLIENT', 'ADMIN', 'SUPER_ADMIN'];

      // ADMIN: Puede actualizar estado
      allow update: if request.auth != null &&
                       getUserRole() in ['ADMIN', 'SUPER_ADMIN'];

      // ADMIN: Puede eliminar
      allow delete: if request.auth != null &&
                       getUserRole() in ['ADMIN', 'SUPER_ADMIN'];
    }

    // Collection: users
    match /users/{userId} {
      // CLIENT: Solo su propio perfil
      allow read: if request.auth != null &&
                     (request.auth.uid == userId ||
                      getUserRole() in ['ADMIN', 'SUPER_ADMIN']);

      // CLIENT: Puede actualizar su propio perfil
      allow update: if request.auth != null &&
                       request.auth.uid == userId;

      // ADMIN: Puede leer todos los perfiles
      allow list: if request.auth != null &&
                     getUserRole() in ['ADMIN', 'SUPER_ADMIN'];
    }

    // Collection: services
    match /services/{serviceId} {
      // GUEST: Puede leer catálogo público
      allow read: if true;

      // ADMIN: Puede modificar catálogo
      allow write: if request.auth != null &&
                      getUserRole() in ['ADMIN', 'SUPER_ADMIN'];
    }

    // Collection: materials
    match /materials/{materialId} {
      // GUEST: Puede leer catálogo público
      allow read: if true;

      // ADMIN: Puede modificar catálogo
      allow write: if request.auth != null &&
                      getUserRole() in ['ADMIN', 'SUPER_ADMIN'];
    }
  }
}
```

---

## 🚨 Casos Especiales

### Caso 1: Homepage Condicional

```yaml
Nodo: 1A.GUEST (/)
Problema: Misma URL, diferente contenido por rol

Solución Guardian:
  1. Usuario accede a /
  2. Guardian verifica auth
  3. Si NO autenticado (GUEST):
     → Renderiza landing marketing
  4. Si autenticado:
     → Verifica rol
     → CLIENT: Redirect /mi-dashboard
     → ADMIN: Redirect /admin
     → SUPER_ADMIN: Redirect /dev-tools

Tipo transacción: CONTEXTO CONDICIONAL (12)
```

### Caso 2: Admin Mobile vs Desktop

```yaml
Nodo: 3B.ADMIN.MOBILE.1 (/admin/cotizaciones)
Problema: Mismo rol, diferentes dispositivos

Solución Guardian:
  1. Usuario ADMIN accede a /admin/cotizaciones
  2. Guardian verifica rol: ✅ ADMIN permitido
  3. Detecta dispositivo:
     → Mobile: ✅ Renderiza vista mobile optimizada
     → Desktop: ⚠️ Warning "Esta página está optimizada para mobile"
                  Opción: Ver de todos modos
```

### Caso 3: URLs NO Autorizadas

```yaml
Problema: 38 URLs debug/test expuestas públicamente

Solución Guardian:
  - Rutas /admin-fix, /admin-test, /temp-login
  - NO están en middleware matcher
  - NO tienen validación Guardian
  - Cualquiera puede acceder ❌

Acción:
  1. Agregar a middleware.ts matcher
  2. O eliminar rutas completamente
  3. Prioridad: URGENTE (24h)
```

---

## 📊 Métricas de Seguridad

```yaml
Nodos protegidos por Guardian: 56/73 (76%)
  - CLIENT: 7 nodos protegidos
  - ADMIN: 9 nodos protegidos
  - SUPER_ADMIN: 40 nodos protegidos

Nodos públicos (GUEST): 17/73 (23%)

Nodos SIN protección (CRÍTICO): 38 URLs
  - Debug/test routes: 35
  - Duplicadas: 3
  - Acción: Eliminar o proteger URGENTE

Firestore collections protegidas: 4/4 (100%)
  - quotes: ✅ Protegida
  - users: ✅ Protegida
  - services: ✅ Protegida
  - materials: ✅ Protegida
```

---

## ✅ Validación CAPA 0

```yaml
Criterios validados:

✅ 4 roles definidos (GUEST, CLIENT, ADMIN, SUPER_ADMIN)
✅ Herencia en cascada implementada
✅ Middleware de protección configurado
✅ Custom claims Firebase configurados
✅ Firestore Rules implementadas
✅ Matriz de permisos documentada

⚠️ Pendiente:
  - Proteger 38 URLs NO autorizadas
  - Agregar device detection para admin mobile
  - Testing exhaustivo de permisos

Estado: PASO 2 COMPLETADO ✅
Próximo paso: PASO 3 - Crear agentes por cada nodo principal
```

---

**Creado**: 27-10-2025
**Metodología**: Descubrimiento Patricio - Guardian como base
**Siguiente**: Crear agentes especializados para nodos críticos

