## 1. Confinamiento de Búsqueda Intra-Bloque

- [x] 1.1 Limitar la ventana de búsqueda `maxLookahead` estrictamente al final del bloque actual (`currentRange.end + 1`) mientras el puntero `curr` no haya alcanzado el final del bloque.
- [x] 1.2 Asegurar que candidatos pertenecientes a bloques futuros sean descartados inmediatamente mientras el orador continúe leyendo dentro del bloque.

## 2. Transición y Filtro de Apertura (Front-Door)

- [x] 2.1 Implementar la condición de desbloqueo de transición: permitir candidatos en el bloque inmediatamente posterior (`candBlock === currentBlock + 1`) únicamente cuando `curr >= blockEnd` (o `curr >= blockEnd - 1` con frase conectada).
- [x] 2.2 Restringir la coincidencia en el Bloque N+1 estrictamente a su frase de apertura (`j - nextBlockStart <= 4`) para rechazar términos repetidos más adelante en el párrafo.
- [x] 2.3 Verificar en navegador y con el guion de prueba que palabras repetidas como "Pin" en párrafos continuos no generen saltos intempestivos ni dejen atrapado al orador.
