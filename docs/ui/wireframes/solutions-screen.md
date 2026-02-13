# Wireframe: Pantalla de soluciones recomendadas

## Objetivo
Guiar al usuario desde el resumen interpretado por IA hasta el envío de una solicitud con una opción clara de personalizar parámetros críticos (urgencia y presupuesto), minimizando fricción y maximizando conversión.

## Estructura general del layout

```text
┌──────────────────────────────────────────────────────────────┐
│ Header: "Soluciones para tu solicitud"                      │
├──────────────────────────────────────────────────────────────┤
│ 1) Resumen entendido por IA (editable)                       │
│    [textarea editable con texto generado]                    │
│    [Guardar cambios]                                         │
├──────────────────────────────────────────────────────────────┤
│ 2) Top 3 soluciones recomendadas                             │
│    ┌──────────────────────────────────────────────────────┐   │
│    │ Card #1 (destacada)                                 │   │
│    │ - Proveedor sugerido                                │   │
│    │ - Por qué encaja                                    │   │
│    │ - Coste estimado | Plazo | Distancia | Valoración   │   │
│    │ - Personalizar (urgencia / presupuesto)             │   │
│    └──────────────────────────────────────────────────────┘   │
│    ┌──────────────────────────────────────────────────────┐   │
│    │ Card #2                                              │   │
│    └──────────────────────────────────────────────────────┘   │
│    ┌──────────────────────────────────────────────────────┐   │
│    │ Card #3                                              │   │
│    └──────────────────────────────────────────────────────┘   │
├──────────────────────────────────────────────────────────────┤
│ 3) Paso de confirmación final                               │
│    - Datos de contacto                                      │
│    - Aceptación de condiciones                              │
│    - Método de pago (si aplica)                             │
├──────────────────────────────────────────────────────────────┤
│ CTA primario fijo (sticky footer): [Enviar solicitud]       │
└──────────────────────────────────────────────────────────────┘
```

## 1) Bloque: Resumen entendido por IA (editable)
- Campo de texto multilínea precargado con la interpretación de la necesidad del usuario.
- Acciones:
  - **Editar inline**.
  - **Guardar cambios** (opcionalmente autoguardado con indicador de estado).
- Validaciones sugeridas:
  - Longitud mínima para evitar solicitudes vacías.
  - Mensaje de ayuda para mantener claridad en materiales, cantidades y plazos.

## 2) Top 3 soluciones recomendadas
- Mostrar exactamente 3 cards priorizadas por score de recomendación.
- Cada card incluye de forma visible:
  - **Coste estimado**.
  - **Plazo estimado**.
  - **Distancia** (proveedor ↔ cliente o punto de entrega).
  - **Valoración** (rating y número de reseñas).
- Orden por defecto:
  1. Mejor equilibrio global (coste/plazo/calidad).
  2. Mejor coste.
  3. Mejor plazo.

## 3) Definición de card de solución
Cada card debe contener:

1. **Proveedor sugerido**
   - Nombre comercial.
   - Ubicación corta.
   - Rating resumido.

2. **Por qué encaja**
   - 2–3 bullets explicando match con la solicitud (capacidad, especialidad, tiempos, historial).

3. **Métricas de decisión**
   - Coste estimado.
   - Plazo.
   - Distancia.
   - Valoración.

4. **Personalización**
   - Control de **urgencia** (por ejemplo: estándar / urgente / muy urgente).
   - Control de **presupuesto** (slider o rango).
   - Recalcular estimaciones al ajustar parámetros.

## 4) CTA primario fijo
- Botón sticky siempre visible: **"Enviar solicitud"**.
- Comportamiento:
  - Deshabilitado hasta completar confirmación mínima.
  - Estados: normal, loading, éxito, error.
- En móvil: fixed bottom bar.
- En desktop: sticky lateral o footer persistente.

## 5) Paso de confirmación final
Sección previa al envío con checklist obligatoria:

1. **Datos de contacto**
   - Nombre.
   - Email.
   - Teléfono (opcional según mercado).

2. **Condiciones**
   - Checkbox de aceptación de términos y tratamiento de datos.

3. **Método de pago (si aplica)**
   - Selección de método (transferencia, tarjeta, contra factura, etc.).
   - Mostrar esta sección solo cuando el tipo de solicitud requiera prepago/reserva.

## Eventos de analítica (conversión)

### `solution_viewed`
- Cuándo: al mostrar la pantalla o al entrar en viewport una solución.
- Payload recomendado:
  - `request_id`
  - `solution_id`
  - `position` (1, 2, 3)
  - `estimated_cost`
  - `estimated_timeline`

### `solution_edited`
- Cuándo: cuando el usuario modifica el resumen IA o personaliza urgencia/presupuesto.
- Payload recomendado:
  - `request_id`
  - `field` (`ai_summary`, `urgency`, `budget`)
  - `previous_value`
  - `new_value`
  - `solution_id` (si aplica)

### `request_submitted`
- Cuándo: click en "Enviar solicitud" con envío exitoso.
- Payload recomendado:
  - `request_id`
  - `selected_solution_id`
  - `final_estimated_cost`
  - `final_timeline`
  - `payment_method` (si aplica)
  - `has_terms_accepted` (true/false)
