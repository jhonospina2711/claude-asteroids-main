# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Descripción del proyecto

Clon del clásico arcade **Asteroids** implementado en canvas HTML5 puro. Sin dependencias, sin bundler, sin framework. Toda la lógica del juego vive en un único archivo: `game.js`.

## Cómo correr

Abrir `index.html` directamente en el navegador, o servir con:

```bash
npx serve .
```

No hay build, lint ni tests configurados en este repo — es HTML/CSS/JS servido directamente.

## Arquitectura

Todo el juego está en `game.js` (~420 líneas), estructurado como sigue:

- **Input**: objetos globales `keys` (estado sostenido) y `justPressed` (flanco de subida, vía `pressed(code)`) poblados por listeners de `keydown`/`keyup`.
- **Clases de entidades**: `Bullet`, `Asteroid`, `Ship`, `Particle` — cada una con `update(dt)` y `draw()`. No hay clase base compartida ni sistema ECS; es composición simple.
- **Estado global del juego**: variables a nivel de módulo (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state`) reasignadas por `initGame()` / `nextLevel()`. `state` es una máquina de estados simple: `'playing' | 'dead' | 'gameover'`.
- **Bucle principal**: `loop(ts)` vía `requestAnimationFrame`, calcula `dt` en segundos (clamped a 0.05 máx.) y llama `update(dt)` → `draw()` en cada frame.
- **Colisiones**: por fuerza bruta O(n·m) con distancia euclidiana (`dist`), sin spatial partitioning — aceptable dado el volumen bajo de entidades.
- **Toroidal wrap**: la función `wrap(v, max)` envuelve posición en los bordes del canvas (800×600); todas las entidades móviles la usan en su `update()`.
- **Tamaños de asteroides**: representados como enteros 1 (pequeño) a 3 (grande); `RADII`, `SPEEDS`, `POINTS` son arrays indexados por tamaño. Al destruirse, `Asteroid.split()` genera dos asteroides de tamaño `size - 1` (o `[]` si ya es tamaño 1).

## Notas

- El README menciona power-ups y una "estrella fugaz" como asteroide especial; estas características **no** están implementadas en el código actual — no asumir su existencia.


##Testing:
