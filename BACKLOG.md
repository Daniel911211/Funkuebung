# Backlog – vorgemerkte Änderungen

Diese Datei sammelt **vorgemerkte** Änderungen, die NICHT sofort einzeln als
eigener Patch umgesetzt werden, sondern jeweils zusammen mit dem nächsten
ohnehin geplanten Patch. Versionsregel beim Umsetzen entsprechend anwenden
(APP_INFO.version bzw. STATION_APP_INFO.version + Kopf-Kommentar synchron).

---

## Offen

### [ ] GPS-Standortprüfung für Stationsoberfläche

**Status:** offen · später fachlich ausarbeiten

- **Ziel:** station.html prüft optional, ob das Handy/Fahrzeug im Bereich der
  richtigen Station ist.
- **Idee:** Button „Standort prüfen" → Browser-GPS lokal abfragen → Entfernung
  zur echten Station → Status (innerhalb/außerhalb Radius, ungenau, verweigert,
  keine Koordinaten).
- **Vorab klären:** GPS nur Warnung oder Pflicht? · Aufgabe/Lagebild/Code
  sperren oder sichtbar lassen? · akzeptable Genauigkeit? · Entfernung anzeigen? ·
  Verhalten bei GPS-Fehlern?
- **Randbedingungen:** keine Standortdaten speichern/übertragen · QR-Link bleibt
  `station.html?s=<echte-station>&fhz=<fahrzeug-id>` · Laufnummer und echte
  Stationsnummer getrennt lassen · erst nach fachlicher Rückfrage umsetzen.

### [ ] Positionscode-Liste für ELW / Übungsleitung — Druck/Export (Restpunkt)

**Status:** teilweise erledigt · Anzeige mit index.html v1.28.0 umgesetzt

- **Erledigt (v1.28.0):** Anzeige der Positionscodes je Fahrzeug/Station in den
  Fahrzeugkarten im Bereich „Lösungssatz" (Laufnummer · Station/Aufgabe ·
  Positionscode · Lösungszeichen).
- **Noch offen:** kompakte **Druck-/Export-Ansicht** der Liste (z. B. im Bereich
  „Druck / Export"), ggf. Kopieren.
- **Vorab klären:** nach Fahrzeug oder Station gruppieren? · mit Lösungszeichen
  drucken? · Kopieren/Export sinnvoll?
- **Randbedingungen:** Positionscode-Format `W<Wortnummer>P<Zeichenposition>` nicht
  ändern · Codes exakt passend zu station.html · Lösungssatz nie im Klartext in
  `data/uebung.json` · nur für ELW/Übungsleitung, nicht für Teilnehmer.

### [ ] ELW-Koordination – Ausbauideen (Folgepunkte)

**Status:** offen · Grundseite `elw.html` erledigt (siehe „Erledigt"), Ausbau später

- **Positionscode erst nach korrekt eingetippter Aufgabe freischalten** —
  *in TEST umgesetzt* (Branch `claude/funny-lovelace-Ljjaw`, nicht live).
  Variante „beide je ein Wort", symmetrischer Funkverkehr, je Fahrzeug × Station:
  FHZ-Rätsel + FHZ-Wort (hoch) und ELW-Rätsel + ELW-Wort (runter); Antwortwörter
  als Verschleierungs-Hash. Pflege im Planungstool unter „Rätsel (Test)". Nach dem
  Test entscheiden: live übernehmen (Merge nach `main`) oder rückbauen. Offene
  Fragen für die Live-Version: Rätsel-Design (nur ortsgebunden lösbar), Umfang der
  Pflege-UI (bis 96 Wörter), Mehrfachversuch/Toleranz.
- **Stations-Übersicht** (je Station: welche Fahrzeuge mit Laufnummer, Adresse,
  Lagebild groß per Lightbox).
- **Link-/QR-Liste je Fahrzeug** direkt vom ELW-Gerät (station.html-Links).
- **Weitere Rollen/Aufgaben**, „damit jeder etwas zu tun hat" (offen für Ideen).
- **Randbedingungen:** kein Lösungssatz/`char` auf `elw.html` · gleiche Optik/Tokens ·
  keine Online-Übertragung · QR-/Stationslogik nicht beschädigen.

---

## Erledigt

### [x] ELW-Koordination als eigene HTML-Seite (`elw.html`)

**Status:** erledigt am 2026-06-07 · `elw.html` v1.0.0 / index.html v1.31.0

Neue lesende Schwesterseite `elw.html` für ELW/Übungsleitung: Übungskopf +
Funkkanäle + je Fahrzeug die Route nach Laufnummer (Einsatzort · Positionscode
verdeckt/„Code anzeigen" · Status „Auftrag erteilt → erledigt", lokal je Gerät).
**Kein Lösungssatz/`char`.** Dafür Export erweitert (`meta.leader`,
`meta.channels`). Ausbauideen → Abschnitt „Offen".

### [x] Stationsplanung: Feld „Kurzbeschreibung / Hinweise" umbenennen

**Status:** erledigt am 2026-06-07 · umgesetzt mit index.html v1.27.1

Label **„Kurzbeschreibung / Hinweise" → „Notiz für die Übungsleitung"** (internes
Feld `description`, nur Label + Platzhalter; Feldname/Logik unverändert).

### [x] Stationsplanung: Begriffe „Aufgabe" / „Aufgabenbeschreibung" + Pflichtfeld

**Status:** erledigt am 2026-06-07 · umgesetzt mit index.html v1.27.0 / station.html v1.0.8

Umsetzung: Tabelle/Detail-Labels umbenannt („Bezeichnung"→„Aufgabe",
„Aufgabe kurz"/„Aufgabe der Station"→„Aufgabenbeschreibung"); „Aufgabe" (intern
`title`) ist Pflichtfeld mit `*`, Inline-Fehlermeldung „Bitte eine Aufgabe
eintragen." und steuert die Vollständigkeit; „Aufgabenbeschreibung" (intern
`task`) optional. station.html zieht mit (Block „Aufgabe" oben, sofern Titel
hinterlegt; bisherige Karte heißt „Aufgabenbeschreibung"). Interne Feldnamen
unverändert (`title`/`task`), bestehende Daten bleiben erhalten.
