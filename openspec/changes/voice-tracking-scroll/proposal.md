## Why

Al grabar videos para YouTube o presentaciones de contenido extenso, mantener un ritmo de lectura fluido y natural con un desplazamiento a velocidad fija (manual) resulta problemático: el creador se ve forzado a ajustarse a la velocidad predefinida del teleprompter, acelerando su voz o perdiendo el hilo si realiza pausas reflexivas o improvisa. 

El seguimiento por voz ("Voice Tracking") soluciona este problema haciendo que el teleprompter escuche al orador y avance únicamente a medida que este pronuncia las palabras del guion, deteniéndose automáticamente en pausas y reanudando la marcha sin necesidad de intervención manual.

## What Changes

- **Modo Voice Tracking (Seguimiento por Voz)**: Nuevo botón de alternancia en la barra de herramientas para activar o pausar el modo de seguimiento de voz mediante micrófono.
- **Integración con Web Speech API**: Uso de la interfaz de reconocimiento de voz del navegador (`SpeechRecognition` / `webkitSpeechRecognition`) configurada con el idioma seleccionado (español o inglés).
- **Tokenización y marcado del guion**: Renderizado del texto con segmentación de palabras/bloques rastreables para asociar la transcripción de audio con nodos en el DOM.
- **Motor de concordancia (Matching) con ventana deslizante**: Algoritmo de comparación que busca coincidencias de palabras habladas en la región actual del guion, tolerando pequeñas omisiones o variaciones de pronunciación.
- **Auto-scroll inteligente y suave**: Desplazamiento continuo hacia la palabra o bloque activo manteniendo la línea en la zona de lectura óptima.
- **Feedback visual y gestión de permisos**: Indicador visual de estado del micrófono (escuchando, inactivo, error de permisos o API no soportada) y fallback fluido al desplazamiento manual estándar.

## Capabilities

### New Capabilities
- `voice-tracking`: Reconocimiento de voz en tiempo real con Web Speech API, concordancia de palabras contra el guion y desplazamiento automático sincronizado con la lectura del orador.

### Modified Capabilities

## Impact

- **Código**: Extensión de [index.html](file:///Users/danielhenriquezsandoval/Documents/Proyectos/teleprompter-web/index.html) para incorporar el hook/servicio de reconocimiento de voz, tokenización de palabras en el área de prompter y estados adicionales de interfaz.
- **APIs del Navegador**: Requiere la `Web Speech API` y solicitud de permiso de micrófono (`getUserMedia` / permisos de speech).
- **Compatibilidad**: Funciona de forma nativa en navegadores Chromium (Google Chrome, Microsoft Edge, Brave) y Safari compatible; en navegadores sin soporte muestra aviso y mantiene el modo manual estándar.
- **Dependencias**: Cero dependencias externas adicionales; se mantiene 100% en el cliente y sin backend.
