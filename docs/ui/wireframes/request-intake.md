# Wireframe: RequestIntake

## Objetivo
Capturar la necesidad inicial del usuario de forma rápida por texto, audio o imagen, con una experiencia guiada y clara.

## Estructura del wireframe

```text
┌───────────────────────────────────────────────────────────────┐
│ Solicita lo que necesitas                                     │
│ Te proponemos opciones en minutos.                            │
├───────────────────────────────────────────────────────────────┤
│ ¿Qué necesitas?                                               │
│ ┌───────────────────────────────────────────────────────────┐ │
│ │ Describe tu pedido, material o servicio…                 │ │
│ │                                                           │ │
│ │                                                           │ │
│ └───────────────────────────────────────────────────────────┘ │
│                                                               │
│ [🎤 Grabar audio]    [📎 Adjuntar imagen]                    │
│ (material o referencia)                                       │
│                                                               │
│ Zona                                                          │
│ [ Guipúzcoa ▼ ]                                               │
│                                                               │
│ ☐ Autorizo el procesamiento de audio e imágenes para          │
│   entender mejor mi solicitud.                                │
│                                                               │
│ Vista previa editable                                         │
│ ┌───────────────────────────────────────────────────────────┐ │
│ │ Resumen detectado (texto + transcripción + datos imagen) │ │
│ │ [Editar]                                                  │ │
│ └───────────────────────────────────────────────────────────┘ │
│                                                               │
│                              [Enviar solicitud]              │
└───────────────────────────────────────────────────────────────┘
```

## Componentes clave
- **Textarea principal (“¿Qué necesitas?”)** para escribir una solicitud libre.
- **Botón de micrófono** para grabar audio y convertirlo automáticamente a texto.
- **Botón de adjuntar imagen** para subir fotos de material o referencias.
- **Selector rápido de zona** con **Guipúzcoa** seleccionado por defecto.

## Validaciones mínimas
1. **Al menos 1 input obligatorio**: se debe proporcionar texto, audio o imagen.
2. **Consentimiento obligatorio** para procesar audio/imagen:
   - Si hay audio o imagen, la casilla de consentimiento debe estar marcada.
   - Si solo hay texto, la casilla puede mantenerse opcional.

### Mensajes de validación sugeridos
- “Cuéntanos qué necesitas por texto, audio o imagen para continuar.”
- “Para usar audio o imágenes, necesitamos tu autorización.”

## Comportamiento esperado
1. **Transcripción automática de audio**
   - Al finalizar la grabación, el sistema transcribe el contenido.
   - La transcripción se agrega a la vista previa editable.
2. **Extracción OCR/visión básica en imágenes**
   - Al adjuntar una imagen, se detectan textos visibles y objetos/materiales básicos.
   - El resultado se suma a la vista previa editable.
3. **Vista previa editable antes de enviar**
   - Unifica: texto escrito + transcripción + datos detectados en imagen.
   - El usuario puede corregir o ampliar el contenido antes del envío.

## Microcopy (español claro, no técnico)
- Título: **“Solicita lo que necesitas”**
- Mensaje de apoyo: **“Te proponemos opciones en minutos.”**
- Placeholder textarea: **“Describe tu pedido, material o servicio…”**
- Botón audio: **“Grabar audio”**
- Botón imagen: **“Adjuntar imagen”**
- Texto ayuda imagen: **“Sube una foto del material o una referencia.”**
- Etiqueta zona: **“Zona”**
- Consentimiento: **“Autorizo el procesamiento de audio e imágenes para entender mejor mi solicitud.”**
- CTA: **“Enviar solicitud”**
