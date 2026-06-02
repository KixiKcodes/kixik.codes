---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'webserv'
pubDate: '12.07.2025'
description: 'Ein Webserver in C++ basierend auf NGINX sowie eine Test-Website zum Hosten.'
tags: ["C++", "HTML", "CSS"]
---

## Über das Projekt

Unser Server verwaltet Verbindungen mithilfe eines synchronen I/O-Multiplexing-Systems. Sockets bleiben für jede eingehende Verbindung aktiv, und ein Timeout-System stellt sicher, dass sie geschlossen werden, wenn innerhalb der in der Konfiguration definierten Zeit keine Aktivität erfolgt. Die Konfiguration ist flexibel und erlaubt präzise Standortberechtigungen und Einstellungen. CGI kann ausgeführt und über ein CGI-Skriptverzeichnis konfiguriert werden. Timeout-Zeit und maximale Body-Größe können ebenfalls pro Server gesetzt werden. Zusätzlich können beliebig viele Server in einer einzigen Konfiguration definiert werden.

## Features

- _Socket-Management:_
  Wir verwenden `poll()`, um eingehende Verbindungen effizient zu verwalten. Server können eine beliebige Anzahl gleichzeitiger Anfragen verarbeiten, ohne abzustürzen.

- _HTTP-Methoden:_
  Aktuell implementiert sind `GET`, `POST` und `DELETE`. Diese müssen pro Location im Server-Root über die Konfigurationsdatei explizit aktiviert werden. `GET` sendet angeforderte Daten an den Client zurück. `POST` lädt Daten vom Client in den Upload-Speicher des Servers hoch (ebenfalls in der Konfiguration definiert). `DELETE` entfernt angeforderte Daten vollständig vom Server. Diese Methoden sind selbstverständlich durch Sicherheitsmechanismen abgesichert.

- _Konfigurationsdirektiven:_
  Es gibt viele Konfigurationsdirektiven, mit denen verschiedene Serverfunktionen gesteuert werden können. Für alle fehlenden Direktiven existieren Standardwerte. Eine detaillierte Tabelle wird im nächsten Abschnitt gezeigt.

- _CGI-Skriptausführung:_
  Der Server unterstützt die Ausführung von CGI-Skripten, die über die Direktive `cgi-path` konfiguriert werden können. Diese definiert den Pfad, anhand dessen CGI-Anfragen erkannt werden.

- _Beispielseiten und Konfiguration:_
  Das Projekt enthält eine Standardkonfiguration sowie eine Beispiel-Website mit mehreren HTML-Seiten und CSS-Styling. Es gibt eine Upload-Seite zum Testen von POST-Anfragen sowie eine CGI-Testseite, die Eingaben an ein Beispielprogramm weiterleitet, welches die Daten verarbeitet und als HTML zurückgibt.

- _Ausführliches Logging:_
  Zusätzlich wurde ein robustes Logging- und Debugging-System implementiert, das zur Laufzeit hilfreiche Informationen ausgibt, etwa Request-Daten und Fehler.

## Nutzung

Das Programm kann einfach mit `./webserv` gestartet werden. Dabei wird automatisch die Datei `default.conf` verwendet, sofern vorhanden. Für eine eigene Konfiguration kann es mit `./webserv [Pfad zur Konfigurationsdatei]` gestartet werden.

Der Prozess läuft anschließend unbegrenzt und gibt Logs aus, bis er durch ein Interrupt-Signal mit `CTRL+C` beendet wird.

## Konfiguration

Es können beliebig viele Server-Scopes und Location-Scopes innerhalb eines Servers definiert werden. Keine Direktive ist zwingend erforderlich, da für alle Werte Standardwerte existieren. Wird z. B. kein Index definiert, wird automatisch `index.html` verwendet. Falls diese Datei nicht existiert, wird eine 404-Antwort zurückgegeben.

Jeder Scope wird mit `{ }` geöffnet und geschlossen, jede Direktive endet mit `;`.

Hier sind alle verfügbaren Direktiven und Scopes im Konfigurationssystem:

<table border="1" cellpadding="6" cellspacing="0">
  <thead>
    <tr>
      <th>Schlüsselwort</th>
      <th>Typ</th>
      <th>Felder</th>
      <th>Beschreibung</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>server</td>
      <td>Scope</td>
      <td>Servername</td>
      <td>Definiert einen Server, der beim Start ausgeführt wird.</td>
    </tr>
    <tr>
      <td>error_page</td>
      <td>Direktive</td>
      <td>Statuscode, Seitenpfad</td>
      <td>Setzt die Antwortseite für einen bestimmten HTTP-Statuscode.</td>
    </tr>
    <tr>
      <td>location</td>
      <td>Scope</td>
      <td>Pfad (relativ zum Server-Root)</td>
      <td>Definiert eine Location innerhalb des Servers. Muss innerhalb eines Server-Scopes liegen.</td>
    </tr>
    <tr>
      <td>listen</td>
      <td>Direktive</td>
      <td>Portnummer</td>
      <td>Legt den Port fest, auf dem der Server lauscht.</td>
    </tr>
    <tr>
      <td>host</td>
      <td>Direktive</td>
      <td>IP-Adresse</td>
      <td>Legt die IP-Adresse fest, auf der der Server läuft.</td>
    </tr>
    <tr>
      <td>root</td>
      <td>Direktive</td>
      <td>Pfad</td>
      <td>Definiert das Root-Verzeichnis des Servers.</td>
    </tr>
    <tr>
      <td>index</td>
      <td>Direktive</td>
      <td>Dateipfad/Dateiname</td>
      <td>Definiert die Standarddatei für einfache Serveranfragen.</td>
    </tr>
    <tr>
      <td>timeout</td>
      <td>Direktive</td>
      <td>Zeit (Sekunden)</td>
      <td>Maximale Zeit, die eine Socket-Verbindung ohne Aktivität offen bleibt.</td>
    </tr>
    <tr>
      <td>cgi</td>
      <td>Direktive</td>
      <td>Dateiendungen</td>
      <td>Definiert Dateitypen, die als CGI-Skripte behandelt werden.</td>
    </tr>
    <tr>
      <td>max_body</td>
      <td>Direktive</td>
      <td>Größe (Bytes)</td>
      <td>Setzt die maximale Body-Größe für <code>POST</code>-Anfragen pro Location.</td>
    </tr>
    <tr>
      <td>methods</td>
      <td>Direktive</td>
      <td>Methodennamen (durch Leerzeichen getrennt)</td>
      <td>Definiert erlaubte HTTP-Methoden für eine Location.</td>
    </tr>
    <tr>
      <td>return</td>
      <td>Direktive</td>
      <td>Pfad-Aliase</td>
      <td>Definiert Weiterleitungen (Aliases) zu der Location, in der die Direktive gesetzt ist.</td>
    </tr>
  </tbody>
</table>

Beispielkonfiguration:
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
