# Backlog – vorgemerkte Änderungen

Diese Datei sammelt **vorgemerkte** Änderungen, die NICHT sofort einzeln als
eigener Patch umgesetzt werden, sondern jeweils zusammen mit dem nächsten
ohnehin geplanten Patch. Versionsregel beim Umsetzen entsprechend anwenden
(APP_INFO.version bzw. STATION_APP_INFO.version + Kopf-Kommentar synchron).

---

## Offen

### [ ] Stations-Detail: Pflichtfeld „ELW zeigt Positionscode direkt" entfernen

**Status:** offen · vorgemerkt am 2026-06-09 · **noch NICHT umsetzen** (Nutzer: „merken, warten")

- **Ziel:** Das Auswahlfeld **„ELW zeigt Positionscode direkt"** (ja/nein) im
  Stations-Detail (Rätsel-Editor, `detElwDirect`) vorerst **aus der UI entfernen**.
- **Vor dem Umsetzen klären:**
  - Soll das Datenfeld `stations[].elwCodeDirect` ganz raus oder nur das
    Eingabefeld verschwinden und `elwCodeDirect` intern fest auf einen Wert
    (z. B. `false` = erst nach Klick „Code anzeigen")?
  - Verhalten in `elw.html`: ohne das Feld immer „Code anzeigen"-Button (kein
    direkter Code) – ist das gewünscht?
  - Validierung/Pflichtfeld-Logik (`detElwDirect` ist aktuell Pflicht) mit anpassen.
- **Randbedingungen:** kein Lösungssatz/`char` auf `elw.html` · Export
  rückwärtskompatibel halten (Feld optional) · Versions-Bump erst beim
  tatsächlichen Umsetzen.

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

- **Stations-Übersicht** (je Station: welche Fahrzeuge mit Laufnummer, Adresse,
  Lagebild groß per Lightbox).
- **Link-/QR-Liste je Fahrzeug** direkt vom ELW-Gerät (station.html-Links).
- **Weitere Rollen/Aufgaben**, „damit jeder etwas zu tun hat" (offen für Ideen).
- **Randbedingungen:** kein Lösungssatz/`char` auf `elw.html` · gleiche Optik/Tokens ·
  keine Online-Übertragung · QR-/Stationslogik nicht beschädigen.

---

## In Test (nicht live)

### [~] Funkaufträge (Funkwort-Weitergabe zwischen Beteiligten)

**Status:** in Test · index v1.35.0 / station v1.0.17 / elw v1.3.0 · Branch
`claude/funny-lovelace-Ljjaw` (Draft-PR, **nicht** gemergt → nicht live).

- Neuer Bereich „Funkaufträge": ein **Funkwort**, das **Von** (FHZ + Station) **An**
  (anderes FHZ + Station **oder** ELW) per Funk durchgegeben wird. Empfänger gibt es an
  seiner Station ein → **Positionscode** frei (echter Funkverkehr erzwungen).
- Export: Sender-Zelle `relaySend` (Klartext), Empfänger `relayRecv`/`relayElw` (nur
  Hash). station.html: Sender-Karte + Empfänger-Gate. elw.html: ELW-Empfänger-Gate
  (Code erst nach Eingabe des hochgefunkten Worts).
- **Bewusste Aufweichung:** das Funkwort steht im Klartext beim Sender in der Datei
  (muss er lesen) – Obfuscation-Niveau wie der Base64-Payload, der Mechanik inhärent.
- **Offen vor Live:** Praxistest des Gesamtablaufs; danach über Live-Gang entscheiden.

### [~] Aufgabentyp Multiple-Choice + Mastercode + Detail-Überarbeitung

**Status:** in Test · index v1.34.0 / station v1.0.16 · Branch
`claude/funny-lovelace-Ljjaw` (Draft-PR, **nicht** gemergt → nicht live).

- **Multiple-Choice-Fragen** als dritter Aufgabentyp je Station (mehrere Fragen,
  Antwortmöglichkeiten + richtige Option). station.html: Fragen-Gate, alle richtig →
  Positionscode frei. Export `stations[].mc` (Optionen Klartext, richtige Option nur
  als Hash).
- **Mastercode der Übungsleitung** (Grunddaten): Override, Export `meta.masterCode`
  (nur Hash). station.html: dezenter Button „Übungsleitung" blendet das Eingabefeld
  ein (sonst verborgen) → korrekter Code zeigt das Lösungszeichen direkt.
- **Stations-Detail** in aufklappbare Abschnitte gegliedert, Hilfetexte gekürzt.
  Mit v1.35.1/v1.35.2 weiter überarbeitet: Karten „Aufgabe" + „Aufgabentyp &
  Freischaltung" zusammengeführt, eigene Karte „Notiz für die Übungsleitung",
  Pflichtfeld heißt **„Stationsüberschrift"**, Aufgabenbeschreibung nur bei
  Aufgabentyp „Text" sichtbar und nur dann exportiert.
- Platzhalter TMO-Funkkanal „z. B. E18" → „z. B. EG18" (erledigt).
- **Offen vor Live:** Annahmen prüfen (MC schaltet Code frei; Mastercode = voller
  Override bis Lösungszeichen); nach Test über Live-Gang entscheiden.

---

## Erledigt

### [x] Rätsel-Freischaltung (ELW↔FHZ-Funkverkehr) – live

**Status:** erledigt am 2026-06-07 · index v1.33.0 / station v1.0.15 / elw v1.2.0
(Branch `claude/funny-lovelace-Ljjaw` → `main`).

Freischalt-Rätsel sind jetzt reguläres Live-Feature, gepflegt **je Station** im
Stations-Detail (Aufgabentyp Text/Rätsel, Geltungsbereich alle/ausgewählte Fahrzeuge).
Je Seite FHZ-Rätsel (**Antwort der Besatzung**) und ELW-Rätsel (**Rückwort des ELW**),
**Freitext oder Multiple-Choice**. Gate-Logik stationsseitig (station.html prüft beide
Wörter); elw.html zeigt das ELW-Rätsel als Overlay und den Code direkt/per Klick
(`elwCodeDirect`). Zusätzlich „Lagebild ausblenden" je Station und Funkkanäle in der
QR-Druck-Kopfzeile. Antwortwörter nur als Verschleierungs-Hash exportiert.
Offene Ausbauideen (nicht umgesetzt): Mehrfachversuch/Toleranz-Feinheiten,
zeitlich begrenzte Codes.

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
