# 🔄 Blockchain Viviente - PASO 4c: Transacciones BIDIRECCIONAL

**App**: Soluciones Díaz CRM (Construcción)
**Fecha**: 27-10-2025
**Versión**: 1.0 - PASO 4c (Loops reversibles)

---

## 🎯 Qué es Transacción BIDIRECCIONAL

**Tipo 8 - BIDIRECCIONAL**: A ⇄ B (loop reversible, ir y venir múltiples veces)

```yaml
Características:
  - Usuario puede ir A → B
  - Usuario puede volver B → A
  - Puede repetir ciclo múltiples veces
  - Parte del workflow iterativo
  - NO es accidental, es diseñado

Diferencia vs FLUJO (Tipo 2):
  - FLUJO: A → B (puede volver con botón browser "atrás")
  - BIDIRECCIONAL: A ⇄ B (botones explícitos "Editar" / "Preview")

Diferencia vs UNIDIRECCIONAL (Tipo 7):
  - UNIDIRECCIONAL: A → B (NO vuelve)
  - BIDIRECCIONAL: A ⇄ B (vuelve diseñado)
```

---

## 📋 Transacciones BIDIRECCIONAL Detectadas

### T8.1: Formulario ⇄ Preview (Quote Detallada)

```yaml
Tipo: 8 - BIDIRECCIONAL
Nodos: 2A2.CLIENT (formulario) ⇄ Preview interno

Flujo iterativo:
  1. Cliente llena formulario detallado
  2. Click "Previsualizar"
  3. Ve preview con formato final
  4. Click "Editar"
  5. Vuelve a formulario (datos preservados)
  6. Hace cambios
  7. Click "Previsualizar" nuevamente
  8. Repite hasta satisfecho
  9. Click "Enviar" → UNIDIRECCIONAL a confirmación

Loop diseñado:
  - Permite ajustar antes de enviar
  - Datos se preservan en cada vuelta
  - Preview muestra exactamente cómo se verá
  - Puede hacer 10+ iteraciones

Código:
  - State: const [isPreview, setIsPreview] = useState(false);
  - Toggle: setIsPreview(!isPreview)
  - Datos: formData persiste en memoria
```

---

### T8.2: Dashboard Tabs ⇄ (Cliente)

```yaml
Tipo: 8 - BIDIRECCIONAL
Nodos: 3B.CLIENT (tabs internos)

Flujo tabs:
  - ?tab=perfil ⇄ ?tab=cotizaciones
  - ?tab=cotizaciones ⇄ ?tab=mensajes
  - ?tab=mensajes ⇄ ?tab=notificaciones
  - etc.

Loop continuo:
  - Cliente navega entre tabs libremente
  - Puede volver a tab anterior cuantas veces quiera
  - URL cambia pero componente principal NO unmount
  - Estado preservado por tab

Código:
  - Router: router.push('?tab=perfil', { shallow: true })
  - State: const [activeTab, setActiveTab] = useState('perfil');
  - Preserve: useEffect con cleanup solo en unmount total
```

---

### T8.3: Admin CRUD ⇄ Modal Edit (Servicios/Materiales)

```yaml
Tipo: 8 - BIDIRECCIONAL
Nodos: 3B.ADMIN.DESKTOP.1 (lista) ⇄ Modal editar

Flujo CRUD:
  1. Admin en /admin/servicios (lista)
  2. Click "Editar" servicio X
  3. Modal abre con form
  4. Admin hace cambios
  5. Options:
     a) Click "Guardar" → actualiza + cierra modal → vuelve a lista
     b) Click "Cancelar" → descarta + cierra modal → vuelve a lista
     c) Click fuera modal → cierra → vuelve a lista
  6. Puede editar otro servicio
  7. Repite ciclo

Loop CRUD:
  - Típico pattern lista ⇄ editar
  - No cambia URL
  - Modal overlay sobre lista
  - Al cerrar, vuelve a lista con scroll preservado

Código:
  - State: const [editingId, setEditingId] = useState<string | null>(null);
  - Modal: {editingId && <EditModal onClose={() => setEditingId(null)} />}
  - Scroll: Preserve scroll position con ref
```

---

### T8.4: Galería ⇄ Imagen Detalle

```yaml
Tipo: 8 - BIDIRECCIONAL
Nodos: 1B.GUEST.3 (galería grid) ⇄ Modal imagen

Flujo galería:
  1. Usuario en /galeria (grid de fotos)
  2. Click en foto X
  3. Modal fullscreen con imagen HD
  4. Swipe left/right para navegar entre fotos
  5. Click "X" o ESC para cerrar
  6. Vuelve a grid
  7. Puede abrir otra foto
  8. Repite

Loop diseñado:
  - Exploración iterativa
  - No requiere volver con botón browser
  - Diseñado para explorar múltiples imágenes

Código:
  - State: const [selectedImageId, setSelectedImageId] = useState<string | null>(null);
  - Modal: Lightbox component
  - Keyboard: ESC key listener → close modal
```

---

### T8.5: Admin Dashboard ⇄ Secciones (Desktop)

```yaml
Tipo: 8 - BIDIRECCIONAL
Nodos: 3A.ADMIN.DESKTOP ⇄ 3B.ADMIN.DESKTOP.*

Flujo navegación:
  1. Admin en /admin (dashboard principal)
  2. Click "Servicios"
  3. Va a /admin/servicios
  4. Click "Volver al Dashboard" o logo
  5. Vuelve a /admin
  6. Click "Materiales"
  7. Va a /admin/materiales
  8. Vuelve a /admin
  9. Repite según necesidad

Loop workflow:
  - Dashboard = hub central
  - Secciones = tareas específicas
  - Siempre puede volver a hub
  - Diseñado para multitasking

Código:
  - router.push('/admin/servicios')
  - router.back() o router.push('/admin')
  - Breadcrumbs: Dashboard > Servicios
```

---

## 📊 Resumen BIDIRECCIONAL

```yaml
Total transacciones tipo 8: 5 principales

Por frecuencia de loop:
  - Alta: T8.2 (tabs - múltiples veces/sesión)
  - Alta: T8.3 (CRUD editar - varias veces/día)
  - Alta: T8.5 (admin navegación - continuo)
  - Media: T8.1 (preview - 2-3 veces/quote)
  - Baja: T8.4 (galería - exploración ocasional)

Criticidad:
  - ALTA: T8.5 (workflow admin)
  - MEDIA: T8.2 (UX cliente)
  - MEDIA: T8.3 (gestión)
  - MEDIA: T8.1 (calidad quotes)
  - BAJA: T8.4 (exploración)
```

---

## 🎯 Características Comunes

```yaml
1. Botones explícitos de navegación:
   ✅ "Editar" / "Preview"
   ✅ "Volver" / "Cerrar"
   ✅ NO depende de botón browser back

2. Estado preservado:
   ✅ Datos NO se pierden al volver
   ✅ Scroll position preservado
   ✅ Form data en memoria

3. Loop iterativo diseñado:
   ✅ Parte del workflow esperado
   ✅ Usuario puede hacer N iteraciones
   ✅ No hay límite de veces

4. Visual feedback:
   ✅ Breadcrumbs muestran dónde estás
   ✅ Active state en tabs
   ✅ Modal overlay (no cambia contexto completo)
```

---

## ⚠️ Patrón de Implementación

```typescript
// Pattern: Modal con estado bidireccional
const ListWithEditModal = () => {
  const [items, setItems] = useState([]);
  const [editingItem, setEditingItem] = useState<Item | null>(null);
  const [isModalOpen, setIsModalOpen] = useState(false);

  const handleEdit = (item: Item) => {
    setEditingItem(item);
    setIsModalOpen(true);
  };

  const handleSave = async (updatedItem: Item) => {
    await updateItem(updatedItem);
    setItems(prev => prev.map(i => i.id === updatedItem.id ? updatedItem : i));
    handleClose();
  };

  const handleClose = () => {
    setIsModalOpen(false);
    setEditingItem(null); // Reset después de cierre
  };

  return (
    <>
      {/* Lista */}
      <div className="list">
        {items.map(item => (
          <div key={item.id}>
            {item.name}
            <button onClick={() => handleEdit(item)}>Editar</button>
          </div>
        ))}
      </div>

      {/* Modal editar - BIDIRECCIONAL */}
      {isModalOpen && editingItem && (
        <Modal onClose={handleClose}>
          <EditForm
            initialData={editingItem}
            onSave={handleSave}
            onCancel={handleClose}
          />
        </Modal>
      )}
    </>
  );
};
```

---

## ✅ Validación PASO 4c

```yaml
Criterios validados:

✅ 5 transacciones BIDIRECCIONAL identificadas
✅ Todas con botones explícitos ida/vuelta
✅ Todas con estado preservado
✅ Pattern modal/tabs documentado
✅ Loop iterativo diseñado

Estado: PASO 4c COMPLETADO ✅
Próximo paso: PASO 4d - Transacciones WiFi (tiempo real, más complejo)
```

---

**Creado**: 27-10-2025
**Metodología**: Descubrimiento Patricio - Orden de complejidad creciente
**Siguiente**: Mapear transacciones WiFi (sincronización tiempo real)

