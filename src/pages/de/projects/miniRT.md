---
layout: ../../../layouts/MarkdownPostLayout.astro
title: 'miniRT'
pubDate: '09.04.2025'
description: '3D-Raytracer in reinem C, basierend auf MLX42.'
tags: ["C"]
---

![test_scene](/images/test_scene.png)

_Dieses Projekt wurde in Zusammenarbeit mit [Cimex](https://github.com/Cimex404) entwickelt. Er hat es ebenfalls in seinem eigenen Repository veröffentlicht!_

## Über das Projekt

MiniRT ist ein einfacher 3D-Raytracer, der die MLX42-Grafikbibliothek (OpenGL-Fenster) verwendet, um eine Szene basierend auf einer Szenendatei zu rendern. Diese enthält Informationen zu Kamera, Lichtquellen und Objekten.

Das Rendering funktioniert, indem für jedes Pixel ein Strahl aus der Kamera in die 3D-Welt geschickt wird und Schnittpunkte mit Objekten berechnet werden. Die Objekte besitzen Oberflächennormalen, die bestimmen, wie der Strahl in Richtung Lichtquelle reflektiert wird.

Die Szene kann derzeit nur eine Punktlichtquelle enthalten, und alle Strahlen werden auf diese zurückgeführt.

Schatten werden auf zwei Arten berechnet: Shadow Rays und Ambient Occlusion. Shadow Rays werden dort erzeugt, wo der Strahl auf ein Objekt trifft und nicht weiterlaufen kann, wodurch der Schattenwurf simuliert wird. Ambient Occlusion basiert hingegen rein auf der Nähe zwischen sehr dicht beieinanderliegenden Objekten und simuliert, wie Licht in engen Bereichen „gefangen“ wird und in Dunkelheit übergeht.

Beide Verfahren verwenden Sampling und pseudozufällige Streuung zur Simulation der Lichtdiffusion. Je höher die Anzahl der Samples, desto weniger verrauscht wirken die Schatten – allerdings auf Kosten der Rechenzeit.

Zusätzlich wurden einige visuelle Features sowie grundlegende Laufzeit-User-Controls implementiert. Das Speichermanagement erfolgt über ein rudimentäres Garbage-Collection-System, ergänzt durch ein Logging- und Fehlerbehandlungssystem.

Obwohl es sich um ein 42-Projekt handelt, wird die aktuelle und zukünftige Version von der Norm abweichen. Wer eine normkonforme Version sehen möchte, kann im Commit-Verlauf zurückgehen.

## Features

- **Primitive Objekte**
  Aktuell unterstützt das Projekt drei primitive Objekte: Ebenen, Kugeln und Zylinder. Diese besitzen jeweils eigene Parameter, die in der Szenendatei definiert werden.

- **Beleuchtung**
  Die Szene wird durch ein Umgebungslicht und eine Punktlichtquelle beleuchtet. Das Umgebungslicht wirkt als globale Beleuchtung und definiert die Grundfarbe, wenn kein Objekt getroffen wird. Die Punktlichtquelle ist ein einzelner Lichtpunkt mit Falloff.

- **Rauigkeit**
  Ein Rauigkeitswert pro Objekt beeinflusst die Intensität von Glanzlichtern.

- **Reflexionen**
  Jedes Objekt kann reflektierend sein. Die Anzahl der maximalen Reflexionen ist begrenzt (bis zu 100 im High-Preset).

- **Benutzereingaben**

  - `w` – vorwärts
  - `s` – rückwärts
  - `a` – links
  - `d` – rechts
  - `↑` – hoch rotieren
  - `↓` – runter rotieren
  - `←` / `→` – links/rechts rotieren
  - Numpad `+` / `-` – Schatten-Samples anpassen

- **Qualitäts-Presets**
  - Standard: 800×500, 64 Samples, 50 Reflexionen
  - Low: 320×200, 1 Sample, 1 Reflexion
  - High: 1600×1000, 80 Samples, 100 Reflexionen

  Auswahl über:
  ```sh
  make QUALITY=LOW
  make QUALITY=HIGH
  ```
- **Logging-System**
    Alle geparsten Objekte werden protokolliert, inklusive Warnungen und Fehlern.

## Nutzung
Du kannst das Projekt einfach mit `make` kompilieren. Zum Entfernen der Build-Artefakte verwende `make clean` oder `make fclean` für eine vollständige Bereinigung des Executables. Die Qualitätsstufe kann ebenfalls wie zuvor beschrieben festgelegt werden.

Um miniRT mit einer Szene auszuführen, verwende folgenden Befehl:

```bash
./miniRT "[Szenendatei / Pfad zur Szenendatei]"
```

Die Szenendatei muss die Endung `.rt` besitzen und einem bestimmten Format folgen:

- Die Szene _muss_ genau ein Umgebungslicht (`A`), eine Kamera (`C`) und eine Punktlichtquelle (`L`) enthalten.
- Die Parameter des **Umgebungslichts** sind `intensity` als Float von 0 bis 1 sowie `color` im RGB-Format mit drei Werten von 0 bis 255. _Farben funktionieren für alle Assets auf diese Weise._
- Die **Kamera**-Parameter sind `position` x,y,z, `rotation` x,y,z (Werte von -1 bis 1) sowie `focal length` bzw. FOV (1 bis 180).
- Die **Punktlichtquelle** besitzt `position` x,y,z sowie `intensity` und `color` im gleichen Format wie das Umgebungslicht.
- Die Szene kann beliebig viele Objekte aus **Ebenen** (`pl`), **Kugeln** (`sp`) und **Zylindern** (`cy`) enthalten.
- Ebenen sind unendlich groß und besitzen eine Normale, die durch ihren Rotationsvektor definiert wird. Sie haben `position` x,y,z, `rotation` x,y,z und `color`.
- Kugeln besitzen wie alle Objekte eine `position`, jedoch keine Rotation (hat keinen Effekt auf eine Kugel), außerdem `diameter` als Float und `color`.
- Zylinder besitzen `position` und `rotation` im üblichen Format sowie `diameter`, `height` und `color`.
- Falls Parameter fehlen, in falscher Reihenfolge angegeben sind oder ungültig sind, wird das Objekt ignoriert und nicht gerendert. Falls die Pflichtobjekte (Umgebungslicht, Kamera und Punktlicht) fehlen oder ungültig sind, wird das Fenster nicht gestartet.
- Für alle Objekte können zusätzlich `roughness` und `reflectivity` als Float-Werte von 0 bis 1 in dieser Reihenfolge angegeben werden. Falls sie nicht gesetzt sind, werden Standardwerte verwendet.
- Parameter können beliebig durch Leerzeichen getrennt werden; auch leere Zeilen zwischen Assets sind erlaubt. Nicht erkannte Assets werden ignoriert.
- Kommentare können hinzugefügt werden, indem die Zeile mit `#` beginnt.

Beispiel:

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

## Render-Galerie

![alien_planet](/images/alien_planet.png)
![mirror_room](/images/mirror_room.png)
![underwater_temple](/images/underwater_temple.png)
![water_molecule](/images/water_molecule.png)
![eval_scene_7](/images/eval_scene_7.png)
![octagon](/images/octagon.png)

## Zukunftspläne

Dieses Projekt gehört zu meinen Favoriten im 42-Core-Curriculum. Geplant ist die Erweiterung um:

* Performance-Verbesserungen (Multithreading, GPU, BVH)
* Bloom-Effekt für die Punktlichtquelle
* besseres Handling großer Szenen
* kubisches Objekt
* Unterstützung des OBJ-Formats
* Lichtbrechung und Kaustiken
* Bump Mapping und Texturen
* Bearbeitung von Assets zur Laufzeit
