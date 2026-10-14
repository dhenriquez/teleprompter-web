## 1. Interfaz de Usuario y Detección de Capacidades

- [ ] 1.1 Agregar botón de micrófono con icono SVG y estados visuales (inactivo, escuchando, error/no soportado) en la barra de herramientas.
- [ ] 1.2 Agregar textos y tooltips traducidos para el modo de voz ("Voice Tracking") en `TRANSLATIONS` para español e inglés.
- [ ] 1.3 Implementar verificación de compatibilidad de `webkitSpeechRecognition` / `SpeechRecognition` al montar la aplicación y gestionar permisos de micrófono.

## 2. Tokenización y Estructura del Guion

- [ ] 2.1 Implementar función de normalización y tokenización del guion que extraiga palabras limpias (sin puntuación ni mayúsculas) asociadas a su posición.
- [ ] 2.2 Modificar el renderizado del texto en pantalla para etiquetar las palabras con identificadores secuenciales (`data-word-index`) manteniendo el formato de bloques `[bloque]` y variables `[texto]`.
- [ ] 2.3 Añadir estilos CSS para resaltar de forma sutil la palabra o línea activa del orador sin distraer en cámara.

## 3. Motor de Reconocimiento de Voz y Concordancia

- [ ] 3.1 Implementar el controlador de `SpeechRecognition` con configuración continua (`continuous: true`, `interimResults: true`) y sincronización con el idioma seleccionado (`lang`).
- [ ] 3.2 Implementar el algoritmo de concordancia con ventana deslizante (12–18 palabras) que compare los fragmentos hablados con la zona activa del guion.
- [ ] 3.3 Gestionar la reconexión automática en el evento `onend` mientras el modo de seguimiento permanezca activo para evitar cortes por silencios prolongados.

## 4. Desplazamiento Automático (Auto-Scroll) e Integración

- [ ] 4.1 Calcular el offset vertical de la palabra activa y aplicar desplazamiento suave (`scrollTop`) para posicionar la línea leída en el tercio superior de la pantalla.
- [ ] 4.2 Sincronizar el botón de reinicio y los atajos de teclado para resetear la posición del puntero de voz al inicio del guion.
- [ ] 4.3 Validar la tolerancia a pausas, muletillas y lectura rápida en el navegador.
