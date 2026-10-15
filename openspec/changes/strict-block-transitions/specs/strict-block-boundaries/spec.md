## ADDED Requirements

### Requirement: Confinamiento estricto de búsqueda dentro del bloque actual
El motor de reconocimiento de voz SHALL confinar la búsqueda de candidatos exclusivamente dentro del bloque actual mientras el puntero activo no haya alcanzado el final del bloque. Ninguna palabra de bloques futuros SHALL ser considerada como candidata mientras el orador continúe dentro del bloque actual.

#### Scenario: Palabras de bloques futuros ignoradas durante la lectura del bloque
- **WHEN** el orador está leyendo en cualquier posición previa al final del Bloque N y pronuncia términos que existen en el Bloque N+1 o posteriores (ejemplo: "Pin")
- **THEN** el motor de búsqueda SHALL ignorar todas las coincidencias fuera del Bloque N y mantener el puntero y el desplazamiento confinados dentro del Bloque N.

### Requirement: Transición de bloque condicionada al final del bloque y apertura del siguiente
La transición hacia el Bloque N+1 SHALL permitirse ÚNICAMENTE cuando el orador haya completado la lectura del Bloque N (puntero en la última palabra o penúltima si la última es símbolo/sigla) AND la coincidencia detectada en el Bloque N+1 corresponda estrictamente a su frase de apertura (primeras 4 palabras del bloque).

#### Scenario: Transición exitosa tras completar el bloque
- **WHEN** el orador lee la última palabra del Bloque N y pronuncia las primeras palabras del Bloque N+1
- **THEN** el motor de seguimiento SHALL avanzar al Bloque N+1 y desplazar el teleprompter de forma focal hacia el inicio del nuevo párrafo.

#### Scenario: Rechazo de saltos a palabras repetidas en el medio del siguiente bloque
- **WHEN** el orador está al final del Bloque N y se detecta una palabra que se repite a mitad o final del Bloque N+1 (más allá de las primeras 4 palabras)
- **THEN** el motor de seguimiento SHALL NOT saltar hacia esa palabra interna y SHALL permanecer en espera al final del Bloque N hasta detectar la frase de apertura.
