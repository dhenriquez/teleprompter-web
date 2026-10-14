## Context

La aplicación actual ([index.html](file:///Users/danielhenriquezsandoval/Documents/Proyectos/teleprompter-web/index.html)) es una Single Page Application autónoma basada en React 18 (vía CDN), Tailwind CSS y Babel Standalone en el navegador.

Actualmente, el desplazamiento se realiza mediante un bucle de animación con `requestAnimationFrame` a una tasa lineal constante (`speed * 0.5 * delta`). Aunque esto es funcional, al grabar videos para YouTube el orador necesita pausar para respirar, enfatizar ideas o rectificar frases; la velocidad fija provoca que el texto se escape o que el creador deba estirar la mano hacia el teclado para pausar la grabación.

## Goals / Non-Goals

**Goals:**
- Implementar un modo de seguimiento por voz ("Voice Tracking") que avance el texto de forma reactiva al ritmo del orador usando el micrófono.
- Mantener la arquitectura client-side sin servidores intermedios, sin costos de API y sin alterar la ligereza del archivo único.
- Proporcionar retroalimentación visual clara (micrófono activo, escuchando, error de permiso).
- Mantener compatibilidad total y fallback transparente al modo de desplazamiento manual por velocidad fija.
- Asegurar ergonomía de lectura en cámara: mantener la línea que se está leyendo en el tercio superior de la pantalla (cerca de la lente de la cámara).

**Non-Goals:**
- No se grabará ni almacenará el audio del usuario.
- No se integrarán modelos locales pesados de inteligencia artificial (ej. Whisper vía ONNX/WASM de más de 40 MB) en esta fase inicial.
- No se realizará análisis fonético complejo ni identificación de múltiples oradores (diarización).

## Decisions

### 1. Motor de reconocimiento: Web Speech API nativa
- **Decisión**: Usar la interfaz nativa `SpeechRecognition` (o `webkitSpeechRecognition` para navegadores WebKit/Chromium).
- **Razón**: No añade peso de descarga al proyecto, arranca instantáneamente y aprovecha los motores de reconocimiento optimizados del sistema/navegador.
- **Alternativas consideradas**: Whisper local con `@xenova/transformers` (descartado por consumo elevado de memoria/CPU que afectaría la grabación del video) y APIs en la nube con cobro como Deepgram o Google Cloud STT (descartado para mantener el proyecto gratuito y sin backend).

### 2. Tokenización y marcado de palabras en el DOM
- **Decisión**: Al renderizar el texto en pantalla, envolver cada palabra del guion en un elemento `span` con un atributo de índice (`data-word-index="N"`).
- **Razón**: Permite conocer con precisión milimétrica la posición vertical relativa (`offsetTop`) de la palabra actual respecto al contenedor del prompter, facilitando un scroll suave y el centrado en la zona de visión.

### 3. Motor de concordancia con Ventana Deslizante (Sliding Window)
- **Decisión**: El reconocedor compara la transcripción entrante únicamente contra las siguientes 12–18 palabras a partir del índice actual de lectura, en lugar de escanear todo el texto.
- **Razón**: 
  - Evita saltos falsos provocados por palabras comunes repetidas en distintos párrafos (ej. "el", "de", "un").
  - Si el orador dice una muletilla o salta una palabra menor, el algoritmo busca la mejor coincidencia hacia adelante y continúa sin trabarse.

```
       [Palabras ya leídas]          [Ventana Activa (15 palabras)]           [Texto Futuro]
... Bienvenidos al canal de YouTube. [Hoy vamos a explicar cómo configurar el] ESP32 paso a paso...
                                      ▲                   ▲
                               Índice Actual       Próximo Match
```

### 4. Desplazamiento interpolado y suave (Smooth Focal Scrolling)
- **Decisión**: Cuando la palabra coincidente sobrepasa la marca focal de lectura (~25-30% de la altura de la ventana), el contenedor actualiza su objetivo de scroll mediante animación suave (`requestAnimationFrame` con interpolación lineal o `scrollTo({ behavior: 'smooth' })`).
- **Razón**: Evita saltos bruscos que marean al orador o desvían su mirada fija de la cámara.

### 5. Protección de límites de bloque (Block-Boundary Protection)
- **Decisión**: Cada palabra del guion se asocia a su bloque (`blockIndex`). El algoritmo prohíbe emparejar palabras de bloques futuros a menos que el orador se encuentre en la cola del bloque actual (últimas 5 palabras), e incluso en ese caso solo permite la transición al bloque inmediatamente siguiente (`currentBlock + 1`), nunca saltando bloques completos.
- **Razón**: Evita saltos falsos provocados por frases recurrentes en el guion (por ejemplo, "Cable Link" o "Pokémon") que se repiten en diferentes párrafos separados.

## Risks / Trade-offs

- **[Riesgo: Navegador sin soporte de Web Speech API (ej. Firefox en ciertas plataformas)]**  
  → *Mitigación*: Detección previa de la API al montar el componente. Si no está disponible, el botón de micrófono se desactiva elegantemente con un tooltip explicativo que invita a usar Chrome/Edge o utilizar el modo de velocidad manual.
- **[Riesgo: Discrepancias ortográficas o acentos en palabras técnicas (ej. "ESP32", "API")]**  
  → *Mitigación*: Normalización de texto (eliminar tildes, signos de puntuación y mayúsculas en el matcher) y tolerancia a omisiones mediante avance por proximidad de palabras adyacentes.
- **[Riesgo: Pausas largas con corte automático de la API de voz en Chrome]**  
  → *Mitigación*: En el manejador `onend`, si el modo de voz sigue activo (`isVoiceTrackingActive === true`), reiniciar automáticamente el reconocimiento de manera transparente.

## Open Questions

- ¿Desea el usuario un indicador visual específico en el texto (ej. resaltar en color sutil la palabra activa) además de mover el scroll? *(Se dejará configurable o sutil por defecto para no distraer durante la lectura en cámara)*.
