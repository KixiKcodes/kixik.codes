---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'webserv'
pubDate: '12.07.2025'
description: 'Un servidor web básico en C++ basado en NGINX junto con una pagina web de prueba para alojarla.'
source: 'https://github.com/KixiKcodes/webserv'
tags: ["C++", "HTML", "CSS"]
---

## Acerca del proyecto

Nuestro servidor gestiona conexiones mediante un sistema síncrono de multiplexación de I/O. Los sockets se mantienen abiertos para cada conexión entrante, y un sistema de timeouts asegura que se cierren si no hay actividad durante el tiempo definido en la configuración.

La configuración es flexible, permitiendo permisos y ajustes precisos por ubicación. También se puede ejecutar CGI configurando un directorio de scripts. El tiempo de espera y el tamaño máximo del cuerpo de las peticiones pueden definirse por servidor. Además, es posible definir múltiples servidores dentro de un mismo archivo de configuración.

## Funcionalidades

- _Gestión de sockets:_  
  Utilizamos `poll()` para gestionar eficientemente las conexiones entrantes. El servidor puede manejar cualquier cantidad de solicitudes simultáneas sin fallos.

- _Métodos HTTP:_  
  Actualmente se soportan `GET`, `POST` y `DELETE`. Deben habilitarse explícitamente por ubicación en el archivo de configuración.  
  `GET` devuelve recursos al cliente.  
  `POST` sube datos al servidor en el directorio de uploads configurado.  
  `DELETE` elimina recursos del servidor.  
  Estos métodos incluyen medidas de seguridad.

- _Directivas de configuración:_  
  Existen múltiples directivas para configurar el comportamiento del servidor. Todas tienen valores por defecto en caso de no ser especificadas.

- _Ejecución de CGI:_  
  El servidor puede ejecutar scripts CGI mediante la directiva `cgi-path`, que define qué rutas se consideran scripts CGI.

- _Páginas de ejemplo y configuración:_  
  El proyecto incluye una configuración por defecto y un sitio web de ejemplo con varias páginas HTML y estilos CSS. Incluye una página de subida para probar `POST` y una página de prueba CGI.

- _Logging detallado:_  
  Sistema de logs y depuración que muestra información útil en tiempo de ejecución, como solicitudes y errores.

## Uso

El programa se puede ejecutar con:

```bash
./webserv
```

Si existe, utilizará el archivo `default.conf`. También se puede especificar un archivo de configuración personalizado:

```bash
./webserv [ruta del archivo de configuración]
```

El proceso se ejecuta indefinidamente y muestra logs hasta ser detenido con `CTRL + C`.

## Configuración

Puede haber múltiples bloques `server` y `location`. Ninguna directiva es estrictamente obligatoria, ya que existen valores por defecto.

Por ejemplo, si no se define `index`, se usará `index.html`. Si no existe, se devolverá un error 404.

Cada bloque se define con `{ }` y cada directiva termina en `;`.

Directivas disponibles:

| Keyword    | Tipo      | Campos                  | Descripción                                          |
| ---------- | --------- | ----------------------- | ---------------------------------------------------- |
| server     | scope     | nombre del servidor     | Define un servidor a ejecutar.                       |
| error_page | directive | código de estado, ruta  | Define la página de error para un código específico. |
| location   | scope     | ruta (relativa al root) | Define una ruta dentro del servidor.                 |
| listen     | directive | número de puerto        | Define el puerto de escucha.                         |
| host       | directive | dirección IP            | Define la IP del servidor.                           |
| root       | directive | ruta                    | Define el directorio raíz.                           |
| index      | directive | archivo                 | Define el archivo por defecto.                       |
| timeout    | directive | segundos                | Tiempo máximo de conexión inactiva.                  |
| cgi        | directive | extensiones de archivo  | Define extensiones tratadas como CGI.                |
| max_body   | directive | tamaño en bytes         | Tamaño máximo de cuerpo en POST.                     |
| methods    | directive | métodos HTTP            | Métodos permitidos.                                  |
| return     | directive | alias de rutas          | Redirecciones hacia otra ubicación.                  |

Ejemplo de configuración:

```conf
server {
	listen 8080;
	host 127.0.0.1;
	root www/;
	index index.html;
	timeout 10;

	location / {
		methods GET;
	}

	location scripts/ {
		index ../pages/scripts.html;
		methods GET POST;
		cgi .sh .py .php;
		return cgi-bin/;
	}

	location uploads/ {
		index ../pages/upload.html;
		methods GET POST DELETE;
		max_body 8000000;
	}
}
```
