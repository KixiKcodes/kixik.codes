---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'Grimoire'
pubDate: '02.05.2026'
description: 'Eine Blood on the Clocktower-Game-Management-App für Android.'
tags: ["Kotlin", "Android"]
---

## Über das Projekt

Um Halloween 2025 bin ich durch einige Bekannte auf das Spiel *Blood on the Clocktower* gestoßen. Ich habe mir einige Videos dazu angesehen und war schnell begeistert. *Blood on the Clocktower* ist ein Social-Deduction-Spiel ähnlich wie Werewolf oder Mafia, das hauptsächlich in sozialen Gruppen gespielt wird.

Das Spiel basiert auf sogenannten "Scripts", also Zusammenstellungen von Charakteren und Regeln, die von den Entwicklern oder der Community erstellt werden. Diese Scripts sind so aufgebaut, dass die Rollen gut miteinander funktionieren. Eine Lücke, die ich dabei gesehen habe, liegt in der Spielvorbereitung. *BotC* wird mit einem Spielleiter ("Storyteller") gespielt, der für die Zuteilung der Rollen sowie für die Verwaltung aller Informationen im Spielverlauf verantwortlich ist. Wie man sich vorstellen kann, trägt diese Person viel Verantwortung, das kann ich auch aus eigener Erfahrung bestätigen.

Man sagt nicht umsonst: Not macht erfinderisch.

Ich habe das Projekt zunächst in C# als CLI-Anwendung begonnen und später nach Kotlin als Android-App portiert. Dabei habe ich offizielle Assets aus dem BotC-Wiki verwendet und die UI mit Jetpack Compose umgesetzt.

Aktuell erlaubt die App die Generierung einer vollständigen Rollenzuteilung für eine beliebige Spieleranzahl basierend auf einem ausgewählten Script. Die Scripts sind in die App integriert, bestehen aber lediglich aus JSON-Dateien, die ich parse. Geplant ist, dass Nutzer in Zukunft eigene Scripts importieren können.

<div style="display: flex; gap: 1rem; align-items: flex-start;">
	<img src="/images/script_menu.png" alt="assignments1" style="width: calc(50% - 0.5rem); aspect-ratio: 450 / 919; object-fit: cover;" />
	<img src="/images/grimiore.gif" alt="assignments2" style="width: calc(50% - 0.5rem); aspect-ratio: 450 / 919; object-fit: cover;" />
</div>

## Features

- Script-Manager, der Scripts dynamisch parst.
- Script-Vorschau, die alle geladenen Scripts anzeigt.
- Spiel-Setup-Screen mit Script-Auswahl und Spieler-Management.
- Algorithmus zur automatischen Zuweisung von Rollen und Subrollen basierend auf Spielregeln und -dynamik.
- Vorschau-Screen für den Storyteller mit allen zugewiesenen Rollen und relevanten Informationen.
- System zur Berechnung aktiver Rollen-Jinxes (Sonderregeln zur Auflösung von Konflikten zwischen Rollen).

<div style="display: flex; gap: 1rem; align-items: flex-start;">
	<img src="/images/script_assigned1.png" alt="assignments1" style="width: calc(50% - 0.5rem); height: auto;" />
	<img src="/images/script_assigned2.png" alt="assignments2" style="width: calc(50% - 0.5rem); height: auto;" />
</div>

## Zukunftspläne

Ich habe für dieses Projekt einige langfristige Pläne. Ich kann nicht garantieren, dass alles in absehbarer Zeit umgesetzt wird, aber unter anderem sind folgende Ideen vorgesehen:

- Hinzufügen eines Endbildschirms mit allen Spielern, ihren Positionen sowie sichtbaren Rollen/Subrollen.
- Anzeige von Loric sowie Fabled/Jinxes auf diesem Endbildschirm.
- Implementierung eines vollständigen Game-Loops, der es dem Storyteller ermöglicht, den Spielverlauf inklusive aller Informationen zu verwalten.
- Portierung auf PC mittels Kotlin Multiplatform.
- Vollständige Automatisierung des Spiels (Auto-Storyteller).
