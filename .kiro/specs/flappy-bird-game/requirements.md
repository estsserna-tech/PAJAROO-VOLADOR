# Requirements Document

## Introduction

Juego web estilo Flappy Bird desarrollado en HTML y JavaScript puro. El jugador controla un pájaro que vuela horizontalmente y debe esquivar tuberías verdes que aparecen como obstáculos. El pájaro sube al presionar la barra espaciadora y cae por gravedad cuando no se presiona. Si el pájaro colisiona con una tubería o sale de los límites de la pantalla, el juego termina y se muestra un mensaje de derrota con opción de reinicio. El juego incluye un fondo animado (GIF), imágenes personalizadas para el pájaro y las tuberías.

## Glossary

- **Game**: El sistema principal del juego web que gestiona el bucle de juego, estado y renderizado.
- **Bird**: El personaje controlado por el jugador, representado por la imagen `pajaro.png`.
- **Pipe**: Obstáculo vertical (tubería verde) representado por la imagen `tuvo verde.png`, que aparece en pares (superior e inferior) con un hueco entre ellos.
- **Gap**: El espacio entre la tubería superior e inferior por donde el pájaro debe pasar.
- **Canvas**: El elemento HTML5 `<canvas>` donde se renderiza el juego.
- **Game Loop**: El ciclo continuo de actualización y renderizado del juego usando `requestAnimationFrame`.
- **Gravity**: La fuerza constante que hace caer al pájaro hacia abajo (0.5 px/frame²).
- **Flap**: El movimiento hacia arriba del pájaro al presionar la barra espaciadora (velocidad −8 px/frame).
- **Collision**: El contacto del bounding box del pájaro con el bounding box de una tubería o con los bordes superior/inferior del Canvas.
- **Score**: El contador de pares de tuberías que el pájaro ha superado exitosamente.
- **Game Over Screen**: La pantalla que se muestra cuando el pájaro colisiona, con el mensaje "Perdiste" y un botón de reinicio.
- **Background**: El fondo animado del juego, implementado como un GIF animado.

## Requirements

### Requirement 1: Renderizado del juego

**User Story:** Como jugador, quiero ver el juego en pantalla con todos sus elementos visuales, para poder jugar de forma clara e intuitiva.

#### Acceptance Criteria

1. THE Game SHALL renderizar todos los elementos del juego (fondo, pájaro, tuberías y puntuación) dentro del Canvas en cada fotograma, manteniendo una tasa mínima de 30 fps.
2. THE Game SHALL mostrar el fondo animado (GIF) cubriendo el 100% del ancho y el 100% del alto del Canvas.
3. THE Game SHALL mostrar el Bird usando la imagen `pajaro.png` en su posición actual en cada fotograma.
4. THE Game SHALL mostrar cada Pipe usando la imagen `tuvo verde.png`, con un ancho de 60 px por segmento y un Gap de 150 px entre el segmento superior e inferior.
5. THE Game SHALL mostrar el Score en la esquina superior izquierda del Canvas con un tamaño de fuente mínimo de 20 px, a 10 px del borde superior y 10 px del borde izquierdo.
6. IF algún recurso gráfico no puede cargarse, THEN THE Game SHALL sustituirlo por un elemento visual alternativo (color sólido o forma geométrica) sin interrumpir la ejecución del juego.

---

### Requirement 2: Física del pájaro

**User Story:** Como jugador, quiero que el pájaro tenga física realista de gravedad y salto, para que el juego sea desafiante y satisfactorio.

#### Acceptance Criteria

1. WHILE el juego está activo, THE Bird SHALL incrementar su velocidad vertical en 0.5 px/frame² hacia abajo en cada fotograma del Game Loop, con una velocidad terminal máxima de 10 px/frame hacia abajo.
2. WHEN el jugador presiona la barra espaciadora, THE Bird SHALL establecer su velocidad vertical a −8 px/frame (hacia arriba), reemplazando cualquier velocidad vertical previa.
3. THE Bird SHALL mantener su posición horizontal fija en el eje X durante toda la partida.
4. IF el borde superior del bounding box del Bird alcanza o supera el borde superior del Canvas (y = 0), THEN THE Game SHALL registrar una Collision y terminar la partida.
5. IF el borde inferior del bounding box del Bird alcanza o supera el borde inferior del Canvas (y = altura del Canvas), THEN THE Game SHALL registrar una Collision y terminar la partida.

---

### Requirement 3: Generación y movimiento de tuberías

**User Story:** Como jugador, quiero que las tuberías aparezcan de forma continua y se muevan hacia mí, para que el juego tenga dificultad progresiva y flujo constante.

#### Acceptance Criteria

1. WHILE el juego está activo, THE Game SHALL generar un nuevo par de Pipes cada 1500 ms.
2. WHEN se genera un par de Pipes, THE Game SHALL posicionar el centro del Gap a una altura aleatoria entre 100 px y (altura del Canvas − 100 px), garantizando que el Gap de 150 px sea siempre completamente transitable.
3. WHILE el juego está activo, THE Game SHALL mover todos los Pipes hacia la izquierda a una velocidad constante de 3 px/frame en cada fotograma.
4. WHEN el borde derecho de un par de Pipes sale completamente del lado izquierdo del Canvas (x + ancho ≤ 0), THE Game SHALL eliminar ese par de la lista de obstáculos activos.
5. WHILE el juego está activo, THE Game SHALL asegurar que siempre haya al menos un par de Pipes visible en pantalla, generando el primero al inicio de la partida.

---

### Requirement 4: Detección de colisiones

**User Story:** Como jugador, quiero que el juego detecte correctamente cuando el pájaro choca con una tubería, para que las reglas del juego sean justas y precisas.

#### Acceptance Criteria

1. WHEN el bounding box del Bird se superpone con el bounding box de cualquier segmento de Pipe, THEN THE Game SHALL registrar una Collision y finalizar la partida inmediatamente.
2. WHILE el juego está activo, THE Game SHALL evaluar la detección de Collision en cada fotograma del Game Loop.
3. THE Game SHALL calcular los bounding boxes del Bird y de cada segmento de Pipe usando sus coordenadas (x, y) y dimensiones (ancho, alto) reales en el Canvas.

---

### Requirement 5: Sistema de puntuación

**User Story:** Como jugador, quiero ver mi puntuación aumentar cada vez que paso una tubería, para sentir progreso y motivación durante el juego.

#### Acceptance Criteria

1. WHEN el borde izquierdo del bounding box del Bird supera el borde derecho de un par de Pipes por primera vez sin Collision, THE Game SHALL incrementar el Score en exactamente 1 punto (sin doble conteo).
2. WHILE el juego está activo, THE Game SHALL actualizar el Score mostrado en el Canvas en el mismo fotograma en que se incrementa.
3. WHEN el juego se reinicia, THE Game SHALL restablecer el Score a 0 antes de iniciar el nuevo Game Loop.

---

### Requirement 6: Pantalla de Game Over

**User Story:** Como jugador, quiero ver un mensaje claro cuando pierdo y poder reiniciar fácilmente, para saber que la partida terminó y volver a jugar sin recargar la página.

#### Acceptance Criteria

1. WHEN ocurre una Collision, THE Game SHALL detener el Game Loop en el fotograma actual y mostrar la Game Over Screen superpuesta sobre el Canvas.
2. THE Game Over Screen SHALL mostrar el texto "Perdiste" centrado horizontalmente y verticalmente en el Canvas, con un tamaño de fuente mínimo de 36 px.
3. THE Game Over Screen SHALL mostrar el Score final de la partida terminada, en texto legible con fuente mínima de 24 px.
4. THE Game Over Screen SHALL incluir un botón con la etiqueta "Reiniciar", con dimensiones mínimas de 120 × 40 px y centrado horizontalmente en el Canvas.
5. WHEN el jugador hace clic en el botón "Reiniciar", THE Game SHALL: restablecer la posición del Bird al centro vertical del Canvas, vaciar la lista de Pipes activos, restablecer el Score a 0, ocultar la Game Over Screen e iniciar un nuevo Game Loop.

---

### Requirement 7: Controles del jugador

**User Story:** Como jugador, quiero controlar el pájaro de forma simple con la barra espaciadora, para que el juego sea fácil de aprender y jugar.

#### Acceptance Criteria

1. WHEN el jugador presiona la tecla barra espaciadora (keyCode 32) y el estado del juego es "activo", THE Game SHALL ejecutar un Flap en el Bird en el mismo fotograma o en el siguiente (≤ 16 ms a 60 fps).
2. WHEN el jugador presiona la barra espaciadora y el estado del juego es "game over", THE Game SHALL ignorar la entrada sin ejecutar ninguna acción de juego.
3. THE Game SHALL registrar el evento de teclado mediante un listener de `keydown` activo durante toda la sesión de la página.

---

### Requirement 8: Carga de recursos gráficos

**User Story:** Como jugador, quiero que el juego cargue correctamente las imágenes del pájaro y las tuberías, para que la experiencia visual sea la esperada.

#### Acceptance Criteria

1. WHEN la página carga, THE Game SHALL iniciar la carga de `pajaro.png`, `tuvo verde.png` y el GIF de fondo de forma simultánea antes de iniciar el Game Loop.
2. WHEN todos los recursos gráficos han disparado el evento `load`, THE Game SHALL iniciar el Game Loop.
3. IF un recurso gráfico dispara el evento `error` durante la carga, THEN THE Game SHALL marcar ese recurso como no disponible y usar un fallback visual (rectángulo de color sólido) en su lugar, sin bloquear el inicio del Game Loop.

---

### Requirement 9: Estructura del proyecto web

**User Story:** Como desarrollador, quiero que el juego esté contenido en una estructura mínima de archivos, para que sea fácil de ejecutar abriendo el archivo en cualquier navegador moderno.

#### Acceptance Criteria

1. THE Game SHALL ejecutarse completamente en un navegador web moderno (Chrome, Firefox, Edge) sin requerir instalación de software adicional ni servidor web.
2. THE Game SHALL estar implementado usando HTML5, CSS y JavaScript puro, sin dependencias de frameworks o librerías externas.
3. THE Game SHALL referenciar los archivos de imagen (`pajaro.png`, `tuvo verde.png`) mediante rutas relativas desde el archivo HTML principal, asumiendo que todos los archivos están en el mismo directorio.
