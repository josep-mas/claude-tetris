# Propuestas de mejora

Este documento recoge mejoras posibles para el Tetris, ordenadas por prioridad. No implica que todas deban implementarse: las primeras propuestas se centran en corregir comportamientos y facilitar el mantenimiento antes de añadir funcionalidades.

## Prioridad alta: estabilidad y experiencia de juego

### Corregir la pausa y la reanudación

Al pausar se muestra el overlay, pero al reanudar no se oculta. La partida continúa detrás de él. Ocultar el overlay al reanudar y comprobar que la pausa no inicia bucles de animación duplicados.

### Detener el bucle cuando termina la partida

`endGame()` cancela el `requestAnimationFrame` actual, pero si se invoca desde `loop()`, esa misma ejecución puede programar otro frame al final. Evitar programar más frames cuando `gameOver` sea `true` y comprobar también el estado después de bloquear o generar una pieza.

### Mejorar la adaptación a pantallas pequeñas

El tablero y el panel usan dimensiones fijas y pueden desbordar en móviles. Añadir estilos responsivos para escalar el tablero manteniendo su proporción, reorganizar el panel y permitir controles táctiles accesibles.

## Prioridad media: calidad del código y pruebas

### Separar la lógica del renderizado y del DOM

`game.js` combina reglas del juego, estado, entrada de teclado y Canvas. Separar esas responsabilidades facilitaría probar la lógica sin navegador y modificar la interfaz sin afectar las mecánicas.

### Añadir pruebas automatizadas

Probar, como mínimo, colisiones en bordes y con bloques, rotaciones y desplazamientos, limpieza de varias líneas, cálculo de puntuación y progresión de nivel. Las pruebas también ayudarían a proteger las correcciones de pausa y fin de partida.

### Mejorar la precisión del intervalo de caída

El bucle actualmente reinicia el acumulador cuando supera el intervalo, descartando el tiempo sobrante. Restar el intervalo acumulado o procesar los pasos pendientes evita que la velocidad dependa tanto de pequeñas variaciones de los frames.

### Revisar accesibilidad y controles

Evitar que `user-select: none` afecte innecesariamente a toda la página, añadir foco visible para el botón de reinicio, etiquetas accesibles y una alternativa a los controles de teclado. Mostrar también en la interfaz la tecla `X`, que el código acepta para rotar.

## Prioridad baja: funcionalidades opcionales

- **Guardar récord local:** persistir la mejor puntuación con `localStorage`.
- **Pieza en espera:** permitir guardar e intercambiar una pieza una vez por turno.
- **Previsualización de varias piezas:** mostrar una cola configurable en lugar de solo la siguiente.
- **Sonido y efectos:** añadir efectos opcionales al mover, soltar piezas y limpiar líneas, con control para silenciarlos.
- **Configuración:** ofrecer controles para volumen, teclas y dificultad.

## Secuencia sugerida

1. Corregir la pausa y el fin del bucle de animación.
2. Añadir pruebas de las reglas principales.
3. Separar gradualmente la lógica del juego de la interfaz.
4. Mejorar la presentación móvil y accesibilidad.
5. Incorporar funcionalidades opcionales según las prioridades del proyecto.
