# API Contracts Mínimos

## Convenciones
- Formato: `application/json`.
- Autenticación: bearer token (pendiente de definición de proveedor IAM).
- `id`: UUID v4 en entidades principales.
- Timestamps en ISO-8601 UTC.

## 1) POST /requests/intake
Crea una solicitud inicial capturada desde UI.

### Request
```json
{
  "customer_id": "uuid",
  "title": "Fabricación de soporte metálico",
  "description": "Necesito 200 piezas en acero inoxidable AISI 304",
  "attachments": [
    {
      "name": "plano.pdf",
      "url": "https://..."
    }
  ],
  "location": {
    "country": "MX",
    "city": "Monterrey"
  },
  "priority": "normal"
}
```

### Response (201)
```json
{
  "request_id": "uuid",
  "status": "capturada",
  "created_at": "2026-01-10T12:00:00Z"
}
```

## 2) POST /requests/{id}/analyze
Ejecuta el pipeline del `ai-orchestrator` para estructurar la solicitud.

### Request
```json
{
  "force_reanalyze": false
}
```

### Response (202)
```json
{
  "request_id": "uuid",
  "status": "analizada",
  "analysis": {
    "category": "mecanizado",
    "materials": ["acero inoxidable"],
    "quantity": 200,
    "constraints": ["entrega <= 15 días"]
  }
}
```

## 3) GET /requests/{id}/solutions
Retorna soluciones candidatas generadas por matching + IA.

### Response (200)
```json
{
  "request_id": "uuid",
  "status": "soluciones_generadas",
  "solutions": [
    {
      "solution_id": "uuid",
      "supplier_id": "uuid",
      "score": 0.92,
      "estimated_price": 5600,
      "currency": "MXN",
      "eta_days": 12
    }
  ]
}
```

## 4) POST /orders/{id}/submit
Confirma el envío/alta de una orden basada en solución seleccionada.

### Request
```json
{
  "solution_id": "uuid",
  "notes": "Priorizar lote piloto de 20 unidades",
  "payment_terms": "50-50"
}
```

### Response (200)
```json
{
  "order_id": "uuid",
  "status": "confirmada",
  "submitted_at": "2026-01-10T12:30:00Z"
}
```

## Estado de solicitud (referencia)
`capturada -> analizada -> soluciones_generadas -> enviada_a_proveedor -> en_negociacion -> confirmada -> completada`
