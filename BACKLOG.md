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

- **Stations-Übersicht** (je Station: welche Fahrzeuge mit Laufnummer, Adresse,
  Lagebild groß per Lightbox).
- **Link-/QR-Liste je Fahrzeug** direkt vom ELW-Gerät (station.html-Links).
- **Weitere Rollen/Aufgaben**, „damit jeder etwas zu tun hat" (offen für Ideen).
- **Randbedingungen:** kein Lösungssatz/`char` auf `elw.html` · gleiche Optik/Tokens ·
  keine Online-Übertragung · QR-/Stationslogik nicht beschädigen.

---

## Erledigt

### [x] Stations-Detail: Pflichtfeld „ELW zeigt Positionscode direkt" entfernen

**Status:** erledigt mit index v1.37.0 (vorgemerkt am 2026-06-09, Umsetzung vom
Nutzer freigegeben) · Branch `claude/funny-lovelace-Ljjaw`.

- **Zuschnitt:** Das Auswahlfeld (`detElwDirect` inkl. Fehlermeldung/Handler und
  der `hasRiddle`-Einblendung) ist ersatzlos aus dem Stations-Detail entfernt;
  die Pflicht-Bedingung in `isStationComplete` ist gestrichen.
- **Antworten auf die damaligen Klärungsfragen:**
  - *Datenfeld ganz raus oder nur UI?* → Nur das Eingabefeld verschwindet;
    `stations[].elwCodeDirect` bleibt intern und im Export erhalten, fest
    `false` (= Code erst nach Klick „Code anzeigen"). `normalizeStation`
    vereinheitlicht dabei auch früher gespeicherte `true`-Werte auf `false`
    (bewusster Nutzer-Entscheid, im CHANGELOG vermerkt).
  - *Verhalten in `elw.html`?* → Unverändert (kein Code-Patch dort): Code
    erscheint erst nach Klick „Code anzeigen" bzw. nach Funkwort-Eingabe;
    Alt-Exporte mit `true` funktionieren weiter.
  - *Validierung/Pflichtfeld-Logik?* → Komplett entfernt (Feld, Fehlermeldung
    `detElwDirectErr`, `syncDirectError`, Vollständigkeits-Bedingung).
- **Randbedingungen eingehalten:** kein Lösungssatz/`char` auf `elw.html` ·
  Export rückwärtskompatibel (Feld wird weiter geschrieben, konstant `false`).

### [x] Mehrere Aufgaben-Blöcke je Station (fahrzeugabhängige Aufgaben) – live

**Status:** erledigt/live am 2026-07-18 · index v1.36.0 / station v1.1.0
(elw unverändert) · Branch `claude/funny-lovelace-Ljjaw` → `main` (PR #87).

- Kartenbereich „Aufgabe" = **1..n Aufgaben-Blöcke** (`station.tasks[]`): Block 1
  mit Geltungsbereich alle/ausgewählte Fahrzeuge; bei „ausgewählte" verteilen
  weitere Blöcke („+ Weitere Aufgabe hinzufügen") die restlichen Fahrzeuge (Chips
  bieten nur unbeanspruchte Fahrzeuge an). Jeder Block eigener Aufgabentyp
  Text/Rätsel/MC (Nutzerentscheid); unzugeteilte aktive Fahrzeuge → Station
  „in Bearbeitung" + Hinweis (Nutzerentscheid), Export bleibt möglich.
- Export: Rätsel wie bisher je Zelle; NEU `cell.task`/`cell.mc` bei „ausgewählt";
  „alle"-Fall weiter über `stations[].task`/`mc` (alte Leser kompatibel,
  Ein-Block-Altdaten byte-identisch). station.html: Zelle vor Station (Fallback).
- **Vermerk:** `elwCodeDirect` bewusst **stationsglobal** geblieben (das Feld ist
  inzwischen mit index v1.37.0 aus der UI entfernt, siehe eigenen Erledigt-Punkt).
- **Offene Idee:** Vollständigkeit könnte zusätzlich verlangen, dass ein MC-Block
  ≥ 1 wohlgeformte Frage bzw. ein Rätsel-Block ≥ 1 Rätselseite hat (heute wie
  bisher ungeprüft; leere Inhalte fallen beim Export einfach weg).

### [x] Funkaufträge (Funkwort-Weitergabe zwischen Beteiligten) – live

**Status:** erledigt/live am 2026-07-18 · index v1.35.0 / station v1.0.17 /
elw v1.3.0 · Branch `claude/funny-lovelace-Ljjaw` → `main` (PR #87).

- Neuer Bereich „Funkaufträge": ein **Funkwort**, das **Von** (FHZ + Station) **An**
  (anderes FHZ + Station **oder** ELW) per Funk durchgegeben wird. Empfänger gibt es an
  seiner Station ein → **Positionscode** frei (echter Funkverkehr erzwungen).
- Export: Sender-Zelle `relaySend` (Klartext), Empfänger `relayRecv`/`relayElw` (nur
  Hash). station.html: Sender-Karte + Empfänger-Gate. elw.html: ELW-Empfänger-Gate
  (Code erst nach Eingabe des hochgefunkten Worts).
- **Bewusste Aufweichung:** das Funkwort steht im Klartext beim Sender in der Datei
  (muss er lesen) – Obfuscation-Niveau wie der Base64-Payload, der Mechanik inhärent.

### [x] Aufgabentyp Multiple-Choice + Mastercode + Detail-Überarbeitung – live

**Status:** erledigt/live am 2026-07-18 · index v1.34.0 / station v1.0.16 ·
Branch `claude/funny-lovelace-Ljjaw` → `main` (PR #87). Damit auch live:
TMO-Platzhalter „z. B. E18" → „z. B. EG18" (Vormerkung vom 2026-06-09 erledigt).

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
