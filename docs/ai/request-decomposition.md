# Especificación funcional: descomposición de solicitudes (AI)

## Objetivo
Transformar una solicitud libre del usuario en una representación estructurada y accionable para:
1. identificar si necesita **compra**, **servicio** o una solución **mixta**;
2. separar requisitos técnicos/operativos;
3. detectar información faltante antes de consultar proveedores;
4. generar subtareas para búsqueda y matching;
5. puntuar alternativas de forma consistente.

## Alcance
Incluye:
- interpretación semántica de texto del usuario;
- extracción de campos normalizados;
- generación de preguntas de aclaración cuando falten datos críticos;
- definición de scoring para ranking de soluciones.

No incluye:
- orquestación final de órdenes o pagos;
- negociación automática con proveedores;
- pricing exacto en tiempo real.

## Flujo funcional
1. **Entrada**: mensaje libre del usuario (y opcionalmente contexto de conversación).
2. **Clasificación de intención**: `compra` | `servicio` | `mixto`.
3. **Extracción de requisitos**: material, medidas, urgencia, ubicación y detalles adicionales.
4. **Descomposición en subtareas**: p. ej. compra de materia prima + servicio de transformación.
5. **Chequeo de criticidad de datos**:
   - si falta información crítica, activar fallback y preguntar;
   - si no falta, continuar a búsqueda de proveedores.
6. **Generación de constraints**: presupuesto, tiempo máximo, distancia máxima, etc.
7. **Scoring de soluciones**: aplicar pesos y rankear opciones.
8. **Salida**: JSON estructurado + (si corresponde) preguntas de aclaración.

## Contrato de salida JSON del modelo

```json
{
  "intent": "compra|servicio|mixto",
  "requirements": {
    "material": "string|null",
    "medidas": {
      "forma": "string|null",
      "diametro_cm": "number|null",
      "ancho_cm": "number|null",
      "alto_cm": "number|null",
      "espesor_mm": "number|null",
      "unidad": "cm|mm|m|null"
    },
    "urgencia": {
      "tipo": "normal|urgente|fecha_fija|null",
      "fecha_limite": "YYYY-MM-DD|null"
    },
    "ubicacion": {
      "ciudad": "string|null",
      "region": "string|null",
      "pais": "string|null",
      "coordenadas": {
        "lat": "number|null",
        "lng": "number|null"
      }
    }
  },
  "subtasks": [
    {
      "id": "string",
      "tipo": "compra|servicio",
      "descripcion": "string",
      "dependencias": ["string"]
    }
  ],
  "constraints": {
    "presupuesto_max": "number|null",
    "moneda": "CLP|USD|EUR|MXN|null",
    "tiempo_max_dias": "number|null",
    "distancia_max_km": "number|null"
  },
  "missing_info": [
    {
      "campo": "string",
      "pregunta": "string",
      "razon_criticidad": "string"
    }
  ]
}
```

## Reglas de validación mínimas
- `intent` debe ser uno de: `compra`, `servicio`, `mixto`.
- `requirements` siempre debe existir aunque tenga campos `null`.
- `subtasks` puede ser vacío solo si `missing_info` contiene al menos un dato crítico.
- `missing_info` debe incluir preguntas concretas y accionables.
- No inventar valores de presupuesto, medidas o fechas: usar `null` si no están explícitos.

## Estrategia de fallback (datos críticos faltantes)

### Qué se considera dato crítico
Se considera crítico cuando su ausencia impide seleccionar proveedores con confianza mínima:
- **Compra de material**: tipo de material, medidas base, ubicación de entrega/retiro.
- **Servicio**: tipo de servicio exacto, ubicación, ventana de tiempo.
- **Mixto**: los críticos de cada subtarea principal.

### Comportamiento esperado
1. Detectar campos críticos faltantes tras la extracción inicial.
2. Poblar `missing_info` con preguntas ordenadas por impacto.
3. **Bloquear búsqueda de proveedores** hasta recibir respuesta de aclaración.
4. Reintentar extracción al recibir nuevas respuestas del usuario.

### Plantilla de pregunta automática
- "Para poder cotizar bien, me falta **{campo}**. ¿Podrías indicar **{pregunta concreta}**?"

### Ejemplo de activación
Solicitud: "Necesito cortar una plancha para mañana".
- Falta material, medidas, ciudad.
- El sistema responde preguntas antes de buscar proveedores.

## Scoring de soluciones (ranking)

## Fórmula general
`score_total = Σ (peso_i * score_i_normalizado)`

Donde cada `score_i_normalizado` está en escala 0-100.

### Pesos sugeridos iniciales
- **Ajuste técnico**: 0.35
- **Precio estimado**: 0.20
- **Tiempo de entrega**: 0.20
- **Distancia**: 0.10
- **Reputación / probabilidad de aceptación**: 0.15

> Los pesos deben ser configurables por vertical o categoría.

### Definición de cada componente
1. **Ajuste técnico (35%)**
   - Coincidencia de capacidad/proceso con requerimientos.
   - 100: cumple todo; 50: cumple parcial con riesgo; 0: no cumple.
2. **Precio estimado (20%)**
   - Normalización inversa (más bajo = mejor), con control de outliers.
3. **Tiempo de entrega (20%)**
   - Penalizar soluciones que superen urgencia/fecha límite.
4. **Distancia (10%)**
   - Menor distancia o mejor logística obtiene mayor score.
5. **Reputación/probabilidad de aceptación (15%)**
   - Historial de cumplimiento, rating, tasa de respuesta/aceptación.

### Reglas de desempate
1. Mayor ajuste técnico.
2. Menor tiempo de entrega.
3. Menor precio.
4. Mayor reputación.

## Casos de ejemplo resumidos
1. **Madera circular** → `mixto` (compra tablero + corte).
2. **Sesión de fotos** → `servicio` (fotógrafo + opcionales de estudio).
3. **Calefacción** → `mixto` (compra equipo + instalación).

## Métricas recomendadas para iteración
- Tasa de solicitudes con `missing_info` crítico.
- Porcentaje de aclaraciones resueltas en 1 turno.
- Precisión de `intent` vs evaluación manual.
- Conversión de búsqueda a cotización útil.
- Correlación entre score y selección final del usuario.
