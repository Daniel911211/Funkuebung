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

### [ ] ELW-Koordination als eigene HTML-Seite

**Status:** offen · größerer späterer Ausbau

- **Ziel:** eigene Seite für ELW/Übungs-/Spielleitung.
- **Dateiname:** `elw.html` oder `elw-koordination.html` (vorab mit Nutzer
  abstimmen).
- **Inhalte (möglich):** Übersicht Fahrzeuge/Stationen · Routen/nächste Stationen ·
  Status je Fahrzeug/Station · Positionscodes · Lösungssatz-/Zeichenstatus ·
  optional Notizen/Rückmeldungen · optional Druck-/Auswertungsansicht.
- **Vorab klären:** nur Anzeige oder aktive Statuspflege? · lokale Speicherung? ·
  Positionscode-Liste integrieren? · GPS-Status dort anzeigen? · Datenbasis?
- **Randbedingungen:** gleiche dunkle Leitstellen-Optik + Design-Tokens (CLAUDE.md) ·
  keine Serverpflicht · keine Online-Übertragung ohne Entscheidung · QR-/Stations-
  logik nicht beschädigen · `data/uebung.json` nicht ohne fachliche Entscheidung
  erweitern.

---

## Erledigt

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
