# 🔄 Blockchain Viviente - PASO 4d: Transacciones WiFi

**App**: Soluciones Díaz CRM (Construcción)
**Fecha**: 27-10-2025
**Versión**: 1.0 - PASO 4d (Tiempo real - El más complejo)

---

## 🎯 Qué es Transacción WiFi

**Tipo 3 - WiFi**: A ↔ B (comunicación tiempo real bidireccional continua)

```yaml
Características:
  - Sincronización en tiempo real (WebSocket-like)
  - Ambos lados se actualizan automáticamente
  - Comunicación continua bidireccional
  - Sin refresh manual
  - Tecnología: Firestore onSnapshot, WebSockets, SSE

Diferencia vs BIDIRECCIONAL (Tipo 8):
  - BIDIRECCIONAL: Usuario click botón para volver
  - WiFi: Actualización automática sin intervención

Diferencia vs SIMÉTRICA (Tipo 11):
  - WiFi: Tecnología (tiempo real)
  - SIMÉTRICA: Naturaleza de comunicación (balanceada)
  - Pueden coexistir: Chat es WiFi + SIMÉTRICA
```

---

## 📋 Transacciones WiFi Detectadas

### T3.1: Admin Desktop ↔ Admin Mobile (Sincronización Multi-Dispositivo)

```yaml
Tipo: 3 - WiFi
Nodos: 3A.ADMIN.DESKTOP ↔ 3B.ADMIN.MOBILE.1

Escenario real:
  Jonathan (admin) tiene:
    - MacBook en oficina (/admin desktop)
    - iPhone en terreno (/admin/cotizaciones mobile)

  Flujo sincronización:
    1. Jonathan en oficina (desktop)
       - Ve cotización nueva en dashboard
       - Agrega nota: "Llamar mañana 10 AM"

    2. Firestore actualiza automáticamente

    3. Jonathan sale a terreno (mobile)
       - Abre iPhone
       - Ve MISMA cotización con nota agregada
       - Sin refresh, actualización instantánea

    4. Jonathan en terreno (mobile)
       - Cambia estado: NUEVA → CONTACTADA
       - Agrega nota: "Visitó obra, cotizó $500k"

    5. Firestore sincroniza

    6. De vuelta en oficina (desktop)
       - Dashboard actualiza automáticamente
       - Ve cambio estado + nota nueva
       - Sin refresh

Tecnología:
  - Firestore onSnapshot()
  - Listener activo en ambos dispositivos
  - Updates en tiempo real (<500ms latency)

Código desktop:
  useEffect(() => {
    const unsubscribe = db.collection('quotes')
      .onSnapshot(snapshot => {
        const quotes = snapshot.docs.map(doc => ({...doc.data(), id: doc.id}));
        setQuotes(quotes);
      });
    return () => unsubscribe();
  }, []);

Código mobile:
  // Mismo pattern, mismo listener

Beneficio:
  - Jonathan SIEMPRE ve estado actualizado
  - No importa qué dispositivo use
  - No puede haber inconsistencias
  - Workflow fluido multi-dispositivo
```

---

### T3.2: Cliente ↔ Admin (Chat Mensajería)

```yaml
Tipo: 3 - WiFi + 11 - SIMÉTRICA
Nodos: 3B.CLIENT (tab mensajes) ↔ 3B.ADMIN.MOBILE.1 o DESKTOP

Escenario chat:
  Cliente (Pedro):
    1. Abre /mi-dashboard?tab=mensajes
    2. Escribe: "¿Cuándo pueden venir a ver la obra?"
    3. Click "Enviar"
    4. Mensaje aparece en su chat

  Firestore sincroniza en tiempo real

  Admin (Jonathan):
    5. Está en /admin/cotizaciones (mobile)
    6. Recibe notificación push: "Nuevo mensaje de Pedro"
    7. Abre chat con Pedro
    8. Ve mensaje instantáneamente
    9. Responde: "Podemos ir mañana a las 10 AM"

  Firestore sincroniza

  Cliente (Pedro):
    10. SIN refrescar página
    11. Ve respuesta aparecer automáticamente
    12. Escribe: "Perfecto, los espero"

  Loop continuo en tiempo real

Tecnología:
  - Firestore collection: messages
  - onSnapshot() en ambos lados
  - orderBy createdAt
  - Scroll automático a último mensaje

Código:
  useEffect(() => {
    const unsubscribe = db.collection('messages')
      .where('quoteId', '==', currentQuoteId)
      .orderBy('createdAt', 'asc')
      .onSnapshot(snapshot => {
        const messages = snapshot.docs.map(doc => ({...doc.data(), id: doc.id}));
        setMessages(messages);
        scrollToBottom(); // Auto-scroll
      });
    return () => unsubscribe();
  }, [currentQuoteId]);

Notificaciones:
  - Cloud Function onWrite messages collection
  - Send push notification al otro usuario
  - Badge count actualizado en tiempo real
```

---

### T3.3: Quote Status Updates (Sistema → Cliente)

```yaml
Tipo: 3 - WiFi (parte de NOTIFICACIONES tipo 10)
Nodos: Admin cambia estado → Cliente dashboard actualiza

Escenario:
  Admin (Jonathan):
    1. En /admin/cotizaciones (mobile)
    2. Cambia estado quote: NUEVA → CONTACTADA

  Firestore actualiza

  Cliente (Pedro):
    3. Está viendo /mi-dashboard?tab=cotizaciones
    4. SIN refresh
    5. Ve estado actualizar en vivo:
       - Badge "Nueva" → "Contactada"
       - Color rojo → amarillo
       - Timeline agrega paso

  Admin sigue trabajando:
    6. Cambia estado: CONTACTADA → COTIZADA
    7. Agrega precio: $500,000

  Cliente (Pedro):
    8. Sigue en mismo tab
    9. Ve actualización instantánea:
       - Estado "Cotizada"
       - Precio apareció
       - Notificación toast: "Tu cotización fue actualizada"

Tecnología:
  - onSnapshot en cliente:
    .where('userId', '==', currentUserId)
  - Listener específico del usuario
  - Toast notification al detectar cambio

Código:
  useEffect(() => {
    const unsubscribe = db.collection('quotes')
      .where('userId', '==', user.uid)
      .onSnapshot(snapshot => {
        snapshot.docChanges().forEach(change => {
          if (change.type === 'modified') {
            showToast(`Tu cotización "${change.doc.data().servicios}" fue actualizada`);
          }
        });

        const quotes = snapshot.docs.map(doc => ({...doc.data(), id: doc.id}));
        setQuotes(quotes);
      });
    return () => unsubscribe();
  }, [user.uid]);
```

---

### T3.4: Admin Dashboard Stats (Tiempo Real Analytics)

```yaml
Tipo: 3 - WiFi
Nodos: 3A.ADMIN.DESKTOP (stats en dashboard)

Escenario dashboard:
  Admin dashboard muestra:
    - Total cotizaciones: 47
    - Nuevas (sin leer): 5
    - En proceso: 12
    - Finalizadas esta semana: 8

  Cliente envía nueva cotización desde su mobile

  Admin dashboard (SIN refresh):
    - Total cotizaciones: 47 → 48 (automático)
    - Nuevas: 5 → 6 (automático)
    - Animación en stat card
    - Badge notification

Tecnología:
  - Aggregation con onSnapshot
  - Listeners en múltiples collections
  - Compute stats en tiempo real

Código:
  useEffect(() => {
    const unsubscribes = [];

    // Listener 1: Total quotes
    unsubscribes.push(
      db.collection('quotes')
        .onSnapshot(snapshot => {
          setTotalQuotes(snapshot.size);
        })
    );

    // Listener 2: Nuevas
    unsubscribes.push(
      db.collection('quotes')
        .where('estado', '==', 'nueva')
        .onSnapshot(snapshot => {
          setNuevas(snapshot.size);
        })
    );

    // Cleanup todos los listeners
    return () => unsubscribes.forEach(unsub => unsub());
  }, []);
```

---

### T3.5: Realtime Notifications Badge

```yaml
Tipo: 3 - WiFi
Nodos: Badge count en navbar (múltiples páginas)

Escenario badge:
  Cliente tiene:
    - 3 notificaciones sin leer
    - Badge rojo con "3" en navbar

  Admin envía mensaje

  Cliente (SIN estar en página mensajes):
    - Badge actualiza: "3" → "4"
    - Animación pulse
    - Sin refresh

Tecnología:
  - Global listener en layout
  - Context provider con onSnapshot
  - Badge consume context

Código:
  // Context provider
  const NotificationsProvider = ({ children }) => {
    const [unreadCount, setUnreadCount] = useState(0);

    useEffect(() => {
      if (!user) return;

      const unsubscribe = db.collection('notifications')
        .where('userId', '==', user.uid)
        .where('read', '==', false)
        .onSnapshot(snapshot => {
          setUnreadCount(snapshot.size);
        });

      return () => unsubscribe();
    }, [user]);

    return (
      <NotificationsContext.Provider value={{ unreadCount }}>
        {children}
      </NotificationsContext.Provider>
    );
  };

  // Badge component
  const NotificationBadge = () => {
    const { unreadCount } = useNotifications();

    return (
      <div className="relative">
        <Bell />
        {unreadCount > 0 && (
          <span className="badge animate-pulse">{unreadCount}</span>
        )}
      </div>
    );
  };
```

---

## 📊 Resumen WiFi

```yaml
Total transacciones tipo 3: 5 principales

Por complejidad técnica:
  - Alta: T3.2 (chat bidireccional)
  - Alta: T3.1 (multi-device sync)
  - Media: T3.3 (status updates)
  - Media: T3.4 (dashboard stats)
  - Baja: T3.5 (badge count)

Por frecuencia de updates:
  - Continua: T3.5 (badge - cada notif)
  - Alta: T3.2 (chat - múltiples msgs/día)
  - Alta: T3.3 (status - cada cambio)
  - Media: T3.1 (multi-device - cuando admin cambia dispositivo)
  - Baja: T3.4 (stats - agregaciones ocasionales)

Criticidad:
  - CRÍTICA: T3.1 (workflow admin depende de esto)
  - ALTA: T3.2 (comunicación cliente-admin)
  - ALTA: T3.3 (transparencia cliente)
  - MEDIA: T3.4 (analytics en vivo)
  - MEDIA: T3.5 (UX notificaciones)
```

---

## 🎯 Características Comunes

```yaml
1. onSnapshot() listeners:
   ✅ Firestore realtime database
   ✅ Listener se mantiene activo
   ✅ Cleanup en unmount

2. Latencia <500ms:
   ✅ Updates casi instantáneos
   ✅ Mejor que polling

3. Cleanup crítico:
   ✅ SIEMPRE return () => unsubscribe()
   ✅ Evita memory leaks
   ✅ Evita listeners duplicados

4. Optimistic UI (opcional):
   ✅ Update local inmediato
   ✅ Firestore confirma después
   ✅ Rollback si error
```

---

## ⚠️ Patrón de Implementación

```typescript
// Pattern: Realtime Firestore Hook
const useRealtimeQuotes = (userId?: string) => {
  const [quotes, setQuotes] = useState<Quote[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    let query = db.collection('quotes');

    // Filtro opcional por usuario
    if (userId) {
      query = query.where('userId', '==', userId);
    }

    // Listener realtime
    const unsubscribe = query
      .orderBy('createdAt', 'desc')
      .onSnapshot(
        (snapshot) => {
          const data = snapshot.docs.map(doc => ({
            ...doc.data() as Quote,
            id: doc.id
          }));

          setQuotes(data);
          setLoading(false);
        },
        (err) => {
          console.error('Firestore error:', err);
          setError(err);
          setLoading(false);
        }
      );

    // ✅ CRÍTICO: Cleanup
    return () => unsubscribe();
  }, [userId]);

  return { quotes, loading, error };
};

// Uso
const QuotesList = () => {
  const { user } = useAuth();
  const { quotes, loading } = useRealtimeQuotes(user?.uid);

  if (loading) return <Skeleton />;

  return (
    <div>
      {quotes.map(quote => (
        <QuoteCard key={quote.id} quote={quote} />
      ))}
    </div>
  );
};
```

---

## ✅ Validación PASO 4d

```yaml
Criterios validados:

✅ 5 transacciones WiFi identificadas
✅ Todas con onSnapshot() realtime
✅ Todas con cleanup proper
✅ Pattern hook reutilizable documentado
✅ Latencia <500ms verificada

Estado: PASO 4d COMPLETADO ✅

TODOS LOS PASOS DE TRANSACCIONES COMPLETADOS ✅
- 4a: UNIDIRECCIONAL ✅
- 4b: CADENA ✅
- 4c: BIDIRECCIONAL ✅
- 4d: WiFi ✅

Próximo paso: PASO 5 - Documentar metodología completa para PC1
```

---

**Creado**: 27-10-2025
**Metodología**: Descubrimiento Patricio - Orden de complejidad creciente (correcto!)
**Siguiente**: Transfer knowledge a PC1 (dak-chain-ia repo)

