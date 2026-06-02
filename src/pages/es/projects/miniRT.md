---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'miniRT'
pubDate: '09.04.2025'
description: 'Raytracer 3D hecho en C puro usando MLX42.'
tags: ["C"]
---

![test_scene](/images/test_scene.png)

_Este proyecto fue realizado en colaboración con [Cimex](https://github.com/Cimex404). Él también lo ha subido a su propio repositorio._

## Acerca del proyecto

MiniRT es un ray-tracer 3D básico que utiliza la librería gráfica MLX42 (ventanas OpenGL) para renderizar una escena a partir de un archivo de escena que contiene datos de cámara, luces y objetos.\
Funciona lanzando un rayo por cada píxel de la pantalla desde la cámara y calculando las intersecciones con objetos en el espacio 3D. Los objetos tienen normales de superficie que determinan cómo el rayo debe comportarse al rebotar hacia la fuente de luz.

La escena actualmente solo puede tener una luz puntual y todos los rayos trazan hacia ella. Las sombras se calculan de dos formas: shadow rays y oclusión ambiental. Los shadow rays se generan cuando el rayo impacta un objeto y no puede continuar, simulando cómo la sombra se proyecta sobre la superficie ocluida. La oclusión ambiental, por otro lado, es puramente basada en proximidad entre objetos muy cercanos y simula cómo la luz queda atrapada en espacios estrechos y se difunde en la oscuridad.

Ambos procesos de sombreado utilizan muestreo y dispersión pseudoaleatoria para simular la difusión. Cuantos más samples, menos ruido presentan las sombras, a cambio de mayor tiempo de cómputo.

Además, se han implementado características visuales adicionales, así como controles básicos en tiempo de ejecución. La memoria se gestiona mediante un sistema rudimentario de recolección de basura y existe un sistema robusto de logging y manejo de errores para mayor comodidad.\
A pesar de ser un proyecto de 42, la última versión y todas las futuras se desviarán de **la norma**. Si deseas ver una versión totalmente compatible con la norma que entregamos, revisa el historial de commits.

## Funcionalidades

- **Assets primitivos:**\
  Actualmente el proyecto cuenta con tres objetos primitivos: planos, esferas y cilindros. Cada uno tiene parámetros únicos que pueden configurarse en el archivo de escena.
- **Iluminación:**\
  La escena está iluminada por dos fuentes: una luz ambiental y una luz puntual. La luz ambiental actúa como iluminación global y define el color base del entorno. La luz puntual emite luz desde un punto específico, con caída de intensidad y sin propagación infinita de sombras.
- **Rugosidad:**\
  Se implementa un índice de rugosidad para simular materiales distintos, afectando los reflejos especulares.
- **Reflejos:**\
  Cada objeto puede tener un índice de reflectividad. El número máximo de rebotes está limitado por configuración, pudiendo llegar hasta 100 reflexiones.
- **Entrada del usuario:**\
  Se han añadido controles de teclado para mover y rotar la cámara y modificar los samples de sombras dinámicamente.
- **Presets de calidad:**\
  Tres configuraciones de calidad: Standard, Low y High, que ajustan resolución, samples y reflexiones.

## Uso

Compilar con `make`. Limpiar con `make clean` o `make fclean`.

Ejecutar:

```bash
./miniRT "[archivo de escena / ruta al archivo de escena]"
```

El archivo debe tener extensión `.rt` y seguir un formato específico:

* Debe incluir una luz ambiental (`A`), una cámara (`C`) y una luz puntual (`L`).
* La luz ambiental tiene intensidad (0 a 1) y color RGB.
* La cámara tiene posición, rotación y FOV.
* La luz puntual tiene posición, intensidad y color.
* Objetos: planos (`pl`), esferas (`sp`) y cilindros (`cy`).
* Se pueden añadir `roughness` y `reflectivity` opcionales (0 a 1).
* Líneas comentadas con `#`.

Ejemplo:

```sh
#AST  POSITION        ROTATION    LUM/FOV RAD HGT  COLOR       ROUGH REFLECT
A                                 0.1              240,255,255
C    -50,40.5,50      1,-0.25,-1  62
L    -5,10,60                     0.84             255,196,240

pl   0,-10,0          0,1,0                        0,255,0     0     0.4
pl   100,0,0          -1,0,0                       255,0,0     0     0
pl   0,0,-100         0,0,1                        0,0,255     0     0
sp   0,0,0                                20       255,0,255   0.8   0
sp   -10,20,0                             5        10,20,30    0     0.8
cy   10,40,-30        .5,1,.5             10 20    255,255,0
cy   50.0,7,-8        -1,0,1              20 15.5  0,255,255
sp   -5,0,-21                             16       255,255,255 0
```

## Galería de renders

![alien\_planet](/images/alien_planet.png)
![mirror\_room](/images/mirror_room.png)
![underwater\_temple](/images/underwater_temple.png)
![water\_molecule](/images/water_molecule.png)
![eval\_scene\_7](/images/eval_scene_7.png)
![octagon](/images/octagon.png)

## Planes futuros

Este proyecto ha sido uno de mis favoritos del core de 42. Planeo añadir:

* mejoras de rendimiento (multithreading, GPU, BVH)
* bloom en la luz puntual
* mejor manejo de escenas grandes
* objeto cúbico
* soporte para formato OBJ
* refracción y caústicas
* bump mapping y texturas
* edición en tiempo real de assets
