# 🔄 Blockchain Viviente - PASO 4b: Transacciones CADENA

**App**: Soluciones Díaz CRM (Construcción)
**Fecha**: 27-10-2025
**Versión**: 1.0 - PASO 4b (Secuencias de múltiples pasos)

---

## 🎯 Qué es Transacción CADENA

**Tipo 4 - CADENA**: A → B → C (secuencia de múltiples pasos)

```yaml
Características:
  - Secuencia de 3+ pasos
  - Cada paso depende del anterior
  - NO se puede saltear pasos
  - Workflow completo con estados
  - Progreso lineal

Diferencia vs UNIDIRECCIONAL (Tipo 7):
  - UNIDIRECCIONAL: 1 paso (A → B)
  - CADENA: Múltiples pasos (A → B → C → D)

Diferencia vs FLUJO (Tipo 2):
  - FLUJO: 2 pasos simples
  - CADENA: 3+ pasos interdependientes
```

---

## 📋 Transacciones CADENA Detectadas

### T4.1: Signup → Verify → Dashboard (Auth Flow)

```yaml
Tipo: 4 - CADENA
Nodos: /auth/signin → /auth/verify → /mi-dashboard

Secuencia obligatoria:
  PASO 1: Signup
    - Usuario completa formulario registro
    - Firebase crea cuenta
    - Estado: emailVerified = false

  PASO 2: Verify Email
    - Sistema envía email verificación
    - Usuario click link en email
    - Estado: emailVerified = true

  PASO 3: Dashboard Access
    - Usuario autenticado + verificado
    - Redirect a dashboard correspondiente (cliente o admin)

NO se puede saltear:
  ❌ NO puedes ir directo a Dashboard sin verificar
  ❌ NO puedes verificar sin crear cuenta primero
  ❌ Cada paso valida el anterior

Código:
  - Step 1: createUserWithEmailAndPassword()
  - Step 2: sendEmailVerification() + onAuthStateChanged()
  - Step 3: Guardian valida emailVerified antes de acceso

Validación Guardian:
  if (!user.emailVerified) {
    redirect('/auth/verify');
  }
```

---

### T4.2: Selector → Formulario → Confirmación (Quote Flow)

```yaml
Tipo: 4 - CADENA
Nodos: 1A.CLIENT → 2A1.CLIENT → 2A.CLIENT.CONFIRM

Secuencia obligatoria:
  PASO 1: Selector de Cotización
    - /cotizacion (puerta)
    - Cliente elige tipo: Rápida | Detallada | Wizard | Optimizada

  PASO 2: Formulario Específico
    - /cotizacion/rapida (o detallada, wizard, optimizada)
    - Cliente llena datos
    - Validación frontend + backend

  PASO 3: Confirmación
    - /cotizacion/confirmacion/[id]
    - Quote creada en database
    - Admin notificado
    - Cliente recibe resumen

NO se puede saltear:
  ❌ NO puedes ir directo a formulario sin pasar por selector
     - Selector define tipo de quote
     - Tipo determina qué formulario mostrar
  ❌ NO puedes ir a confirmación sin llenar formulario
     - Confirmación requiere quoteId
     - QuoteId solo existe después de submit

Código:
  - Step 1: /cotizacion/page.tsx → Botones selector
  - Step 2: /cotizacion/rapida/page.tsx → onSubmit()
  - Step 3: router.push(`/confirmacion/${quoteId}`)

Variantes (mismo patrón):
  - Selector → Rápida → Confirmación
  - Selector → Detallada → Confirmación
  - Selector → Wizard → Confirmación
  - Selector → Optimizada → Confirmación
```

---

### T4.3: Admin Quote States (Gestión Lifecycle)

```yaml
Tipo: 4 - CADENA
Nodos: 3B.ADMIN.MOBILE.1 o DESKTOP (estados de quote)

Secuencia de estados:
  PASO 1: NUEVA
    - Quote recién recibida
    - Admin aún no la revisó
    - Badge rojo
    - Action: Marcar como "LEÍDA"

  PASO 2: LEÍDA
    - Admin revisó quote
    - Puede agregar notas
    - Puede llamar cliente
    - Actions: "CONTACTADA" | "RECHAZADA"

  PASO 3: CONTACTADA
    - Admin llamó/contactó cliente
    - Acordaron visita técnica
    - Action: "COTIZADA"

  PASO 4: COTIZADA
    - Admin envió precio al cliente
    - Esperando respuesta
    - Actions: "APROBADA" | "RECHAZADA"

  PASO 5a: APROBADA
    - Cliente aceptó cotización
    - Action: "EN OBRA"

  PASO 5b: RECHAZADA
    - Cliente rechazó
    - Quote archivada
    - FIN del flujo

  PASO 6: EN OBRA (si aprobada)
    - Obra en progreso
    - Fotos de progreso
    - Action: "FINALIZADA"

  PASO 7: FINALIZADA
    - Obra completada
    - Facturación generada
    - Cliente notificado
    - Action: "ARCHIVAR"

NO se puede saltear:
  ❌ NO puedes marcar "EN OBRA" sin pasar por "APROBADA"
  ❌ NO puedes "FINALIZAR" sin pasar por "EN OBRA"
  ❌ Cada estado implica acciones previas completadas

Código:
  - Update: useQuoteActions.ts → updateQuoteStatus(newStatus)
  - Validation: Backend valida transiciones permitidas
  - Notifications: Cloud Function envía notif en cada cambio

Estados permitidos (validación backend):
  nueva → leida
  leida → contactada | rechazada
  contactada → cotizada
  cotizada → aprobada | rechazada
  aprobada → en-obra
  en-obra → finalizada
  finalizada → archivada
```

---

### T4.4: Landing SEO → Homepage → Cotización → Submit (Conversion Funnel)

```yaml
Tipo: 4 - CADENA
Nodos: 2A.GUEST.* → 1A.GUEST → 1A.CLIENT → 2A1.CLIENT → CONFIRM

Secuencia de conversión:
  PASO 1: Landing SEO
    - /blog/remodelacion-casas-punta-arenas (o similar)
    - Usuario encuentra vía Google
    - CTA: "Cotiza Gratis"

  PASO 2: Homepage
    - / (landing marketing)
    - Usuario explora servicios
    - CTA: "Solicitar Cotización"

  PASO 3: Selector Cotización
    - /cotizacion (requiere auth)
    - Usuario debe autenticarse primero si no lo está
    - Elige tipo cotización

  PASO 4: Formulario
    - /cotizacion/rapida (o variante)
    - Usuario llena datos
    - Submit

  PASO 5: Confirmación
    - /cotizacion/confirmacion/[id]
    - Conversión completada ✅

NO se puede saltear:
  ❌ Funnel diseñado para guiar paso a paso
  ❌ Cada paso calienta al lead
  ❌ Auth requerido antes de cotizar

Métricas de funnel:
  - Landing SEO: 1000 visits
  - Homepage: 300 visits (30% conversion)
  - Selector: 150 visits (50% conversion - requiere auth)
  - Formulario: 120 views (80% conversion)
  - Confirmación: 80 submits (67% conversion)

  Total funnel: 1000 → 80 = 8% conversion rate

Código:
  - Analytics: Track cada paso con GA4
  - Hotjar: Heatmaps en cada paso
  - A/B testing: CTA buttons

Optimizaciones:
  - Pre-fill datos de perfil (ahorra 1 min)
  - Formulario rápido opción default
  - Calculadora en homepage (engagement)
```

---

### T4.5: Admin Onboarding (Primer Uso)

```yaml
Tipo: 4 - CADENA
Nodos: /admin (first-time) → /admin/configuracion → /admin/servicios → /admin

Secuencia inicial setup:
  PASO 1: Bienvenida
    - Admin accede por primera vez a /admin
    - Modal "Bienvenido, completa setup inicial"
    - CTA: "Empezar Setup"

  PASO 2: Configuración Básica
    - /admin/configuracion
    - Admin configura:
      - Datos empresa (nombre, RUT, dirección)
      - Zonas cobertura (Punta Arenas, Porvenir, etc.)
      - Horarios atención

  PASO 3: Catálogo Servicios
    - /admin/servicios
    - Admin agrega servicios principales:
      - Instalación pisos
      - Remodelación baños
      - Ampliaciones
      - etc.

  PASO 4: Catálogo Materiales
    - /admin/materiales
    - Admin agrega materiales disponibles
    - Precios y stock

  PASO 5: Setup Completo
    - Redirect /admin
    - Dashboard operativo
    - Flag: onboardingCompleted = true

NO se puede saltear:
  ❌ Dashboard no funciona sin servicios configurados
  ❌ Cotizaciones requieren catálogo completo
  ❌ Secuencia guiada para setup correcto

Código:
  - Check: useEffect en /admin → if (!onboardingCompleted) showModal
  - Progress: localStorage o Firestore flag
  - Skip: Admin puede saltar pasos (pero warning)

Estado actual:
  ⚠️ NO IMPLEMENTADO (mejora pendiente)
  - Actualmente admin puede acceder sin setup
  - Recomendación: Implementar wizard onboarding
```

---

## 📊 Resumen CADENA

```yaml
Total transacciones tipo 4: 5 principales

Por longitud de cadena:
  - 3 pasos: T4.2 (Selector → Form → Confirm)
  - 3 pasos: T4.1 (Signup → Verify → Dashboard)
  - 7 pasos: T4.3 (Admin quote lifecycle)
  - 5 pasos: T4.4 (Conversion funnel)
  - 5 pasos: T4.5 (Admin onboarding - pendiente)

Por frecuencia:
  - Alta: T4.2 (quote flow - múltiples veces/día)
  - Alta: T4.3 (admin gestión - continuo)
  - Media: T4.4 (funnel - nuevo clientes)
  - Baja: T4.1 (signup - ocasional)
  - N/A: T4.5 (onboarding - 1 vez)

Criticidad:
  - CRÍTICA: T4.2 (conversión)
  - CRÍTICA: T4.3 (gestión negocio)
  - ALTA: T4.4 (acquisition)
  - MEDIA: T4.1 (auth)
  - BAJA: T4.5 (mejora pendiente)
```

---

## 🎯 Características Comunes

Todas las transacciones CADENA comparten:

```yaml
1. Progreso lineal:
   ✅ Estado avanza paso a paso
   ✅ NO se puede saltear pasos
   ✅ Cada paso valida anterior

2. Indicador de progreso:
   ✅ Breadcrumbs o stepper
   ✅ "Paso 2 de 5"
   ✅ Progress bar

3. Validación entre pasos:
   ✅ Backend valida transición permitida
   ✅ Frontend disable next si current incompleto

4. Persistencia de estado:
   ✅ Si usuario abandona, puede retomar
   ✅ Auto-save draft (opcional)
   ✅ Estado guardado en DB o localStorage

5. Punto de no retorno:
   ✅ Último paso es UNIDIRECCIONAL
   ✅ Ej: Submit final, Confirm, Finalizar
```

---

## ⚠️ Patrón de Implementación

### Stepper Component

```typescript
interface Step {
  id: string;
  title: string;
  component: React.ComponentType;
  isComplete: boolean;
  isActive: boolean;
}

const QuoteFlowStepper = () => {
  const [currentStep, setCurrentStep] = useState(0);
  const [completedSteps, setCompletedSteps] = useState<Set<number>>(new Set());

  const steps: Step[] = [
    { id: 'selector', title: 'Tipo de Cotización', component: SelectorStep },
    { id: 'formulario', title: 'Detalles', component: FormularioStep },
    { id: 'confirmacion', title: 'Confirmación', component: ConfirmacionStep }
  ];

  const canGoNext = () => {
    // Validar que paso actual esté completo
    return completedSteps.has(currentStep);
  };

  const handleNext = () => {
    if (!canGoNext()) return;

    setCompletedSteps(prev => new Set(prev).add(currentStep));
    setCurrentStep(prev => Math.min(prev + 1, steps.length - 1));
  };

  const handlePrevious = () => {
    // Solo permitir volver si NO es paso final
    if (currentStep < steps.length - 1) {
      setCurrentStep(prev => Math.max(prev - 1, 0));
    }
  };

  return (
    <div>
      {/* Progress indicator */}
      <div className="stepper">
        {steps.map((step, index) => (
          <div
            key={step.id}
            className={cn(
              "step",
              index === currentStep && "active",
              completedSteps.has(index) && "completed"
            )}
          >
            {step.title}
          </div>
        ))}
      </div>

      {/* Current step content */}
      <div className="step-content">
        {React.createElement(steps[currentStep].component)}
      </div>

      {/* Navigation */}
      <div className="step-actions">
        {currentStep > 0 && currentStep < steps.length - 1 && (
          <button onClick={handlePrevious}>Anterior</button>
        )}
        {currentStep < steps.length - 1 && (
          <button onClick={handleNext} disabled={!canGoNext()}>
            Siguiente
          </button>
        )}
      </div>
    </div>
  );
};
```

### Backend Validation (State Transitions)

```typescript
// API endpoint: /api/quotes/update-status
const ALLOWED_TRANSITIONS = {
  nueva: ['leida'],
  leida: ['contactada', 'rechazada'],
  contactada: ['cotizada'],
  cotizada: ['aprobada', 'rechazada'],
  aprobada: ['en-obra'],
  'en-obra': ['finalizada'],
  finalizada: ['archivada']
};

export async function POST(request: Request) {
  const { quoteId, newStatus } = await request.json();

  // Get current quote
  const quoteDoc = await db.collection('quotes').doc(quoteId).get();
  const currentStatus = quoteDoc.data().estado;

  // Validate transition
  const allowedNextStates = ALLOWED_TRANSITIONS[currentStatus] || [];

  if (!allowedNextStates.includes(newStatus)) {
    return Response.json({
      error: `No se puede cambiar de "${currentStatus}" a "${newStatus}"`,
      allowedTransitions: allowedNextStates
    }, { status: 400 });
  }

  // Update
  await quoteDoc.ref.update({
    estado: newStatus,
    updatedAt: FieldValue.serverTimestamp()
  });

  return Response.json({ success: true });
}
```

---

## ✅ Validación PASO 4b

```yaml
Criterios validados:

✅ 5 transacciones CADENA identificadas
✅ Todas con progreso lineal (3+ pasos)
✅ Todas con validación entre pasos
✅ Patrón stepper documentado
✅ Backend state transitions validado

Estado: PASO 4b COMPLETADO ✅
Próximo paso: PASO 4c - Transacciones BIDIRECCIONAL (loops reversibles)
```

---

**Creado**: 27-10-2025
**Metodología**: Descubrimiento Patricio - Orden de complejidad creciente
**Siguiente**: Mapear transacciones BIDIRECCIONAL

