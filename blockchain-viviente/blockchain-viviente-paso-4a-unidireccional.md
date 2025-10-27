# 🔄 Blockchain Viviente - PASO 4a: Transacciones UNIDIRECCIONAL

**App**: Soluciones Díaz CRM (Construcción)
**Fecha**: 27-10-2025
**Versión**: 1.0 - PASO 4a (Transacciones simples irreversibles)

---

## 🎯 Qué es Transacción UNIDIRECCIONAL

**Tipo 7 - UNIDIRECCIONAL**: A → B (no se puede regresar)

```yaml
Características:
  - Flujo de una sola vía (forward-only)
  - Acción irreversible
  - Usuario NO puede volver a A después de ir a B
  - Ejemplos: Confirmar pago, Enviar formulario, Delete permanente

Diferencia vs FLUJO (Tipo 2):
  - FLUJO: A → B (puede regresar con botón "Atrás")
  - UNIDIRECCIONAL: A → B (NO hay vuelta atrás)
```

---

## 📋 Transacciones UNIDIRECCIONAL Detectadas

### T7.1: Formulario Cotización → Confirmación

```yaml
Tipo: 7 - UNIDIRECCIONAL
Nodos: 2A1.CLIENT → 2A.CLIENT.CONFIRM

Flujo irreversible:
  1. Cliente completa formulario (/cotizacion/rapida)
  2. Click "Enviar Cotización"
  3. Sistema crea registro en Firestore
  4. Redirect a /cotizacion/confirmacion/[id]
  5. ❌ NO puede volver atrás
     - Formulario se limpia
     - Quote ya está en database
     - No hay botón "Editar"

Por qué irreversible:
  - Quote enviada a admin
  - Admin puede estar viéndola ya
  - Timestamp registrado
  - Si quiere cambiar → debe crear nueva quote

Código:
  - submit: src/app/cotizacion/rapida/page.tsx líneas 280-320
  - redirect: router.push(`/cotizacion/confirmacion/${quoteId}`)
  - cleanup: setFormData(initialState) // ✅ Formulario limpio
```

**Variantes** (mismo patrón):
- 2A2.CLIENT → 2A.CLIENT.CONFIRM (Cotización detallada)
- 2A3.CLIENT → 2A.CLIENT.CONFIRM (Cotización wizard)
- 2A4.CLIENT → 2A.CLIENT.CONFIRM (Cotización optimizada)

---

### T7.2: Signup → Verify Email

```yaml
Tipo: 7 - UNIDIRECCIONAL
Nodos: /auth/signin (signup) → /auth/verify

Flujo irreversible:
  1. Usuario completa signup
  2. Firebase crea cuenta
  3. Sistema envía email verificación
  4. Redirect a /auth/verify
  5. ❌ NO puede volver a signup
     - Cuenta ya creada
     - Email ya enviado
     - Si vuelve → "Email ya en uso"

Por qué irreversible:
  - Cuenta creada en Firebase Auth
  - Email de verificación enviado (1 vez)
  - UID asignado
  - Para "cancelar" → debe eliminar cuenta (otro flujo)

Código:
  - create: Firebase Auth createUserWithEmailAndPassword()
  - send email: sendEmailVerification()
  - redirect: router.push('/auth/verify')
```

---

### T7.3: Admin Archive Quote (Swipe)

```yaml
Tipo: 7 - UNIDIRECCIONAL
Nodos: 3B.ADMIN.MOBILE.1 (estado FINALIZADA) → Archivado

Flujo irreversible:
  1. Admin swipe left en quote mobile
  2. Aparece botón "Archivar"
  3. Tap "Archivar"
  4. Quote.isArchived = true + archivedAt = Timestamp
  5. Quote desaparece de vista principal
  6. ❌ NO aparece botón "Desarchivar" en mobile
     - Solo desde desktop se puede desarchivar
     - Requiere ir a "Archivadas" (otra vista)

Por qué irreversible (en mobile):
  - Quote sale de vista principal
  - Usuario mobile no tiene acceso fácil a deshacer
  - Acción confirmada (no es drag temporal)

Código:
  - swipe: SwipeableQuoteCard.tsx
  - archive: useQuoteActions.ts → archiveQuote()
  - update: db.collection('quotes').doc(id).update({ isArchived: true })
```

---

### T7.4: Delete Service/Material (Admin)

```yaml
Tipo: 7 - UNIDIRECCIONAL
Nodos: 3B.ADMIN.DESKTOP.1 o .2 (CRUD)

Flujo irreversible:
  1. Admin en /admin/servicios o /admin/materiales
  2. Click "Eliminar" en servicio/material
  3. Modal confirmación: "¿Estás seguro?"
  4. Click "Sí, eliminar"
  5. Firestore: doc.delete()
  6. ❌ NO hay "Papelera de reciclaje"
     - Eliminado permanentemente
     - No se puede recuperar desde UI

Por qué irreversible:
  - No soft delete (no isDeleted: true)
  - Hard delete de Firestore
  - Para recuperar → necesita backup de database

Código:
  - delete: db.collection('services').doc(id).delete()
  - confirm modal: antes de ejecutar
  - ⚠️ MEJORA PENDIENTE: Implementar soft delete
```

---

### T7.5: Admin Finalizar Obra

```yaml
Tipo: 7 - UNIDIRECCIONAL
Nodos: Quote estado "en-obra" → "finalizada"

Flujo irreversible:
  1. Admin marca cotización como "En Obra"
  2. Obra transcurre (días/semanas)
  3. Admin marca "Finalizada"
  4. Quote.estado = 'finalizada' + finalizedAt = Timestamp
  5. ❌ NO puede volver a "en-obra"
     - Implica obra completada
     - Facturación generada
     - Cliente notificado

Por qué irreversible:
  - Implica facturación/cobro
  - Cliente recibe notificación final
  - Métricas de negocio actualizadas
  - Para corregir → crear nueva quote

Código:
  - update: useQuoteActions.ts → updateQuoteStatus('finalizada')
  - notification trigger: Cloud Function onUpdate
```

---

## 📊 Resumen UNIDIRECCIONAL

```yaml
Total transacciones tipo 7: 5 principales

Por rol:
  - CLIENT: 2 (formulario submit, signup)
  - ADMIN: 3 (archive, delete, finalizar obra)

Por frecuencia:
  - Alta: T7.1 (formulario submit - múltiples veces/día)
  - Media: T7.3 (archive - 5-10 veces/día)
  - Baja: T7.2 (signup - ocasional)
  - Baja: T7.4 (delete - ocasional)
  - Media: T7.5 (finalizar obra - 2-3 veces/semana)

Criticidad:
  - CRÍTICA: T7.1 (conversión principal)
  - ALTA: T7.5 (afecta facturación)
  - MEDIA: T7.3 (gestión)
  - BAJA: T7.2, T7.4
```

---

## 🎯 Características Comunes

Todas las transacciones UNIDIRECCIONAL comparten:

```yaml
1. Confirmación explícita:
   ✅ Modal "¿Estás seguro?"
   ✅ Botón específico "Enviar" o "Confirmar"
   ✅ No es accidental (requiere tap/click intencional)

2. Cambio de estado permanente:
   ✅ Database write
   ✅ Timestamp registrado
   ✅ No hay "Deshacer" fácil

3. Redirect o cambio vista:
   ✅ Usuario sale del contexto original
   ✅ No hay botón "Volver" que restaure estado anterior

4. Side effects:
   ✅ Notificaciones enviadas
   ✅ Otros usuarios afectados
   ✅ Métricas/analytics registradas
```

---

## ⚠️ Prevención de Errores

### Patrón: Confirmation Modal

```typescript
// Antes de acción irreversible
const handleIrreversibleAction = async () => {
  // Paso 1: Confirmación
  const confirmed = await showConfirmModal({
    title: "¿Estás seguro?",
    message: "Esta acción NO se puede deshacer",
    confirmText: "Sí, continuar",
    cancelText: "Cancelar"
  });

  if (!confirmed) return;

  // Paso 2: Disable button (evitar doble click)
  setIsProcessing(true);

  try {
    // Paso 3: Ejecutar acción
    await executeIrreversibleAction();

    // Paso 4: Success feedback
    showToast("Acción completada exitosamente");

    // Paso 5: Redirect
    router.push('/success-page');
  } catch (error) {
    // Paso 6: Error handling
    showToast("Error: " + error.message, 'error');
    // NO redirect en error (permite retry)
  } finally {
    setIsProcessing(false);
  }
};
```

### Patrón: Undo dentro de Timeout

```typescript
// Alternativa: Permitir "undo" en 5 segundos
const handleSubmitWithUndo = async () => {
  // Paso 1: Acción temporal
  const tempQuoteId = await createTempQuote();

  // Paso 2: Toast con undo
  const toast = showToast(
    "Cotización enviada",
    {
      action: {
        label: "Deshacer",
        onClick: () => deleteTempQuote(tempQuoteId)
      },
      duration: 5000 // 5 segundos
    }
  );

  // Paso 3: Después de 5s → permanente
  setTimeout(async () => {
    if (!toast.dismissed) {
      await finalizeQuote(tempQuoteId);
      router.push(`/confirmacion/${tempQuoteId}`);
    }
  }, 5000);
};
```

---

## ✅ Validación PASO 4a

```yaml
Criterios validados:

✅ 5 transacciones UNIDIRECCIONAL identificadas
✅ Todas con confirmación explícita
✅ Todas con cambio estado permanente
✅ Patrón de prevención documentado
✅ Alternativa "undo timeout" documentada

Estado: PASO 4a COMPLETADO ✅
Próximo paso: PASO 4b - Transacciones CADENA (secuencias A → B → C)
```

---

**Creado**: 27-10-2025
**Metodología**: Descubrimiento Patricio - Orden de complejidad creciente
**Siguiente**: Mapear transacciones CADENA

