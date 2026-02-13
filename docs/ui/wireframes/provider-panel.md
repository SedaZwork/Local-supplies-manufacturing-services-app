# Provider Panel (MVP)

## Objetivo
Permitir que un proveedor gestione solicitudes entrantes con la mínima fricción operativa: revisar, aceptar/declinar y estimar precio/tiempo rápidamente.

## Módulos mínimos

### 1) Bandeja de solicitudes
Lista priorizada por urgencia y proximidad al vencimiento SLA.

**Columnas sugeridas:**
- ID solicitud
- Categoría
- Cliente/Proyecto (resumen)
- Ubicación
- Fecha límite
- Estado
- Tiempo restante SLA

**Filtros básicos:**
- Categoría
- Estado (nueva, en revisión, contraoferta)
- Urgente (sí/no)

### 2) Botón Aceptar / Declinar
En la vista detalle y en acciones rápidas de la bandeja.

**Aceptar**
- Confirmación de capacidad
- Cambio de estado inmediato

**Declinar**
- Motivo obligatorio:
  - Sin capacidad
  - Fuera de zona
  - Precio no viable
  - Agenda incompatible

### 3) Estimación rápida de precio/tiempo
Formulario compacto en el detalle de solicitud.

**Inputs mínimos:**
- Precio estimado
- Tiempo estimado (horas/días)
- Notas opcionales (supuestos)

**Acciones:**
- `Enviar aceptación` (si coincide con solicitud)
- `Enviar propuesta alternativa` (contraoferta)

---

## Wireframe textual (MVP)

```text
┌────────────────────────────────────────────────────────────────────┐
│ Provider Panel                                                    │
├────────────────────────────────────────────────────────────────────┤
│ Filtros: [Categoría v] [Estado v] [Urgente □] [Buscar........]   │
├────────────────────────────────────────────────────────────────────┤
│ Bandeja de solicitudes                                            │
│ ┌──────┬──────────────┬───────────┬─────────┬─────────┬────────┐ │
│ │ ID   │ Categoría    │ Resumen   │ Límite  │ SLA     │ Acción │ │
│ ├──────┼──────────────┼───────────┼─────────┼─────────┼────────┤ │
│ │ 1021 │ Materiales   │ Acero x20 │ 18:00   │ 12 min  │ Ver    │ │
│ │ 1022 │ Técnico      │ CNC onsite│ 20:00   │ 28 min  │ Ver    │ │
│ └──────┴──────────────┴───────────┴─────────┴─────────┴────────┘ │
├────────────────────────────────────────────────────────────────────┤
│ Detalle solicitud #1021                                           │
│ [Resumen] [Ubicación] [Adjuntos]                                  │
│ Precio estimado: [__________]   Tiempo estimado: [_____]          │
│ Notas: [_______________________________________________]           │
│                                                                    │
│ [✅ Aceptar]   [❌ Declinar]   [🔁 Propuesta alternativa]          │
└────────────────────────────────────────────────────────────────────┘
```

## Criterios de aceptación UX (MVP)
- Un proveedor puede responder una solicitud en ≤ 3 clics desde la bandeja.
- El tiempo restante de SLA es visible en lista y detalle.
- Declinar requiere motivo para análisis operacional.
- Contraoferta exige precio y tiempo estimado para trazabilidad.
