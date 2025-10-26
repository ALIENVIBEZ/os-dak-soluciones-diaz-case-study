# [NOMBRE DESCRIPTIVO] - Nodo [X][Y]

**Tipo:** Skill Tipo [1-6] - [NOMBRE TIPO]
**Nodo(s):** [Identificación del nodo]
**URL(s):** [URLs involucradas]
**Rol(es):** [Quién puede acceder]

---

## 🎯 Propósito

[Describe en 1-2 párrafos qué documenta esta skill]

Ejemplo:
```
Esta skill documenta el nodo 2A1 (Cotización Rápida), donde clientes
pueden enviar una cotización básica en menos de 5 minutos. Es el túnel
de conversión más rápido de Guest a Cliente registrado.
```

---

## 🗺️ Ubicación en Blockchain

```
CAPA [X][Y]: [URL]
  ├── Profundidad: [NÚMERO] ([significado: puerta/túnel/control/proyección])
  ├── Contexto: [LETRA] ([significado: acción/browse/etc])
  ├── Tipo: [Nodo / SUB-NODO]
  └── Rol: [Quién accede]
```

Ejemplo:
```
CAPA 2A1: /cotizacion/rapida
  ├── Profundidad: 2 (Túnel - flujo guiado)
  ├── Contexto: A (Acción - crear cotización)
  ├── Tipo: Nodo
  └── Rol: Cliente (autenticado)
```

---

## 🔗 Flujo de Información

[Diagrama o descripción del flujo de datos]

Ejemplo:
```
Usuario → Componente UI → Validación → API Route → Firestore
  ↓            ↓             ↓            ↓          ↓
Input      QuoteForm     Zod schema   /api/quotes  Collection
(5 campos) (React)       (validation) (POST)       'quotes'
```

O diagrama ASCII:
```
┌─────────┐       ┌──────────┐       ┌──────────┐       ┌──────────┐
│ Usuario │ ----> │ Frontend │ ----> │ API      │ ----> │ Database │
│ Cliente │       │ QuoteForm│       │ /quotes  │       │ Firestore│
└─────────┘       └──────────┘       └──────────┘       └──────────┘
                       │                   │                  │
                       ↓                   ↓                  ↓
                  Validación          Auth check        Write doc
                  Zod schema          Middleware        quotes/{id}
```

---

## 📊 Schema de Datos

[Qué collections/tablas/endpoints usa]

### Collections Firestore

```typescript
// Collection: quotes
interface Quote {
  id: string;
  clientId: string;              // Usuario que creó
  serviceType: string;           // Tipo servicio (pisos, pintura, etc)
  description: string;           // Descripción del trabajo
  urgency: 'urgente' | 'normal' | 'sin-prisa';
  status: 'NUEVO' | 'CONTACTADO' | 'COTIZADO' | 'CONTRATADO';
  createdAt: Timestamp;
  updatedAt: Timestamp;

  // Campos adicionales (opcionales)
  photos?: string[];             // URLs de fotos en Storage
  price?: number;                // Asignado por admin
  adminNotes?: string;           // Notas internas admin
}
```

### API Endpoints

```typescript
// POST /api/quotes
Request:
  {
    serviceType: string;
    description: string;
    urgency: 'urgente' | 'normal' | 'sin-prisa';
  }

Response:
  {
    success: true;
    quoteId: string;
    message: "Cotización creada exitosamente";
  }

Errores:
  400: Validación fallida
  401: No autenticado
  500: Error servidor
```

---

## 🎯 Componentes Involucrados

[Lista de archivos/componentes del código]

```yaml
Frontend:
  - src/app/cotizacion/rapida/page.tsx (página principal)
  - src/components/QuoteForm.tsx (formulario)
  - src/components/UrgencySelector.tsx (selector urgencia)
  - src/hooks/useQuoteSubmit.ts (lógica submit)

Backend:
  - src/app/api/quotes/route.ts (API handler)
  - src/lib/quotes.ts (lógica negocio)
  - src/lib/validation/quoteSchema.ts (Zod schema)

Database:
  - firestore.rules (Security Rules)
  - Collection: quotes
```

---

## 🔐 Roles y Permisos

[Quién puede acceder y qué puede hacer]

### Cliente (Usuario Autenticado)

```yaml
✅ Puede:
  - Acceder a /cotizacion/rapida
  - Crear cotización nueva
  - Subir hasta 10 fotos
  - Ver SUS cotizaciones creadas
  - Editar cotización ANTES de enviar

❌ NO puede:
  - Ver cotizaciones de otros clientes
  - Modificar precio
  - Cambiar estado (solo admin)
  - Eliminar cotización (solo archivar)
```

### Admin

```yaml
✅ Puede:
  - Ver TODAS las cotizaciones
  - Cambiar estado (NUEVO → CONTACTADO → COTIZADO)
  - Asignar precio
  - Agregar notas internas
  - Archivar/eliminar cotizaciones
```

### Firestore Security Rules

```javascript
match /quotes/{quoteId} {
  // Cliente puede leer solo SUS cotizaciones
  allow read: if request.auth.uid == resource.data.clientId;

  // Cliente puede crear cotizaciones
  allow create: if request.auth.uid != null;

  // Cliente puede actualizar solo ciertos campos de SUS cotizaciones
  allow update: if request.auth.uid == resource.data.clientId
                && !request.resource.data.diff(resource.data).affectedKeys()
                   .hasAny(['price', 'status', 'adminNotes']);

  // Admin puede hacer TODO
  allow read, write: if get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
}
```

---

## ⚡ Casos de Uso

### Caso 1: Cliente Nuevo Crea Primera Cotización

```yaml
Contexto:
  - María acaba de registrarse
  - Necesita cotizar remodelación de cocina
  - Quiere proceso rápido (no subir fotos aún)

Flujo:
  1. María navega a /cotizacion
  2. Click en "Cotización Rápida" (vs Detallada)
  3. Redirige a /cotizacion/rapida
  4. Completa formulario:
     - Servicio: Remodelación cocina
     - Descripción: "Cambiar muebles y pintar"
     - Urgencia: Normal
  5. Click "Enviar Cotización"
  6. Sistema valida (Zod schema)
  7. POST /api/quotes
  8. Crea documento en Firestore
  9. Redirige a /mi-dashboard?tab=cotizaciones
  10. María ve cotización con estado NUEVO

Tiempo total: ~3 minutos
Resultado: Cotización creada, Jonathan recibe notificación
```

### Caso 2: Cliente Abandona Formulario (Recovery)

```yaml
Contexto:
  - Pedro empieza cotización
  - Cierra browser accidentalmente
  - Vuelve 10 minutos después

Flujo:
  1. Pedro vuelve a /cotizacion/rapida
  2. Sistema detecta localStorage (draft guardado)
  3. Muestra modal: "¿Continuar cotización anterior?"
  4. Pedro acepta
  5. Formulario se llena con data guardada
  6. Pedro completa y envía

Tiempo ahorrado: 2 minutos
Resultado: 40% menos abandono
```

### Caso 3: Validación Falla (Error Handling)

```yaml
Contexto:
  - Ana intenta enviar cotización incompleta
  - Falta campo "Descripción"

Flujo:
  1. Ana completa solo 2 de 3 campos
  2. Click "Enviar"
  3. Zod schema detecta error
  4. Frontend muestra error inline:
     "Descripción es requerida (mínimo 20 caracteres)"
  5. Ana completa descripción
  6. Validación pasa
  7. Cotización enviada exitosamente

UX: Validación inline sin perder data
```

---

## 🔄 Transacciones Relacionadas

[Conexiones con otros nodos]

### Flujo Previo (Tipo 2: FLUJO)

```yaml
1A (Selector) → 2A1 (Este nodo)

Usuario en /cotizacion decide "Cotización Rápida"
→ Redirige a /cotizacion/rapida
```

### Flujo Siguiente (Tipo 2: FLUJO)

```yaml
2A1 (Este nodo) → 1B.Cotizaciones (Dashboard)

Usuario envía cotización
→ Redirige a /mi-dashboard?tab=cotizaciones
→ Ve cotización recién creada
```

### Notificación Admin (Tipo 3: WiFi)

```yaml
2A1 (Cliente) ↔ Admin Dashboard (Real-time)

Cliente crea cotización → Firestore escribe doc
→ Admin escucha onSnapshot
→ Dashboard admin actualiza en <1 segundo
→ Jonathan recibe notificación
```

---

## 📐 Dimensiones y Performance

[Métricas de UX y performance]

### Mobile (iPhone)

```yaml
Touch Targets:
  - Botón "Enviar": 48px altura (Apple HIG compliant)
  - Inputs: 44px altura mínima
  - Selector urgencia: 56px cada opción

Layout:
  - Single column (sin scroll horizontal)
  - Formulario ocupa 100% width
  - Padding lateral: 16px

Performance:
  - Carga inicial: <1.5s
  - Submit: <800ms (API + Firestore)
  - Validación inline: <50ms (sin lag perceptible)
```

### Desktop

```yaml
Layout:
  - Max-width: 600px (centered)
  - Sidebar opcional (recomendaciones)

Performance:
  - Mismo que mobile (optimizado para mobile-first)
```

---

## 🐛 Errores Comunes y Soluciones

### Error 1: "Descripción muy corta"

```yaml
Síntoma: Usuario escribe "pintar" y no puede enviar
Causa: Validación requiere mínimo 20 caracteres
Solución:
  - Mensaje claro: "Describe con más detalle (mínimo 20 caracteres)"
  - Contador de caracteres visible
  - Placeholder con ejemplo: "Ej: Necesito pintar 3 habitaciones..."
```

### Error 2: "No autenticado"

```yaml
Síntoma: Usuario no registrado intenta acceder
Causa: Middleware valida auth, falla
Solución:
  - Redirect a /auth/signin
  - Query param: ?redirect=/cotizacion/rapida
  - Después de login: vuelve a formulario
```

### Error 3: "Firestore write failed"

```yaml
Síntoma: Error 500 al enviar
Causa: Security Rules bloquearon write
Solución:
  - Verificar token válido
  - Verificar campo clientId == request.auth.uid
  - Logs en Firebase Console
```

---

## 🎨 Mejores Prácticas

### UX

```yaml
1. Formulario corto (3-5 campos max)
2. Validación inline (no esperar submit)
3. Guardar draft en localStorage
4. Mostrar progreso (1 de 3 pasos)
5. Botón "Enviar" siempre visible
```

### Performance

```yaml
1. Lazy load componentes pesados
2. Debounce validación (300ms)
3. Optimistic UI (mostrar success antes de confirm)
4. Cache Firestore queries (1 min)
```

### Seguridad

```yaml
1. SIEMPRE validar server-side (Zod schema)
2. Sanitizar input (XSS prevention)
3. Rate limiting (max 10 cotizaciones/hora por usuario)
4. Verificar auth en API route
```

---

## 📊 Métricas y Analytics

[Qué medir para optimizar]

```yaml
Conversión:
  - Visitantes /cotizacion → /cotizacion/rapida
  - Usuarios empiezan formulario → completan
  - Completan → envían exitosamente

Tiempo:
  - Promedio completar formulario: 3 minutos
  - Tasa abandono: <15% (objetivo)

Errores:
  - Validación fallida: <5% (objetivo)
  - API errors: <0.1% (objetivo)

Satisfacción:
  - NPS post-envío: >8/10
```

---

## 🔮 Mejoras Futuras

[Roadmap de features]

```yaml
Versión 1.1 (próxima):
  [ ] Autocompletar dirección (Google Places API)
  [ ] Selector de fecha/hora preferida
  [ ] Agregar fotos (opcional, max 3)

Versión 1.2 (futuro):
  [ ] Estimador de precio automático (ML)
  [ ] Chat en vivo con admin
  [ ] Voice input (descripción por voz)

Versión 2.0 (largo plazo):
  [ ] AR preview (visualizar remodelación)
  [ ] Integración WhatsApp (enviar cotización por WA)
```

---

## 📚 Recursos Adicionales

[Links y referencias]

```yaml
Código:
  - src/app/cotizacion/rapida/page.tsx
  - GitHub PR: #123 (implementación inicial)

Docs relacionadas:
  - ROL_CLIENTE_CAPA_2A1_COTIZACION_RAPIDA.md
  - API-QUOTES-ENDPOINT.md

Testing:
  - Cypress test: cypress/e2e/quote-rapid.cy.ts
  - Unit tests: src/__tests__/quote-form.test.tsx

Diseño:
  - Figma: https://figma.com/file/abc123
  - Mobile mockup: designs/mobile-quote-rapid.png
```

---

**Última actualización:** [fecha]
**Creado por:** [nombre] + Claude
**Keywords:** [palabras clave para auto-activación]

Ejemplo keywords:
```
cotizacion, rapida, formulario, 2A1, cliente, crear, quote
```

---

## 📝 NOTAS DE USO

**Cómo usar este template:**

1. **Copiar archivo completo**
2. **Reemplazar [placeholders] con tu info**
3. **Eliminar secciones que no aplican**
4. **Agregar secciones específicas si necesario**
5. **Guardar en `.claude/skills/[nombre-skill]/SKILL.md`**

**Secciones opcionales (puedes eliminar):**
- Dimensiones y Performance (si no es critical)
- Errores Comunes (si es simple)
- Mejoras Futuras (si es estable)

**Secciones obligatorias:**
- Propósito
- Ubicación en Blockchain
- Flujo de Información
- Schema de Datos
- Roles y Permisos
- Al menos 1 Caso de Uso

---

🌌 **"Documentación profunda = Claude Code con contexto completo"**
