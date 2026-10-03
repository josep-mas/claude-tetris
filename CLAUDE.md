# CLAUDE.md

Este archivo proporciona orientación a Claude Code (claude.ai/code) cuando trabaja con código en este repositorio.

## Descripción del Proyecto

**Tetris** es una implementación en JavaScript vanilla del clásico juego Tetris. Sin dependencias externas, sin proceso de compilación—solo abre `index.html` en el navegador y juega.

## Ejecutar el Juego

**Opción 1: Archivo directo**
```bash
# Linux
xdg-open index.html
# macOS
open index.html
# Windows
start index.html
```

**Opción 2: Servidor local (recomendado)**
```bash
# Python 3
python3 -m http.server 8000

# Node.js
npx serve .

# PHP
php -S localhost:8000
```

Luego visita `http://localhost:8000` en el navegador.

## Arquitectura del Código

El juego se divide en tres archivos, cada uno con una única responsabilidad:

### 1. `index.html`
- Estructura DOM: canvas del juego (300 × 600 px), panel lateral con puntuación/nivel/pieza siguiente, overlay para estados pausa/game over
- Sin estilos inline; los estilos están en `style.css`
- Elementos canvas: `#board` (juego principal) y `#next-canvas` (vista previa)
- Referencias de elementos por ID utilizadas por JavaScript

### 2. `style.css`
- Estética dark/arcade retro
- Layout flexbox para el contenedor principal, contenedor del juego y panel lateral
- Sin variables CSS; los colores están hardcodeados
- Minimal (~150 líneas)

### 3. `game.js`
Toda la lógica del juego está aquí (~305 líneas). Secciones clave:

**Constantes al inicio:**
- `COLS`, `ROWS`, `BLOCK`: dimensiones del tablero y tamaño en píxeles por celda
- `COLORS`: valores hex de colores 1–7 para los siete tipos de piezas
- `PIECES`: definiciones de piezas como matrices 4×4 con índices de color
- `LINE_SCORES`: puntos por limpiar 1, 2, 3 o 4 líneas (índice 0 no usado)

**Modelo del tablero:**
- `board`: matriz 2D (`ROWS × COLS`). Cada celda es `0` (vacía) o un índice de color (1–7)
- `current`: objeto de la pieza activa con `{ type, shape, x, y }`
- `next`: la pieza en cola

**Funciones principales:**
- `createBoard()`: matriz vacía
- `randomPiece()`: genera una pieza aleatoria de `PIECES`, centrada horizontalmente
- `collide(shape, ox, oy)`: verifica si una pieza golpea el límite o bloques existentes
- `rotateCW(shape)`: rota una pieza 90° en sentido horario (transposición + reverso de filas)
- `tryRotate()`: intenta rotación con wall kicks (desplazamientos de ±0, ±1, ±2 columnas)
- `merge()`: fija la pieza actual en el tablero
- `clearLines()`: elimina filas completadas, desplaza filas superiores hacia abajo, actualiza puntuación/nivel
- `ghostY()`: calcula dónde aterrizará la pieza (para la pieza fantasma)
- `hardDrop()`: caída instantánea con bonificación de puntos (2 pts por fila)
- `softDrop()`: caída acelerada con puntos mínimos (1 pt por fila)
- `spawn()`: mueve `next` a `current`, genera nuevo `next`, verifica game over

**Renderizado:**
- `drawBlock(context, x, y, colorIndex, size, alpha)`: dibuja un bloque de color único con destello blanco
- `drawGrid()`: dibuja líneas de la cuadrícula
- `draw()`: limpia canvas, dibuja cuadrícula, tablero, pieza fantasma y pieza activa
- `drawNext()`: renderiza la siguiente pieza en `nextCanvas` (centrada en 4×4)

**Game loop:**
- `loop(ts)`: llamada por `requestAnimationFrame`, acumula delta de tiempo y baja piezas en intervalos
- `dropInterval`: velocidad en milisegundos, comienza en 1000, disminuye según aumenta el nivel

**Gestión de estado:**
- Todo el estado del juego tiene alcance de módulo (no en un objeto o clase)
- `init()`: reinicia todo e inicia un nuevo juego
- `togglePause()`: lógica de pausa/reanudación
- `endGame()`: muestra overlay de game over, detiene el animation loop

**Entrada:**
- Un único listener `keydown` manejando teclas de flecha, tecla X, barra espaciadora y tecla P
- Colisión verificada antes de cada movimiento; se actualiza solo si no hay colisión

## Personalización

Parámetros más fáciles de ajustar en `game.js`:

| Constante | Propósito | Por defecto |
|-----------|-----------|-------------|
| `COLS` | Ancho del tablero | 10 |
| `ROWS` | Alto del tablero | 20 |
| `BLOCK` | Tamaño en píxeles de cada celda | 30 |
| `COLORS` | Colores hex para piezas 1–7 | 7 colores |
| `LINE_SCORES` | Puntos: [0, 1-línea, 2-línea, 3-línea, 4-línea] | [0, 100, 300, 500, 800] |

**Importante:** Si cambias `COLS`, `ROWS` o `BLOCK`, actualiza las dimensiones del canvas en `index.html`:
- `#board`: `width = COLS * BLOCK`, `height = ROWS * BLOCK`
- `#next-canvas`: típicamente `120 × 120` (o ajusta según sea necesario)

## Detalles de Implementación Clave

- **Rotación de piezas:** usa transposición de matriz + reverso de filas (`rotateCW`)
- **Wall kicks:** intenta desplazamientos [0, -1, 1, -2, 2] cuando la rotación golpea una pared (evita piezas bloqueadas frustrantemente)
- **Progresión de nivel:** aumenta cada 10 líneas; la velocidad se escala como `max(100, 1000 - (level - 1) * 90)` ms
- **Puntuación:** la puntuación de limpiar líneas es `LINE_SCORES[num_líneas] * nivel`; bonificaciones por caída son separadas
- **Pieza fantasma:** dibujada con alfa 20% para mostrar dónde aterrizará la pieza actual
- **Sin persistencia de estado:** el juego se reinicia al refrescar; sin localStorage

## Tareas Comunes

**Añadir una nueva forma de pieza:**
1. Define una matriz 4×4 en `PIECES` con un índice de color único
2. Añade un nuevo color a `COLORS` si es necesario
3. Ajusta `randomPiece()` si usas un número diferente de piezas

**Ajustar dificultad:**
- Cambia la fórmula `dropInterval` en `clearLines()` para escalar la velocidad diferentemente
- Modifica `LINE_SCORES` para diferentes curvas de recompensa

**Ajustar visuales:**
- Colores de bloques: edita valores hex de `COLORS`
- Apariencia de cuadrícula: ajusta estilo de trazo y ancho de línea en `drawGrid()`
- Efecto de destello: modifica el overlay blanco en `drawBlock()`
- Tema dark: edita variables CSS o colores hardcodeados en `style.css`

**Añadir características (ejemplos):**
- Pieza en espera: almacena `held` pieza, intercambia con actual al presionar tecla
- Bloqueo de soft drop: añade retardo antes de que una pieza se bloquee al tocar el fondo
- Efectos de sonido: llama `audio.play()` en `clearLines()`, `hardDrop()`, etc.
- Efectos de partículas: añade dibujo canvas en el loop `draw()`