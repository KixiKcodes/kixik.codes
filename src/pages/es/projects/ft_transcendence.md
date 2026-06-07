---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'ft_transcendence'
pubDate: '06.11.2025'
description: 'Proyecto final del Core Curriculum de 42. Prototipo full-stack de una aplicación web centrada en el clásico Pong.'
source: 'https://github.com/KixiKcodes/ft_transcendence'
tags: ["TypeScript", "JavaScript", "HTML", "CSS", "Docker"]
---

En colaboración con mis compañeros [Jacob](https://github.com/Cimex404), [Tobias](https://github.com/tmkeil) y [Betül](https://github.com/Bebuber).

## Acerca del proyecto
Este es el proyecto final del Common Core de 42. Su objetivo es poner a prueba todas nuestras habilidades mediante el desarrollo de una aplicación web full-stack con una gran cantidad de funcionalidades. La premisa consiste en crear nuestra propia versión de Pong con algunas características modernas elegidas por nosotros. El proyecto se compone de una parte obligatoria y de módulos opcionales Major y Minor que contribuyen a la nota final.

![Menú Principal](/images/transcendence_menu.png)

## Funcionalidades básicas
Las funcionalidades básicas que debía incluir Transcendence son una partida local de Pong entre dos jugadores y un sistema de torneos por rondas para varios jugadores. La jugabilidad debe ser fiel a la del juego original de 1972. El funcionamiento exacto del sistema de torneos queda a nuestra elección, por lo que nos tomamos ciertas libertades en su diseño. Nuestro sitio web también debe ser seguro y estar protegido contra inyecciones SQL y llamadas maliciosas a la API. Me centré principalmente en este aspecto, desarrollando el flujo de inicio de sesión y registro, el sistema de autenticación en dos factores (2FA), la gestión de JWT y cookies, así como la protección de la API mediante pre-handlers de autorización.

## Módulos
Elegimos 9 módulos Major y 4 módulos Minor. Un módulo Minor vale la mitad de un Major, lo que suma un total de 11 puntos de módulos. El requisito para obtener una calificación del 100% es de 7 puntos, por lo que alcanzamos la bonificación máxima del 125%.

### Módulos Major:
- **Framework Backend (Fastify)** - Nuestras rutas y endpoints del backend utilizan Fastify para JavaScript.
- **Autenticación Remota** - Existe soporte para Google Authenticator como sistema de autenticación de terceros.
- **Jugadores Remotos** - El juego puede jugarse en línea con jugadores remotos mediante el uso de WebSockets.
- **Chat en Tiempo Real** - Existe un sistema de chat en vivo donde los jugadores pueden enviar mensajes, invitaciones a partidas y consultar las estadísticas de otros usuarios.
- **Panel de Usuario y Estadísticas** - Hay un panel dinámico donde los usuarios pueden consultar las estadísticas de otros jugadores y enviar solicitudes de amistad. También incluye un sistema de fotos de perfil y un sistema de bloqueo de usuarios.
- **Oponente IA** - Un modo adicional donde los jugadores pueden jugar indefinidamente contra una inteligencia artificial con tres niveles de dificultad.
- **Autenticación en Dos Factores y JWT** - Las sesiones se almacenan mediante cookies del navegador y se verifican utilizando JSON Web Tokens. Los usuarios pueden activar 2FA y escanear un código QR para vincular Google Authenticator a su perfil.
- **Gráficos 3D (Babylon)** - El juego se renderiza en 3D utilizando una biblioteca gráfica llamada Babylon.js (de donde nuestro equipo tomó su nombre).
- **API de Pong en el Servidor** - Junto con el sistema de jugadores remotos, calculamos en el servidor los datos del juego, como las posiciones de las paletas y la velocidad de la pelota, y los enviamos a los clientes mediante una API personalizada.

### Módulos Minor:
- **Framework Frontend** (Tailwind y Vite) - El frontend utiliza Tailwind CSS para el diseño y renderizado de la interfaz de usuario, además de Vite para recargas rápidas.
- **Implementación de Base de Datos (SQLite)** - Utilizamos una base de datos para almacenar información de usuarios, amistades, bloqueos y datos de partidas.
- **Sistema de Monitorización DevOps (Prometheus, Grafana y Alert Manager)** - Se configuraron tres contenedores adicionales para recopilar métricas y visualizarlas. También existe un sistema de alertas conectado a un canal personalizado de Slack.
- **Compatibilidad con Múltiples Navegadores** - Nuestro Transcendence funciona en varios navegadores (aunque Firefox es su navegador principal).

![Iniciar sesión o registrarse](/images/transcendence_login.png)

![GIF de jugabilidad](/images/gameplay.gif)

## Uso
- Puedes clonar este repositorio en tu máquina:
```sh
git clone https://github.com/KixiKCodes/ft_transcendence
```

* Debes crear un archivo `.env` en la raíz del repositorio con las siguientes variables:

```conf
JWT_SECRET=[Clave secreta para JSON Web Token]

GF_SECURITY_ADMIN_USER=[Nombre de usuario administrador de Grafana]
GF_SECURITY_ADMIN_PASSWORD=[Contraseña del administrador de Grafana]
GF_AUTH_ANONYMOUS_ENABLED=false
GF_USERS_ALLOW_SIGN_UP=true

SLACK_WEBHOOK_URL=[URL del Webhook de Slack para Alert Manager]
```

* Después, puedes utilizar el Makefile para compilar todo rápidamente:
  `make build`

(Puedes usar `make help` para obtener información sobre comandos adicionales de Make).

* Una vez que todo esté en funcionamiento, navega a `https://localhost:8443/` y omite la advertencia relacionada con el certificado SSL autofirmado.

* ¡Y listo! El proyecto estará funcionando.

## Créditos

**Jacob Graf** - Diseño del juego, lógica de juego, gráficos 3D, estadísticas de usuario, arquitectura general del proyecto, implementación de todos los modos de juego y pruebas.

**Tobias Keil** - Enrutamiento web y WebSockets, sistemas de red y jugadores remotos, configuración de base de datos y backend, arquitectura general del proyecto, implementación del chat en vivo y del panel de usuario, sistemas de amistades, bloqueos e invitaciones, y pruebas.

**Betül Büber** - Sistemas DevOps y de monitorización, y pruebas.

**Yo mismo** - Ciberseguridad, flujo de registro de usuarios, sistemas de autenticación y autorización, configuración de usuario, frontend y diseño de interfaz, gestión del proyecto, diseño de sonido y música, pruebas y documentación.

*Agradecimientos especiales:* [skyecodes](https://github.com/skyecodes) por su ayuda en la implementación del sistema de fotos de perfil, las pruebas y por proporcionar un servidor para alojar el proyecto durante las pruebas en línea. Además, por el apoyo moral, que fue muy necesario.
