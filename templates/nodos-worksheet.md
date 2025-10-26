# 📝 WORKSHEET: Mapear URLs a Nodos

**Herramienta interactiva para adaptar OS DAK a tu aplicación**

---

## 🎯 Objetivo

Al completar este worksheet, tendrás:
- ✅ Inventario completo de URLs
- ✅ URLs agrupadas por rol/contexto
- ✅ NODOS vs SUB-NODOS identificados
- ✅ NÚMERO+LETRA asignados
- ✅ Base para crear tu Blockchain Visual Map

**Tiempo estimado:** 1-2 horas

---

## PASO 1: Listar URLs

### Instrucciones
1. Abre tu aplicación
2. Navega TODAS las secciones
3. Anota URL de cada página
4. Diferencia públicas vs autenticadas vs admin

### Template

```yaml
URLs_PUBLICAS (accesibles sin login):
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________

URLs_AUTENTICADAS (requieren login):
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________

URLs_ADMIN (solo admins):
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________
  [ ] /_______________

URLs_OTRAS (especiales, debug, etc.):
  [ ] /_______________
  [ ] /_______________
```

**Total URLs identificadas:** _____ URLs

---

## PASO 2: Asignar Roles

### Instrucciones
1. ¿Tienes roles en tu app? (Sí / No)
2. Si SÍ: Lista roles y qué URLs acceden
3. Si NO: Agrupa por tipo de acceso (público, autenticado, etc.)

### Template A: Tienes Roles

```yaml
ROL 1: _______________
  Descripción: _______________
  URLs accesibles:
    [ ] /_______________
    [ ] /_______________
    [ ] /_______________

ROL 2: _______________
  Descripción: _______________
  URLs accesibles:
    [ ] /_______________
    [ ] /_______________
    [ ] /_______________

ROL 3: _______________
  Descripción: _______________
  URLs accesibles:
    [ ] /_______________
    [ ] /_______________
    [ ] /_______________
```

### Template B: NO tienes Roles

```yaml
PÚBLICO (sin autenticación):
  [ ] /_______________
  [ ] /_______________

AUTENTICADO (con login):
  [ ] /_______________
  [ ] /_______________

PREMIUM/PRO (plan pagado):
  [ ] /_______________
  [ ] /_______________
```

---

## PASO 3: Definir CAPA 0

### Instrucciones
¿Cuál es el "guardian" de tu sistema?

### Opción A: Tienes Roles → CAPA 0 = Roles

```yaml
CAPA 0: Sistema de Roles

Mis roles (define letras):
  ___ = _______________ (descripción)
  ___ = _______________ (descripción)
  ___ = _______________ (descripción)
  ___ = _______________ (descripción)

Ejemplo:
  G = Guest (no autenticado)
  C = Cliente (autenticado)
  A = Admin (gestor)
```

### Opción B: NO tienes Roles → CAPA 0 = Guardian de Planes

```yaml
CAPA 0: Guardian de Planes

Función:
  - Validar plan antes de ejecutar
  - Usar TodoWrite proactivamente
  - Preguntar antes de proceder

URL: N/A (pre-ejecución)
```

**Mi CAPA 0 es:** _______________

---

## PASO 4: Identificar NODOS vs SUB-NODOS

### Instrucciones
Para cada URL, pregunta: **¿Tiene tabs/secciones internas?**

### Template

```yaml
URL 1: /_______________
  ¿Tiene tabs/secciones? (Sí / No)

  Si SÍ, listar SUB-NODOS:
    [ ] _______________ (tab/sección)
    [ ] _______________ (tab/sección)
    [ ] _______________ (tab/sección)

  Si NO:
    → Es NODO simple (sin SUB-NODOS)

---

URL 2: /_______________
  ¿Tiene tabs/secciones? (Sí / No)

  Si SÍ, listar SUB-NODOS:
    [ ] _______________ (tab/sección)
    [ ] _______________ (tab/sección)

  Si NO:
    → Es NODO simple (sin SUB-NODOS)

---

[Repetir para cada URL...]
```

**Ejemplo completo:**
```yaml
URL: /dashboard
  ¿Tiene tabs/secciones? Sí

  SUB-NODOS:
    [x] Resumen (tab overview)
    [x] Perfil (tab editar usuario)
    [x] Configuración (tab settings)
    [x] Notificaciones (tab alerts)
```

---

## PASO 5: Asignar NÚMERO (Profundidad Funcional)

### Instrucciones
Para cada URL, pregunta: **¿Qué FUNCIÓN cumple?**

### Guía de Profundidad

```yaml
NÚMERO 1: Puerta (inicio, selector)
  - Punto de entrada a funcionalidad
  - Usuario decide qué hacer
  - Ejemplos: /dashboard, /productos, /checkout

NÚMERO 2: Túnel (flujo guiado)
  - Proceso paso a paso
  - Usuario completa flujo
  - Ejemplos: /checkout/paso-1, /onboarding, /formulario

NÚMERO 3: Control (gestión, dashboard)
  - Centro de control con múltiples acciones
  - Usuario gestiona datos
  - Ejemplos: /admin/dashboard, /panel-control

NÚMERO 4: Proyección (analytics, futuro)
  - Análisis, tendencias, predicciones
  - Usuario visualiza resultados
  - Ejemplos: /analytics, /reportes, /predicciones
```

### Template

```yaml
URL 1: /_______________
  Función: _______________
  ¿Es puerta/túnel/control/proyección?: _______________
  NÚMERO asignado: ___

URL 2: /_______________
  Función: _______________
  ¿Es puerta/túnel/control/proyección?: _______________
  NÚMERO asignado: ___

[Repetir para cada URL...]
```

**Ejemplo:**
```yaml
URL: /checkout
  Función: Proceso de compra guiado en pasos
  ¿Es puerta/túnel/control/proyección?: Túnel
  NÚMERO asignado: 2
```

---

## PASO 6: Asignar LETRA (Contexto de Uso)

### Instrucciones
Define qué significa cada LETRA en TU app.

### Opción A: Por Tipo de Acción

```yaml
Mis letras (definir):
  A = _______________ (ej: Action - crear, modificar)
  B = _______________ (ej: Browse - leer, navegar)
  C = _______________ (ej: Control - admin, gestión)
  D = _______________ (ej: Data - reportes, analytics)
```

### Opción B: Por Audiencia

```yaml
Mis letras (definir):
  A = _______________ (ej: Admin)
  B = _______________ (ej: Business - vendedor)
  C = _______________ (ej: Customer - cliente)
  P = _______________ (ej: Public - visitante)
```

### Opción C: Por Funcionalidad

```yaml
Mis letras (definir):
  A = _______________ (ej: Authentication)
  B = _______________ (ej: Business logic)
  C = _______________ (ej: Content management)
  D = _______________ (ej: Data analytics)
```

**Mi sistema de letras:**
```yaml
A = _______________
B = _______________
C = _______________
D = _______________
```

### Template Asignación

```yaml
URL 1: /_______________
  NÚMERO: ___
  Contexto de uso: _______________
  LETRA asignada: ___

URL 2: /_______________
  NÚMERO: ___
  Contexto de uso: _______________
  LETRA asignada: ___

[Repetir para cada URL...]
```

**Ejemplo:**
```yaml
URL: /admin/productos/crear
  NÚMERO: 2 (túnel)
  Contexto de uso: Acción de crear producto
  LETRA asignada: A (Action)

→ Nodo: 2A
```

---

## PASO 7: Combinar TODO

### Instrucciones
Combina NÚMERO+LETRA+Descripción para cada URL.

### Template Final

```yaml
NODO 1:
  Código: ___[LETRA][sufijo]
  URL: /_______________
  Descripción: _______________
  Rol: _______________

  SUB-NODOS (si aplica):
    - ___.___: _______________
    - ___.___: _______________

---

NODO 2:
  Código: ___[LETRA][sufijo]
  URL: /_______________
  Descripción: _______________
  Rol: _______________

  SUB-NODOS (si aplica):
    - ___.___: _______________
    - ___.___: _______________

---

[Repetir para todos los nodos...]
```

**Ejemplo completo:**
```yaml
NODO 1:
  Código: 1A
  URL: /admin/dashboard
  Descripción: Dashboard principal admin con widgets
  Rol: Admin

  SUB-NODOS:
    - 1A.Resumen: Widget de resumen ejecutivo
    - 1A.Urgencias: Widget de alertas urgentes
    - 1A.Métricas: Widget de KPIs principales

---

NODO 2:
  Código: 2A
  URL: /admin/productos/crear
  Descripción: Formulario crear producto nuevo
  Rol: Admin

  SUB-NODOS: N/A (formulario simple)
```

---

## PASO 8: Crear Diagrama ASCII Inicial

### Instrucciones
Usa tus nodos para crear diagrama visual.

### Template

```
OS [TU APP] (Sistema Operativo de Desarrollo con IA)
Analogía: Blockchain Viviente

┌─────────────────────────────────────────────────────────────────┐
│  NÚMERO = PROFUNDIDAD (tu definición)                           │
│  LETRA  = CONTEXTO (tu definición)                              │
│  CAPA   = URL física única                                      │
└─────────────────────────────────────────────────────────────────┘

CAPA 0 - [TU DEFINICIÓN]
├── [Descripción]
└── [Función]

CAPA 1 - [FUNCIÓN NIVEL 1]
├── 1A: /_______________
│   └── [Descripción]
└── 1B: /_______________
    └── [Descripción]

CAPA 2 - [FUNCIÓN NIVEL 2]
├── 2A: /_______________
│   └── [Descripción]
└── 2B: /_______________
    └── [Descripción]

[... continuar con todos tus nodos ...]

---

ESTADÍSTICAS

Total Nodos: ___
Total SUB-NODOS: ___
Roles: ___
```

---

## PASO 9: Priorizar Skills a Crear

### Instrucciones
No documentes TODO de una vez. Prioriza.

### Template

```yaml
PRIORIDAD ALTA (crear primero):
  [ ] Blockchain Visual Map (obligatorio)
  [ ] Skill nodo más importante: /_______________
  [ ] Skill journey principal: [usuario hace X]

PRIORIDAD MEDIA (crear después):
  [ ] Skill nodo crítico 2: /_______________
  [ ] Skill nodo crítico 3: /_______________
  [ ] Skill flujo importante: [A → B]

PRIORIDAD BAJA (crear cuando necesario):
  [ ] Skill nodo secundario 1: /_______________
  [ ] Skill nodo secundario 2: /_______________
```

**Orden recomendado:**
1. Blockchain Visual Map (siempre primero)
2. Journey principal (happy path usuario)
3. 2-3 nodos críticos (los más usados)
4. 1 flujo importante (conexión entre nodos)
5. Resto según necesidad

---

## ✅ CHECKLIST FINAL

```yaml
PASO 1: Listar URLs
  [ ] URLs públicas identificadas
  [ ] URLs autenticadas identificadas
  [ ] URLs admin identificadas
  [ ] Total contado: ___ URLs

PASO 2: Asignar Roles
  [ ] Roles definidos (o alternativa sin roles)
  [ ] URLs agrupadas por rol

PASO 3: Definir CAPA 0
  [ ] CAPA 0 definida (Roles o Guardian)
  [ ] Letras asignadas (si aplica)

PASO 4: NODOS vs SUB-NODOS
  [ ] Diferenciados claramente
  [ ] Tabs/secciones listados como SUB-NODOS

PASO 5: Asignar NÚMERO
  [ ] Profundidad funcional definida
  [ ] NÚMERO asignado a cada URL

PASO 6: Asignar LETRA
  [ ] Sistema de letras definido
  [ ] LETRA asignada a cada URL

PASO 7: Combinar
  [ ] NÚMERO+LETRA+Descripción completo
  [ ] Formato: [NUM][LETRA][sufijo]: /url - descripción

PASO 8: Diagrama ASCII
  [ ] Diagrama inicial creado
  [ ] Estadísticas calculadas

PASO 9: Priorizar Skills
  [ ] Skills prioritarias identificadas
  [ ] Orden de creación definido
```

---

## 🎓 TIPS

**1. Empieza pequeño**
- No mapees 50 URLs de una vez
- Empieza con 10-15 más importantes
- Expande después

**2. Valida con código**
- Revisa middleware de autenticación
- Confirma roles en database
- Verifica routing real

**3. Itera**
- Primera versión NO será perfecta
- Ajusta después de usar 1 semana
- Sistema evoluciona con uso

**4. Pregunta "¿Por qué?"**
- No copies template ciegamente
- Adapta a TU contexto
- Cada decisión debe tener razón

**5. Documenta decisiones**
- Anota por qué elegiste X nomenclatura
- Futuros tú agradecerán contexto
- Mantén DECISIONS-LOG.md

---

## 📚 PRÓXIMOS PASOS

Una vez completes este worksheet:

1. **Crear .claude/skills/blockchain-viviente-visual-map/SKILL.md**
   - Copia tu diagrama ASCII
   - Agrega estadísticas
   - Documenta transacciones

2. **Crear primera skill de nodo**
   - Usa `templates/skill-template.md`
   - Documenta nodo más importante
   - Valida que funciona

3. **Configurar persistencia**
   - Git add .claude/
   - Commit inicial
   - Test recovery

4. **Testing**
   - Boot time <2 segundos
   - Load on demand funciona
   - Comunicación sistémica validada

---

**Creado:** 2025-10-26
**Autor:** Patricio Díaz + Claude
**Para:** Developers adaptando OS DAK
**Tiempo estimado:** 1-2 horas

🌌 **"Mapeo sistemático = arquitectura visualizable"**
