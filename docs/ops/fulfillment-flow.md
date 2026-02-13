# Fulfillment Flow (Operaciones de Proveedores)

## Objetivo
Estandarizar la coordinación entre la plataforma y proveedores para solicitudes de materiales, servicios técnicos y servicios profesionales creativos.

## Flujo operativo end-to-end

### 1) Notificación al proveedor
Cuando una solicitud pasa validaciones básicas (datos completos, ubicación y ventana de entrega), se envía una notificación automática al proveedor candidato.

**Canales recomendados (en orden):**
1. Push/In-app (si proveedor está activo en panel)
2. Email transaccional
3. WhatsApp/SMS (fallback para alta urgencia)

**Contenido mínimo de notificación:**
- ID de solicitud
- Categoría (materiales / técnicos / creativos)
- Resumen del requerimiento
- Ubicación y fecha límite
- CTA: `Aceptar`, `Declinar`, `Proponer alternativa`

### 2) Aceptación / rechazo
El proveedor responde desde el panel o enlace seguro.

**Aceptar**
- Cambia estado a `aceptada_proveedor`
- Reserva capacidad del proveedor
- Dispara mensaje de confirmación al cliente

**Rechazar**
- Solicita motivo estructurado (capacidad, zona, precio, agenda)
- Cambia estado a `rechazada_proveedor`
- Activa ruteo automático al siguiente proveedor elegible

### 3) Contraoferta
El proveedor puede responder con propuesta alternativa cuando no puede cumplir exactamente la solicitud inicial.

**Campos mínimos de contraoferta:**
- Precio propuesto
- Tiempo estimado de ejecución/entrega
- Ajustes de alcance (incluye / no incluye)
- Vigencia de la propuesta

**Reglas de negocio sugeridas:**
- Máximo 2 rondas de contraoferta por solicitud para evitar fricción
- Toda contraoferta expira automáticamente (ej. 24 h)
- Si cliente acepta, estado pasa a `confirmada`

### 4) Confirmación y cierre
**Confirmación**
- Se confirma proveedor asignado, precio final y ETA
- Se bloquea la edición de términos críticos (precio, fecha) salvo excepción operativa

**Cierre**
- Al completar la entrega/servicio: estado `cerrada_exitosa`
- Si no se concreta: `cerrada_no_exitosa` con causa
- Registrar métricas: tiempo de primera respuesta, tiempo de aceptación, tasa de rechazo, tasa de contraoferta aceptada

---

## SLA internos por categoría

> Objetivo: asegurar velocidad de respuesta y previsibilidad para cliente.

| Categoría | 1ra respuesta proveedor | Aceptación/Declinación | Confirmación final | Resolución objetivo |
|---|---:|---:|---:|---:|
| Materiales | ≤ 15 min | ≤ 30 min | ≤ 60 min | Mismo día (stock local) |
| Servicios técnicos | ≤ 30 min | ≤ 90 min | ≤ 4 h | 24-48 h |
| Profesionales creativos | ≤ 2 h | ≤ 8 h | ≤ 24 h | 2-5 días |

### Escalamiento operativo por incumplimiento
- **80% del SLA consumido sin respuesta:** enviar recordatorio automático.
- **100% SLA incumplido:** reasignar a siguiente proveedor y marcar incidente.
- **Reincidencia (3+ incumplimientos/30 días):** revisión de desempeño y ajuste de prioridad en ruteo.

---

## Plantillas de mensajes automáticos

## 1) Solicitud inicial
**Asunto:** Nueva solicitud disponible – {{categoria}} – #{{request_id}}

Hola {{proveedor_nombre}},

Tienes una nueva solicitud en tu zona:
- ID: {{request_id}}
- Categoría: {{categoria}}
- Resumen: {{resumen}}
- Ubicación: {{ubicacion}}
- Fecha límite: {{fecha_limite}}

Por favor responde antes de **{{sla_respuesta}}**:
- ✅ Aceptar
- ❌ Declinar
- 🔁 Proponer alternativa

Accede aquí: {{deep_link_panel}}

## 2) Recordatorio
**Asunto:** Recordatorio: solicitud pendiente #{{request_id}}

Hola {{proveedor_nombre}},

Aún no recibimos tu respuesta para la solicitud **#{{request_id}}**.
Quedan **{{tiempo_restante}}** para responder dentro del SLA.

Responder ahora: {{deep_link_panel}}

Si no puedes tomarla, por favor declínala para reasignación inmediata.

## 3) Propuesta alternativa (contraoferta)
**Asunto:** Nueva propuesta alternativa para solicitud #{{request_id}}

Hola {{cliente_nombre}},

El proveedor **{{proveedor_nombre}}** envió una propuesta alternativa:
- Precio: {{precio_propuesto}}
- Tiempo estimado: {{eta_propuesta}}
- Ajustes de alcance: {{ajustes_alcance}}
- Vigencia: {{vigencia}}

Opciones:
- ✅ Aceptar propuesta
- ❌ Rechazar y buscar otro proveedor

Ver detalle y responder: {{deep_link_cliente}}

---

## Estados recomendados para trazabilidad
`nueva` → `notificada` → (`aceptada_proveedor` | `rechazada_proveedor` | `contraoferta`) → `confirmada` → (`cerrada_exitosa` | `cerrada_no_exitosa`)

## KPIs mínimos
- Tiempo medio de primera respuesta (MTFR)
- Tasa de aceptación por categoría
- Tasa de rechazo por motivo
- % solicitudes con contraoferta
- Tasa de cierre exitoso
- Cumplimiento SLA por proveedor y categoría
