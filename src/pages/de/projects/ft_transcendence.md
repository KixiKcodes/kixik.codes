---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'ft_transcendence'
pubDate: '06.11.2025'
description: 'Abschlussprojekt des 42 Core Curriculums. Full-Stack-Prototyp einer Webanwendung rund um das klassische Pong.'
tags: ["TypeScript", "JavaScript", "HTML", "CSS", "Docker"]
---

In Zusammenarbeit mit meinen Kommilitonen [Jacob](https://github.com/Cimex404), [Tobias](https://github.com/tmkeil) und [Betül](https://github.com/Bebuber).

## Über das Projekt
Dies ist das Abschlussprojekt des 42 Common Core. Ziel ist es, alle unsere Fähigkeiten zu testen, indem wir eine Full-Stack-Webanwendung mit zahlreichen Features entwickeln. Die Grundidee besteht darin, eine eigene Version von Pong mit modernen Erweiterungen zu erstellen. Das Projekt besteht aus einem verpflichtenden Teil sowie optionalen Major- und Minor-Modulen, die zur Endbewertung beitragen.

![Main Menu](/images/transcendence_menu.png)

## Basisfunktionen
Die grundlegenden Anforderungen von Transcendence sind ein lokales Pong-Spiel zwischen zwei Spielern sowie ein rundenbasiertes Turniersystem für mehrere Spieler. Das Gameplay soll dem Original von 1972 möglichst treu bleiben. Die genaue Umsetzung des Turniersystems war uns freigestellt, daher haben wir hier eigene Entscheidungen getroffen. Die Website muss sicher sein und gegen SQL-Injections sowie bösartige API-Aufrufe geschützt werden. Ich habe mich hauptsächlich auf diesen Bereich konzentriert und den Login-/Registrierungsprozess, das 2FA-System, JWT- und Cookie-Management sowie API-Schutz mittels Authorization Pre-Handlern implementiert.

## Module
Wir haben 9 Major-Module und 4 Minor-Module gewählt. Ein Minor-Modul zählt dabei halb so viel wie ein Major-Modul, insgesamt ergeben sich somit 11 Modulpunkte. Für 100 % Bewertung sind 7 Punkte erforderlich, wodurch wir den maximalen Bonus von 125 % erreichen.

### Major-Module:
- **Backend-Framework (Fastify)** – Unsere Backend-Routen und Endpunkte basieren auf Fastify für JavaScript.
- **Remote-Authentifizierung** – Unterstützung für Google Authenticator als Drittanbieter-Authentifizierung.
- **Remote-Spieler** – Das Spiel kann online mit entfernten Spielern über WebSockets gespielt werden.
- **Live-Chat** – Ein Live-Chat-System, in dem Spieler Nachrichten, Spieleinladungen und Statistiken austauschen können.
- **User-Dashboard und Statistiken** – Ein dynamisches Dashboard zur Anzeige von Statistiken, Freundschaftsanfragen, Profilbildern und einem Blockierungssystem.
- **KI-Gegner** – Ein zusätzlicher Modus, in dem Spieler unbegrenzt gegen eine KI mit drei Schwierigkeitsstufen antreten können.
- **Zwei-Faktor-Authentifizierung und JWTs** – Sitzungen werden über Cookies gespeichert und mittels JSON Web Tokens verifiziert. Nutzer können 2FA aktivieren und einen QR-Code scannen, um Google Authenticator zu verbinden.
- **3D-Grafik (Babylon)** – Das Spiel wird in 3D mit der Grafikbibliothek Babylon.js gerendert (daher auch der Teamname).
- **Serverseitige Pong-API** – In Kombination mit dem Remote-Spieler-System werden Spielzustände wie Schlägerpositionen und Ballgeschwindigkeit serverseitig berechnet und über eine eigene API an die Clients übertragen.

### Minor-Module:
- **Frontend-Framework (Tailwind und Vite)** – Das Frontend nutzt Tailwind CSS für UI-Design sowie Vite für schnelle Reloads.
- **Datenbank-Implementierung (SQLite)** – Speicherung von Nutzerdaten, Freundes-/Blocklisten sowie Spieldaten.
- **DevOps-Monitoring-System (Prometheus, Grafana und Alertmanager)** – Drei zusätzliche Container sammeln und visualisieren Metriken. Zusätzlich gibt es ein Alert-System, das mit einem Slack-Channel verbunden ist.
- **Mehrbrowser-Kompatibilität** – Transcendence funktioniert in mehreren Browsern (Firefox ist jedoch der native Browser).

![Login or Register](/images/transcendence_login.png)

![Gameplay GIF](/images/gameplay.gif)

## Nutzung
- Repository klonen:
```sh
git clone https://github.com/KixiKCodes/ft_transcendence
```

- Im Root-Verzeichnis muss eine .env-Datei mit folgenden Variablen erstellt werden:
```conf
JWT_SECRET=[JSON Web Token Secret Key]

GF_SECURITY_ADMIN_USER=[Grafana Admin Benutzername]
GF_SECURITY_ADMIN_PASSWORD=[Grafana Admin Passwort]
GF_AUTH_ANONYMOUS_ENABLED=false
GF_USERS_ALLOW_SIGN_UP=true

SLACK_WEBHOOK_URL=[Slack Webhook URL für Alertmanager]
```

- Anschließend kann alles mit dem Makefile gebaut werden: `make build` (`make help` zeigt weitere verfügbare Befehle)

- Danach kann die Anwendung unter `https://localhost:8443/` geöffnet werden. Die Warnung wegen des selbstsignierten SSL-Zertifikats kann ignoriert werden.

- Das System ist damit einsatzbereit.

## Credits

**Jacob Graf** – Game Design, Game Logic, 3D-Grafik, Nutzerstatistiken, allgemeine Architektur, Implementierung aller Spielmodi, Playtesting.

**Tobias Keil** – Routing und WebSockets, Networking und Remote-Spieler-System, Datenbank- und Backend-Setup, Architektur, Live-Chat und Dashboard, Freundes- und Blockierungssystem, Playtesting.

**Betül Büber** – DevOps und Monitoring-Systeme, Playtesting.

**Ich selbst** – Cybersecurity, Registrierungsflow, Authentifizierungs- und Autorisierungssysteme, Nutzereinstellungen, Frontend- und UI-Design, Projektmanagement, Sounddesign und Musik, Playtesting, Dokumentation.

_Besonderer Dank: [skyecodes](https://github.com/skyecodes) für Unterstützung beim Profilbild-System, Playtesting sowie die Bereitstellung eines Servers für Online-Tests. Außerdem für wichtige moralische Unterstützung._
