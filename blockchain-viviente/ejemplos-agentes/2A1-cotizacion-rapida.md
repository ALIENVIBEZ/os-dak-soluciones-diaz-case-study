# 🤖 Agente: Cotización Rápida

## 🎯 Identificación

**Nodo**: 2A1.CLIENT
**URL**: `/cotizacion/rapida`
**Rol**: CLIENT (autenticado)
**Dispositivo**: DESKTOP + MOBILE (responsive)
**Tipo**: Túnel (formulario de envío)
**Prioridad**: CRÍTICA (conversión principal)

---

## 📝 Descripción

Formulario rápido de cotización diseñado para clientes que quieren una estimación aproximada en 24h. Optimizado para completarse en ~2 minutos sin fotos ni medidas exactas.

**Diferenciador vs Detallada**:
- ✅ 2 min (vs 10-15 min)
- ✅ Sin fotos requeridas
- ✅ Estimación aproximada (vs precisa)
- ✅ Auto-load de perfil

---

## 🔧 Componentes

**Archivo principal**: `src/app/cotizacion/rap ida/page.tsx` (898 líneas)

**Stack**:
- Next.js 15.4.2 App Router
- React 18 + TypeScript
- Firebase Auth Context
- Tailwind CSS

**Hooks utilizados**:
```typescript
const { user, loading, userData } = useFirebaseAuth();
const { isAuthenticated, isLoading } = useAuth({ required: true });
```

---

## 📊 Schema de Datos

**Firestore collection**: `quotes`

```typescript
interface QuoteRapida {
  // Auto-load desde perfil
  nombre: string;
  email: string;
  telefono: string;      // Formato: +56 9 XXXX XXXX
  whatsapp: string;
  direccion: string;
  ciudad: string;        // Default: "Punta Arenas"

  // Usuario llena
  servicios: string[];   // Multi-select: Pisos, Paredes, Techos, Ampliaciones
  descripcion: string;   // Textarea libre
  urgencia: 'baja' | 'media' | 'alta';  // Default: 'media'
  presupuesto: string;   // Rango estimado

  // Metadata sistema
  userId: string;
  tipo: 'rapida';
  estado: 'nueva';
  createdAt: Timestamp;
}
```

**API Endpoints**:
- `GET /api/client/profile-autofill` - Auto-load perfil
- `POST /api/client/quotes/submit` - Enviar cotización

---

## ✅ Validaciones

**Frontend** (`page.tsx` líneas 200-250):
```typescript
// Campos requeridos
✅ nombre (min 3 caracteres)
✅ email (formato válido)
✅ telefono (formato +56 9 XXXX XXXX)
✅ direccion (min 5 caracteres)
✅ servicios (min 1 seleccionado)
✅ descripcion (min 20 caracteres, max 500)

// Validación teléfono chileno
/^\+56\s9\s\d{4}\s\d{4}$/.test(telefono)
```

**Backend** (`/api/client/quotes/submit/route.ts`):
```typescript
// Triple validación
1. Auth: Verificar usuario autenticado
2. Data: Validar campos requeridos + formato
3. Firestore: Verificar quote no duplicada (mismo user + 5 min)
```

---

## 🔗 Transacciones

**Entrada desde**:
- `1A.CLIENT` (/cotizacion) - FLUJO tipo 2
  - Usuario click "Cotización Rápida" en selector

**Salida hacia**:
- `2A.CLIENT.CONFIRM` (/cotizacion/confirmacion/[id]) - UNIDIRECCIONAL tipo 7
  - Post-submit (irreversible)
  - No puede volver atrás, formulario se limpia

**Transacciones relacionadas**:
- **T12**: Cliente envía quote → Admin recibe (ASIMÉTRICA tipo 9)
- **T13**: Sistema notifica admin automáticamente (NOTIFICACIONES tipo 10)

---

## 🐛 Errores Comunes

### 1. Auto-load falla
**Síntoma**: Campos nombre/email vacíos
**Causa**: Usuario no tiene perfil completo
**Solución**:
```typescript
// Líneas 142-172: Catch error y usar valores auth
if (!response.ok) {
  setFormData({
    nombre: user?.displayName || '',
    email: user?.email || '',
    // ... fallback a auth data
  });
}
```

### 2. Teléfono formato inválido
**Síntoma**: Error "Formato de teléfono inválido"
**Causa**: Usuario ingresa sin +56 o sin espacios
**Solución**:
```typescript
// Auto-formatear mientras escribe
const formatearTelefono = (value: string) => {
  const numbers = value.replace(/\D/g, '');
  if (numbers.startsWith('56')) {
    return `+${numbers.slice(0,2)} ${numbers[2]} ${numbers.slice(3,7)} ${numbers.slice(7,11)}`;
  }
  return value;
};
```

### 3. Submit duplicado (doble click)
**Síntoma**: 2 quotes creadas en segundos
**Causa**: Usuario hace doble click en "Enviar"
**Solución**:
```typescript
// Líneas 280-285: Disable button durante submit
const [isSubmitting, setIsSubmitting] = useState(false);

<button disabled={isSubmitting || !isFormValid}>
  {isSubmitting ? 'Enviando...' : 'Enviar Cotización'}
</button>
```

**Backend prevención** (route.ts):
```typescript
// Verificar duplicado en últimos 5 min
const recentQuote = await db.collection('quotes')
  .where('userId', '==', userId)
  .where('createdAt', '>', fiveMinutesAgo)
  .get();

if (!recentQuote.empty) {
  return Response.json({ error: 'Ya enviaste una cotización reciente' }, { status: 429 });
}
```

---

## 🎯 Guardian

**Permisos**:
- Rol mínimo: `CLIENT`
- Middleware: ✅ Protegido en `src/middleware.ts`
- Firestore Rules: ✅ Solo owner puede leer su quote

```javascript
// Firestore Rules
match /quotes/{quoteId} {
  allow create: if request.auth != null &&
                   getUserRole() in ['CLIENT', 'ADMIN', 'SUPER_ADMIN'];

  allow read: if request.auth != null &&
                 (resource.data.userId == request.auth.uid ||
                  getUserRole() in ['ADMIN', 'SUPER_ADMIN']);
}
```

---

## 💡 Notas

### Optimizaciones implementadas
- ✅ Auto-load de perfil (ahorra ~1 min)
- ✅ Multi-select servicios (vs checkboxes)
- ✅ Auto-formateo teléfono
- ✅ Prevención submit duplicado

### Métricas clave
- Tiempo promedio: 2 min 15 seg
- Tasa conversión: 67% (completan formulario)
- Abandono: 33% (mayoría en descripción)

### Mejoras pendientes
- [ ] Auto-save draft cada 30s
- [ ] Geolocalización automática dirección
- [ ] Sugerencias IA en descripción

---

**Creado**: 27-10-2025
**Tipo**: Agente Nodo Blockchain Viviente
**Estado**: ACTIVO
**Última actualización**: Basado en ROL_CLIENTE_CAPA_2A1_COTIZACION_RAPIDA.md

