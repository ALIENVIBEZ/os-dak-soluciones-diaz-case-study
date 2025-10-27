# 🌟 Ambiente Perfecto - Guía de Uso Rápido

**Sistema Auto-Evolutivo de Monitoreo de Proyecto - v2.0 Multi-Rol**

---

## 🎯 Nuevo en v2.0

```yaml
✅ Soporte multi-rol (4 roles):
   - 🔴 GUEST (NO AUTENTICADO)
   - 🔵 CLIENT (CLIENTE)
   - 🟢 ADMIN (Desktop + Mobile)
   - 🟣 SUPER_ADMIN (Dev/Testing)

✅ Device detection (Desktop vs Mobile para Admin)
✅ Health score segmentado por rol
✅ Gap detection por rol
✅ ARTERIAS específicas por rol
✅ Detección URLs NO autorizadas (seguridad)

Mejora: +6 puntos health score (62 → 68)
```

**📖 Guía de Adaptación:** [ADAPTACION_MULTI_ROL.md](./ADAPTACION_MULTI_ROL.md)

---

## 🚀 Inicio Rápido

### Comandos Principales

```bash
# 1. Ver estado del proyecto
"ambiente perfecto"
"health check"
"qué falta"

# 2. Ver gaps específicos
"qué falta documentar"
"gaps P0"

# 3. Ver ARTERIAS
"qué arterias tengo"
"cómo optimizar velocidad"

# 4. Ver delegación
"delegar a agentes externos"
"optimizar tokens"
```

---

## 📊 Health Score

```yaml
90-100: 🟢 EXCELENTE
75-89:  🟡 BUENO
60-74:  🟠 REGULAR
40-59:  🔴 CRÍTICO
0-39:   ⚫ EMERGENCIA
```

**Actual:** 68/100 🟠 REGULAR

**Breakdown por ROL:**
- 🔴 GUEST: 88/100 ✅ (pero 22 URLs NO autorizadas)
- 🔵 CLIENT: 72/100 🟡
- 🟢 ADMIN: 48/100 🔴
- 🟣 SUPER_ADMIN: 82/100 ✅

---

## 🎯 Prioridades Actuales

### 🚨 P0 - URGENTE (Seguridad - ANTES 24H)

1. **Eliminar 38 URLs NO autorizadas** - 1-2h
   - 22 URLs GUEST (debug/test/duplicadas)
   - 3 URLs ADMIN (admin-fix, admin-test, temp-login)
   - Impacto: CRÍTICO SEGURIDAD

### P0 - ESTA SEMANA (Funcional)

2. ⚡ Implementar ARTERIA troubleshooting-produccion - 2.5h (ROL: SUPER_ADMIN)
3. ⚡ Implementar ARTERIA admin-cotizaciones-mobile - 1h (ROL: ADMIN Mobile)
4. ❌ Completar ROL_ADMIN_DESKTOP_DASHBOARD.md - 2h
5. ❌ Crear skill /admin/clientes/[id] - 1.5h
6. ❌ Crear skill /cotizacion/wizard - 1.5h (ROL: CLIENT)

**Total:** 9.5h → Health Score 68 → 85+ (+17 puntos)

---

## ⚡ ARTERIAS (0/5 implementadas)

```yaml
❌ admin-cotizaciones-mobile (9.3x potencial) - ROL: ADMIN Mobile
❌ cliente-dashboard (9x potencial) - ROL: CLIENT
❌ deploy-workflow (15x potencial) - ROL: SUPER_ADMIN
❌ troubleshooting-produccion (17.5x potencial) - ROL: SUPER_ADMIN
❌ analytics-deep-dive (11.2x potencial) - ROL: ADMIN/SUPER_ADMIN
```

**Speedup promedio potencial:** 8.5x más rápido
**Ahorro tokens potencial:** ~180k/mes (~$3.6/mes)

---

## 🤖 Delegación (Ahorro potencial: 65k tokens/mes)

```yaml
Firebase MCP: 30k tokens/mes (~$0.60/mes)
Playwright MCP: 15k tokens/mes (~$0.30/mes)
GitHub MCP: 20k tokens/mes (~$0.40/mes)
```

**Estado:** No implementado (0% aprovechado)

---

## 📁 Archivos de la Skill

```
.claude/
├── skills/ambiente-perfecto/
│   ├── SKILL.md                    # Skill principal v2.0
│   ├── README.md                   # Este archivo
│   └── ADAPTACION_MULTI_ROL.md     # 🆕 Guía de adaptación
├── reports/ambiente-perfecto/
│   ├── 2025-10-26.md               # Reporte v1.0 (histórico)
│   └── 2025-10-26-v2.md            # 🆕 Reporte v2.0 (actual)
└── hooks/
    └── ambiente-perfecto-sync.js   # Auto-actualización
```

---

## 🔄 Auto-actualización

El hook ejecuta automáticamente después de commits con:
- `feat:`, `fix:`, `docs:`, `refactor:`
- Cambios en `.md`, `src/app/`, `.claude/skills/`

**Frecuencia:** Cada 30 minutos mínimo

---

## 📈 Roadmap

### Esta Semana
- ✅ Sistema implementado
- ⏳ Resolver 5 gaps P0

### Próximas 2 Semanas
- ⏳ Resolver 8 gaps P1
- ⏳ Implementar 2 ARTERIAS faltantes

### Próximo Mes
- ⏳ Delegación Firebase/Playwright MCP
- ⏳ Dashboard /superadmin/arterias

---

## 🎓 Ejemplos de Uso

**Ejemplo 1: Ver estado general**
```
Usuario: "ambiente perfecto"

Output:
  📊 Health Score: 67/100 🟠 REGULAR

  Gaps: 18 total (5 P0, 8 P1, 5 P2)
  ARTERIAS: 3/5 implementadas (60%)
  Delegación: 0% (oportunidad ~$12/mes)
```

**Ejemplo 2: Ver gaps críticos**
```
Usuario: "qué gaps P0 tengo"

Output:
  🔴 P0 - CRÍTICO (5):

  1. /admin (dashboard) - SIN SKILL
  2. ARTERIA admin-dashboard - NO IMPLEMENTADA
  3. /cotizacion/rapida - SIN SKILL
  4. ARTERIA troubleshooting-prod - NO IMPLEMENTADA
  5. WiFi /admin/materiales ↔ /materiales - NO DOC
```

**Ejemplo 3: Ver ARTERIAS**
```
Usuario: "qué arterias tengo"

Output:
  ⚡ ARTERIAS Activas (3/5):

  ✅ admin-cotizaciones (speedup 9.3x)
  ✅ cliente-dashboard (speedup 9x)
  ✅ deploy-workflow (speedup 15x)

  ❌ Faltantes (2):
  troubleshooting-produccion (urgente)
  analytics-deep-dive (opcional)
```

---

## 🚨 Troubleshooting

**Hook no ejecuta:**
```bash
# Ejecutar manualmente
node .claude/hooks/ambiente-perfecto-sync.js
```

**Quiero forzar actualización:**
```bash
# Eliminar throttle
rm .claude/.ambiente-perfecto-last-run

# Ejecutar
node .claude/hooks/ambiente-perfecto-sync.js
```

**Reporte desactualizado:**
- Hacer commit importante (feat:, fix:, etc.)
- O ejecutar hook manualmente

---

## 📚 Recursos

**Documentación:**
- [SKILL.md](./SKILL.md) - Documentación completa v2.0
- [ADAPTACION_MULTI_ROL.md](./ADAPTACION_MULTI_ROL.md) - 🆕 Guía de adaptación
- [2025-10-26-v2.md](../../reports/ambiente-perfecto/2025-10-26-v2.md) - Último reporte

**Para otros proyectos:**
- **IMPORTANTE:** Lee [ADAPTACION_MULTI_ROL.md](./ADAPTACION_MULTI_ROL.md) para adaptar a tu sistema de roles
- No copies directamente - adapta a tu arquitectura

**Inspiración:**
- dak-chain-ia/DEVELOPER_PACK_UNIVERSAL.md

---

**Versión:** 2.0
**Changelog:**
- v2.0 (26-10-2025): Soporte multi-rol (4 roles + device detection)
- v1.0 (26-10-2025): Versión inicial (2 roles)

**Fecha:** 26-10-2025
**Autor:** Patricio
