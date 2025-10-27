# 🤖 Blockchain Viviente - Patrón de Agentes

**App**: Soluciones Díaz CRM (Construcción)
**Fecha**: 27-10-2025
**Versión**: 1.0 - PASO 3 (Agentes especializados por nodo)

---

## 🎯 Qué es un Agente de Nodo

**Agente = Especialista experto de UN nodo específico**

```yaml
Función:
  - Conoce TODO sobre su nodo asignado
  - Sabe qué campos tiene
  - Sabe qué validaciones aplica
  - Sabe qué errores puede tener
  - Sabe dónde está el código exacto
  - Sabe cómo se conecta con otros nodos

NO es:
  - Un trabajador externo (MCP tool)
  - Temporal
  - Genérico

Es:
  - Parte PERMANENTE de la arquitectura
  - Específico de un nodo
  - Documentado en .claude/.agents/
```

---

## 📋 Estructura de Un Agente

```yaml
# Agente: [Nombre Nodo]

## 🎯 Identificación

Nodo: [NÚMERO+LETRA+CAPA]
URL: [/ruta]
Rol: [GUEST | CLIENT | ADMIN | SUPER_ADMIN]
Dispositivo: [DESKTOP | MOBILE | AMBOS]
Tipo: [Puerta | Túnel | Dashboard | Display]

## 📝 Descripción

[Descripción clara de qué hace este nodo]

## 🔧 Componentes

Archivo principal: [ruta al archivo]
Componentes relacionados:
  - [componente 1]
  - [componente 2]

## 📊 Schema de Datos

Firestore collection: [nombre]
Schema:
  ```typescript
  interface [Interface] {
    campo1: tipo;
    campo2: tipo;
  }
  ```

## ✅ Validaciones

Frontend:
  - [validación 1]
  - [validación 2]

Backend:
  - [validación 1]
  - [validación 2]

## 🔗 Transacciones

Entrada desde:
  - [nodo A] (tipo: FLUJO)
  - [nodo B] (tipo: BIDIRECCIONAL)

Salida hacia:
  - [nodo C] (tipo: UNIDIRECCIONAL)
  - [nodo D] (tipo: CADENA)

## 🐛 Errores Comunes

1. [Error 1]
   - Causa: [...]
   - Solución: [...]

2. [Error 2]
   - Causa: [...]
   - Solución: [...]

## 🎯 Guardian

Permisos:
  - Rol mínimo: [CLIENT]
  - Validación especial: [si aplica]

## 💡 Notas

[Cualquier información adicional importante]
```

---

## 🏆 Top 10 Nodos Críticos (Agentes a crear)

```yaml
Prioridad 1 - CRÍTICOS (crear primero):

1. 2A1.CLIENT - Cotización Rápida
   Impacto: CRÍTICO - Conversión principal
   Frecuencia: Alta

2. 3B.ADMIN.MOBILE.1 - Admin Cotizaciones Mobile
   Impacto: CRÍTICO - Gestión diaria
   Frecuencia: Muy alta

3. 3A.ADMIN.DESKTOP - Admin Dashboard
   Impacto: CRÍTICO - Control negocio
   Frecuencia: Alta

4. 1A.GUEST - Homepage
   Impacto: CRÍTICO - Primera impresión
   Frecuencia: Muy alta

5. 3B.CLIENT - Mi Dashboard
   Impacto: ALTO - Hub cliente
   Frecuencia: Alta

Prioridad 2 - IMPORTANTES:

6. 2A2.CLIENT - Cotización Detallada
7. 1A.CLIENT - Selector Cotización
8. 1A.GUEST.2 - Auth Signin
9. 3B.ADMIN.DESKTOP.1 - CRUD Servicios
10. 3B.ADMIN.DESKTOP.2 - CRUD Materiales
```

---

## 📁 Ubicación de Agentes

```yaml
Estructura de carpetas:

.claude/.agents/
├── blockchain-viviente/
│   ├── guest/
│   │   ├── 1A-homepage.md
│   │   ├── 1A-auth-signin.md
│   │   └── ...
│   ├── client/
│   │   ├── 1A-selector-cotizacion.md
│   │   ├── 2A1-cotizacion-rapida.md
│   │   ├── 2A2-cotizacion-detallada.md
│   │   ├── 3B-mi-dashboard.md
│   │   └── ...
│   ├── admin/
│   │   ├── desktop/
│   │   │   ├── 3A-dashboard.md
│   │   │   ├── 3B-servicios.md
│   │   │   ├── 3B-materiales.md
│   │   │   └── ...
│   │   └── mobile/
│   │       ├── 3B-cotizaciones.md
│   │       └── 3B-clientes.md
│   └── super-admin/
│       ├── 4A-dev-tools.md
│       └── ...
└── .agent-registry.json (índice de todos los agentes)
```

---

## 🎯 Agente Registry (Índice)

Archivo: `.claude/.agents/.agent-registry.json`

```json
{
  "version": "1.0",
  "blockchain_viviente": true,
  "total_agents": 73,
  "agents": [
    {
      "id": "1A.GUEST",
      "name": "Homepage Agent",
      "nodo": "1A.GUEST",
      "url": "/",
      "rol": "GUEST",
      "file": ".claude/.agents/blockchain-viviente/guest/1A-homepage.md",
      "priority": "CRITICAL",
      "status": "active"
    },
    {
      "id": "2A1.CLIENT",
      "name": "Cotización Rápida Agent",
      "nodo": "2A1.CLIENT",
      "url": "/cotizacion/rapida",
      "rol": "CLIENT",
      "file": ".claude/.agents/blockchain-viviente/client/2A1-cotizacion-rapida.md",
      "priority": "CRITICAL",
      "status": "active"
    }
    // ... resto de agentes
  ],
  "meta_agents": [
    {
      "id": "cotizaciones-flow",
      "name": "Cotizaciones Flow Meta-Agent",
      "coordinates": ["1A.CLIENT", "2A1.CLIENT", "2A2.CLIENT"],
      "file": ".claude/.agents/meta-agents/cotizaciones-flow.md"
    }
  ]
}
```

---

## 🔄 Meta-Agentes (Coordinadores)

**Meta-Agente = Coordinador de múltiples agentes en un flujo**

```yaml
Ejemplo: Meta-Agente "Cotizaciones Flow"

Coordina:
  - Agente 1A.CLIENT (selector)
  - Agente 2A1.CLIENT (formulario rápido)
  - Agente 2A2.CLIENT (formulario detallado)
  - Agente 3B.ADMIN.MOBILE.1 (admin recibe)

Conoce:
  - Flujo completo de cotizaciones
  - Transacción Cliente → Admin (ASIMÉTRICA)
  - Transacción Notificaciones (SYSTEM)

Cuándo activar:
  - Usuario menciona "problema en cotizaciones"
  - Abarca múltiples nodos
  - Necesita visión panorámica del flujo
```

---

## ✅ Checklist Creación Agente

```yaml
Para cada nodo crítico:

□ Identificar nodo (NÚMERO+LETRA+CAPA)
□ Encontrar archivo(s) de código
□ Documentar componentes principales
□ Extraer schema Firestore (si aplica)
□ Listar validaciones
□ Mapear transacciones entrada/salida
□ Documentar errores comunes
□ Especificar permisos Guardian
□ Crear archivo .md en carpeta correspondiente
□ Agregar a .agent-registry.json
□ Validar que agente responde consultas sobre su nodo
```

---

## 🎯 Ejemplo: Agente 2A1.CLIENT (Preview)

Voy a crear el agente para el nodo más crítico como ejemplo...

---

**Creado**: 27-10-2025
**Total agentes a crear**: 73 (empezando por top 10)
**Siguiente**: Crear agentes para nodos críticos

