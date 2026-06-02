---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'Grimoire'
pubDate: '02.05.2026'
description: 'Una aplicación de gestión de partidas de Blood on the Clocktower para Android.'
tags: ["Kotlin", "Android"]
---

## Acerca del proyecto

Alrededor de Halloween de 2025 descubrí este pequeño e interesante juego llamado Blood on the Clocktower gracias a algunos compañeros. Vi varios vídeos sobre él y rápidamente me enamoré del concepto. Blood on the Clocktower es un juego de deducción social similar a Hombre Lobo o Mafia, pensado principalmente para jugarse en un entorno social presencial.

El juego se basa en "scripts", que son selecciones de personajes y reglas diseñadas por los creadores del juego o por la comunidad. Los scripts están diseñados para que los distintos roles funcionen bien en conjunto. Donde vi una oportunidad de aportar algo fue en la fase de preparación de la partida. BotC se juega con una persona que actúa como director de juego (conocido como el "Storyteller"), responsable de asignar los roles y gestionar toda la información que recibe cada jugador en cada ronda. Como puedes imaginar, esta persona tiene una gran responsabilidad, algo que puedo confirmar tras haber desempeñado el papel de Storyteller en varias ocasiones.

Y ya sabes lo que dicen: la necesidad es la madre de la invención.

Comencé este proyecto en C# como una aplicación de línea de comandos y más tarde lo porté a Kotlin como una aplicación para Android. Utilicé los recursos oficiales obtenidos de la wiki de BotC y empleé Jetpack Compose para construir la interfaz de usuario.

En su estado actual, la aplicación permite generar una lista completa de roles asignados a los jugadores seleccionados a partir de un script elegido. Los scripts están integrados en la aplicación, pero en realidad son archivos JSON que analizo dinámicamente, por lo que mi intención es permitir que los usuarios importen sus propios scripts en el futuro.

<div style="display: flex; gap: 1rem; align-items: flex-start;">
	<img src="/images/script_menu.png" alt="assignments1" style="width: calc(50% - 0.5rem); aspect-ratio: 450 / 919; object-fit: cover;" />
	<img src="/images/grimiore.gif" alt="assignments2" style="width: calc(50% - 0.5rem); aspect-ratio: 450 / 919; object-fit: cover;" />
</div>

## Funcionalidades

- Gestor de scripts que analiza dinámicamente los scripts disponibles.
- Menú de vista previa que permite al usuario visualizar todos los scripts cargados.
- Pantalla de configuración de partida con un menú desplegable para seleccionar el script y una herramienta para añadir jugadores.
- Algoritmo que asigna automáticamente roles y subroles a los jugadores añadidos siguiendo las reglas y dinámicas del juego.
- Pantalla de vista previa que muestra todos los roles asignados a los jugadores y otra información relevante para el Storyteller.
- Sistema para calcular los Jinxes activos entre roles (reglas especiales utilizadas para resolver conflictos entre determinadas combinaciones de personajes).

<div style="display: flex; gap: 1rem; align-items: flex-start;">
	<img src="/images/script_assigned1.png" alt="assignments1" style="width: calc(50% - 0.5rem); height: auto;" />
	<img src="/images/script_assigned2.png" alt="assignments2" style="width: calc(50% - 0.5rem); height: auto;" />
</div>

## Planes futuros

Tengo planes bastante ambiciosos para este proyecto a largo plazo. No puedo prometer nada en un plazo razonable, pero algunas de mis ideas son:

- Añadir una pantalla de resultado final con todos los jugadores colocados en sus posiciones y mostrando sus roles y subroles.
- Añadir la visualización de los personajes Loric y de los Fabled/Jinxes en dicha pantalla de resultado final.
- Implementar un ciclo de juego completo que permita al Storyteller avanzar por todas las rondas mientras la aplicación realiza el seguimiento de toda la información relevante.
- Portar la aplicación a PC mediante Kotlin Multiplatform.
- Automatizar completamente las partidas mediante un Storyteller automático.
