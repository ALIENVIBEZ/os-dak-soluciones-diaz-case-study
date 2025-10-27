# 🤖 Agente: Admin Cotizaciones Mobile

## 🎯 Identificación

**Nodo**: 3B.ADMIN.MOBILE.1
**URL**: `/admin/cotizaciones`
**Rol**: ADMIN (Jonathan - dueño)
**Dispositivo**: MOBILE (iPhone optimizado)
**Tipo**: Dashboard (gestión mobile)
**Prioridad**: CRÍTICA (gestión diaria desde terreno)

---

## 📝 Descripción

Vista mobile-first para que Jonathan gestione todas las cotizaciones desde su iPhone mientras trabaja en terreno. Sistema completo de gestión con 3 estados (NUEVAS, LEÍDAS, FINALIZADAS), touch-optimizado y acción rápida (llamar/WhatsApp con 1 tap).

**Contexto crítico**: Jonathan usa principalmente su iPhone para gestionar negocio desde terreno mientras instala pisos o hace remodelaciones.

**Workflow optimizado**:
1. Recibe notificación → cotización nueva
2. Abre /admin/cotizaciones en iPhone
3. Ve card touch-friendly
4. Tap "Llamar" → abre app teléfono
5. Llama y acuerda visita
6. Tap "Contactado" → estado updated
7. **Total: 4 taps, 2 minutos**

---

## 🔧 Componentes

**Archivo wrapper**: `src/app/admin/cotizaciones/page.tsx` (39 líneas)
- Verifica auth + rol ADMIN/SUPERADMIN
- Redirige si no autorizado

**Manager principal**: `src/components/admin/AdminQuotesManager.tsx` (2,098 líneas) ⚠️ ARCHIVO GIGANTE
- Gestión completa desktop + mobile
- Lazy loading componentes pesados
- State consolidation (UIState, FilterState, OperationState)

**Componentes especializados**:
```typescript
// Lista y cards
- QuotesList.tsx - Lista/Cards responsive
- QuoteCard.tsx - Card individual touch-optimized
- SwipeableQuoteCard.tsx - Swipe-to-archive (mobile)

// Funcionalidad
- QuoteFilters.tsx - Filtros colapsables (mobile)
- QuoteDetailModal.tsx - Modal detalle completo

// Hooks
- useQuotes.ts - CRUD + realtime sync
- useQuoteActions.ts - Acciones (archivar, cambiar estado)
- useSimpleDevice.ts - Mobile detection
```

---

## 📊 Schema de Datos

**Firestore collection**: `quotes`

```typescript
interface Quote {
  // Identificación
  id: string;
  userId: string;              // Cliente que cotizó
  clienteNombre: string;
  clienteEmail: string;
  clienteTelefono: string;      // Para tap "Llamar"
  clienteWhatsapp?: string;

  // Datos cotización
  servicios: string[];
  descripcion: string;
  urgencia: 'baja' | 'media' | 'alta';
  presupuesto?: string;
  direccion: string;
  ciudad: string;

  // Estados admin (gestión)
  estado: 'nueva' | 'leida' | 'contactada' | 'cotizada' | 'aprobada' | 'rechazada' | 'en-obra' | 'finalizada';
  prioridad?: 'baja' | 'media' | 'alta';
  asignadoA?: string;
  precioEstimado?: number;
  notasAdmin?: string;

  // Metadata
  tipo: 'rapida' | 'detallada';
  createdAt: Timestamp;
  updatedAt: Timestamp;
  archivedAt?: Timestamp;       // Para FINALIZADAS
  isArchived: boolean;
}
```

**Sistema 3 estados** (CADENA tipo 4):
```
NUEVAS → LEÍDAS → FINALIZADAS
```

---

## ✅ Validaciones

**Auth middleware** (`page.tsx`):
```typescript
✅ Usuario autenticado
✅ Rol = ADMIN o SUPER_ADMIN
❌ Si CLIENT → redirect /auth/unauthorized
```

**Firestore Rules**:
```javascript
match /quotes/{quoteId} {
  // Admin puede leer TODAS (sin WHERE userId)
  allow list: if getUserRole() in ['ADMIN', 'SUPER_ADMIN'];

  // Admin puede actualizar estados
  allow update: if getUserRole() in ['ADMIN', 'SUPER_ADMIN'];
}
```

**Mobile UX validations**:
```typescript
✅ Touch targets ≥44px (iPhone standards)
✅ Swipe gestures enabled
✅ Lazy loading (no cargar 100+ quotes a la vez)
✅ Realtime sync con debounce (evitar re-renders)
```

---

## 🔗 Transacciones

**Entrada desde**:
- `3A.ADMIN.DESKTOP` (/admin) - FLUJO tipo 2
  - Jonathan navega desde dashboard desktop
- Sistema WiFi con Desktop:
  - **T19**: Admin Desktop ↔ Mobile WiFi tipo 3
  - Sincronización tiempo real entre dispositivos

**Salida hacia**:
- Modal detalle interno (no cambia URL)
- Acciones de estado:
  - **T12**: Relacionado con transacción Cliente → Admin (ASIMÉTRICA tipo 9)
  - **T13**: Cambio estado → Cliente notified (NOTIFICACIONES tipo 10)

**Transacciones de datos**:
- **T18**: Multiple quotes → convergencia en dashboard (CONVERGENCIA tipo 6)
- **T20**: Sistema 3 estados (CADENA tipo 4)

---

## 🐛 Errores Comunes

### 1. Realtime sync se desconecta
**Síntoma**: Quotes no actualizan en tiempo real
**Causa**: onSnapshot listener no cleanup
**Solución**:
```typescript
// useQuotes.ts líneas 45-80
useEffect(() => {
  const unsubscribe = db.collection('quotes')
    .where('isArchived', '==', false)
    .onSnapshot(snapshot => {
      // Update quotes
    });

  return () => unsubscribe(); // ✅ Cleanup
}, []);
```

### 2. Tap "Llamar" no abre teléfono (iOS)
**Síntoma**: Click no hace nada en iPhone
**Causa**: Formato tel: incorrecto
**Solución**:
```typescript
// Formatear teléfono para iOS
const formatTelLink = (telefono: string) => {
  // Remover todo excepto números
  const numbers = telefono.replace(/\D/g, '');
  return `tel:+${numbers}`; // ✅ +56912345678
};

<a href={formatTelLink(quote.clienteTelefono)}>
  Llamar
</a>
```

### 3. SwipeableQuoteCard lag en iPhone
**Síntoma**: Swipe se siente lento o cortado
**Causa**: Re-renders durante swipe
**Solución**:
```typescript
// SwipeableQuoteCard.tsx
const QuoteCardMemo = React.memo(QuoteCard, (prev, next) => {
  return prev.quote.id === next.quote.id &&
         prev.quote.estado === next.quote.estado;
});
```

### 4. Imágenes no cargan en mobile
**Síntoma**: Placeholder en vez de imágenes Firebase
**Causa**: CORS o URL sin proxy
**Solución**:
```typescript
// imageUtils.ts - Proxy Firebase Storage
const getProxiedImageUrl = (firebaseUrl: string) => {
  return `/api/proxy-image?url=${encodeURIComponent(firebaseUrl)}`;
};
```

---

## 🎯 Guardian

**Permisos**:
- Rol mínimo: `ADMIN`
- También: `SUPER_ADMIN`
- Middleware: ✅ Protegido en `src/middleware.ts`

```typescript
// middleware.ts
if (pathname.startsWith('/admin')) {
  if (role !== 'ADMIN' && role !== 'SUPER_ADMIN') {
    return redirectToUnauthorized();
  }
}
```

**Firestore permission**:
```javascript
// Admin puede ver TODAS las cotizaciones (sin filtro userId)
allow list: if getUserRole() in ['ADMIN', 'SUPER_ADMIN'];
```

---

## 💡 Notas

### Optimizaciones Mobile

**Touch targets** (iPhone standards):
```css
.btn-action {
  min-height: 44px;
  min-width: 44px;
  padding: 12px 16px;
}
```

**Lazy loading**:
- Solo cargar 20 quotes iniciales
- Scroll infinito para cargar más
- Imágenes con lazy loading nativo

**Realtime debounce**:
```typescript
const debouncedSync = useDebounce(quotes, 500);
// Evita re-render cada vez que Firestore actualiza
```

### Sistema 3 Estados

```yaml
Estado NUEVAS:
  - Recién recibidas
  - Badge rojo
  - Ordenadas por createdAt DESC

Estado LEÍDAS:
  - Admin las revisó
  - Puede agregar notas
  - Puede llamar/contactar

Estado FINALIZADAS:
  - Archivadas
  - No aparecen en vista principal
  - Swipe-to-archive
```

### Métricas Clave

```yaml
Tiempo promedio gestión: 2 min/quote
Taps promedio: 4 taps
Quotes procesadas/día: 15-25
Device: iPhone 90% del tiempo
```

### Mejoras Pendientes

```yaml
- [ ] Swipe gestures más suaves
- [ ] Vibration feedback al archivar
- [ ] Voice notes en vez de notas texto
- [ ] Geolocalización automática para rutas
```

---

**Creado**: 27-10-2025
**Tipo**: Agente Nodo Blockchain Viviente
**Estado**: ACTIVO
**Última actualización**: Basado en ROL_ADMIN_MOBILE_COTIZACIONES.md

