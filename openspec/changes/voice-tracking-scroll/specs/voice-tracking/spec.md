## ADDED Requirements

### Requirement: Activación y control de seguimiento por voz
El sistema SHALL permitir activar y desactivar el modo de seguimiento por voz a través de un botón dedicado en la barra de herramientas.

#### Scenario: Activación exitosa del modo voz
- **WHEN** el usuario hace clic en el botón de micrófono y concede los permisos de audio
- **THEN** el sistema inicializa el reconocimiento continuo de voz en segundo plano y muestra un indicador visual de que el micrófono está activo escuchando.

#### Scenario: Desactivación manual por el usuario
- **WHEN** el usuario hace clic nuevamente en el botón de micrófono o activa la pausa manual (barra espaciadora)
- **THEN** el sistema detiene la captura de audio y congela la posición del texto.

#### Scenario: Navegador sin soporte de SpeechRecognition
- **WHEN** el usuario intenta activar el modo voz en un navegador que no soporta `SpeechRecognition` ni `webkitSpeechRecognition`
- **THEN** el sistema muestra una notificación explicativa indicando la incompatibilidad del navegador y permanece en modo de control manual sin provocar errores de ejecución.

### Requirement: Concordancia de voz con el guion (Matching en tiempo real)
El sistema SHALL comparar las palabras reconocidas del flujo de audio contra el contenido del guion usando una ventana deslizante hacia adelante, identificando la posición actual de lectura del orador.

#### Scenario: Lectura secuencial normal
- **WHEN** el orador pronuncia en voz alta las palabras contenidas dentro de la ventana activa del guion
- **THEN** el sistema actualiza el índice de la palabra activa en el guion y resalta visualmente el segmento o línea correspondiente.

#### Scenario: Pausas y silencios del orador
- **WHEN** el orador guarda silencio o realiza una pausa prolongada
- **THEN** el sistema mantiene fija la posición actual del guion sin desplazar el texto.

#### Scenario: Omisión leve o palabras de relleno
- **WHEN** el orador omite una palabra menor o pronuncia una muletilla no presente en el guion pero retoma las palabras siguientes de la ventana
- **THEN** el motor de concordancia tolera la discrepancia, enlaza con las palabras coincidentes posteriores y avanza la posición sin perder el rastro del orador.

### Requirement: Desplazamiento automático sincronizado (Auto-scroll guiado por voz)
El sistema SHALL desplazar suavemente el contenedor del teleprompter conforme avanza el índice de lectura para mantener la línea activa en la zona focal de lectura.

#### Scenario: Avance de línea de lectura
- **WHEN** las palabras habladas corresponden a líneas ubicadas más abajo de la guía de lectura superior
- **THEN** el contenedor realiza un desplazamiento vertical suave (`scrollTop`) para posicionar la línea activa dentro del tercio superior de la pantalla.

#### Scenario: Reinicio del guion
- **WHEN** el usuario presiona el botón de reinicio
- **THEN** el índice de lectura por voz se restablece al inicio del documento y el scroll regresa a la posición superior.

### Requirement: Sincronización de idioma del reconocedor
El sistema SHALL adaptar el idioma de escucha del motor de reconocimiento de voz de acuerdo con el idioma seleccionado en la interfaz del teleprompter.

#### Scenario: Cambio de idioma en la interfaz
- **WHEN** el usuario cambia el selector de idioma entre español e inglés
- **THEN** el servicio de reconocimiento de voz se reinicia configurando el parámetro `lang` a `es-ES` o `en-US` respectivamente.
