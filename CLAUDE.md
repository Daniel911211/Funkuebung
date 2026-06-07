# Arbeitsanweisung – Funkübung-Planungstool

Projekt: statische GitHub-Pages-App (kein Build-Schritt). Alles inline in
`index.html` (Planungstool) und `station.html` (Teilnehmer-/QR-Ansicht);
Übungsdaten in `data/uebung.json` (Base64-verpackt). Live ausgeliefert wird
ausschließlich der Stand auf dem Branch `main`.

## Backlog / Vormerkungen (WICHTIG)

- **Kleinere Änderungen werden nicht sofort einzeln als eigener Patch
  umgesetzt, sondern in `BACKLOG.md` vorgemerkt** und zusammen mit dem nächsten
  ohnehin geplanten Patch umgesetzt — außer der Nutzer fordert die sofortige
  Umsetzung ausdrücklich an.
- Wenn der Nutzer etwas „vormerken" / „für später" / „mit dem nächsten Patch"
  sagt: Eintrag in `BACKLOG.md` ergänzen (Status, Datum, Beschreibung,
  Randbedingungen), **nicht** implementieren.
- Vor jedem regulären Patch zuerst `BACKLOG.md` lesen und passende offene
  Punkte mit einplanen. Umgesetzte Punkte in `BACKLOG.md` abhaken/entfernen.

## Patch-Workflow

1. Änderung in `index.html` / `station.html` umsetzen.
2. **Version bumpen** und synchron halten:
   - `index.html`: `APP_INFO.version` + Kopf-Kommentar (Versionszeile + neuer
     „Stand x.y.z"-Eintrag).
   - `station.html`: `STATION_APP_INFO.version` + Kopf-Kommentar.
   - Reine Doku/Backlog-Änderungen ohne App-Code → kein Versions-Bump.
3. `node --check` über den extrahierten `<script>`-Block (der echte App-Block
   in `index.html`, nicht Beispieltext in Kommentaren).
4. Commit → Push (`-u origin <branch>`, bei Netzfehlern Retry mit Backoff) →
   Draft-PR gegen `main` → mergen (= live) → von der PR-Subscription
   abmelden.
5. `data/uebung.json` ist Nutzer-Live-Daten: **nie** mit einem veralteten
   lokalen Stand überschreiben. Vor dem Commit ggf. aus `origin/main`
   wiederherstellen, sodass nur die beabsichtigten Code-Dateien im Commit sind.

## Branch- und Live-Stand prüfen (vor jedem Patch)

Vor jedem Patch zuerst klären:
- Welcher Branch ist der aktuelle **Default-Branch**?
- Welcher Branch wird von **GitHub Pages** live ausgeliefert?
- Gegen welchen Branch muss der **Pull Request** laufen?

Nicht blind annehmen, dass `main` live ist. Wenn `CLAUDE.md`, GitHub-Default-Branch
und GitHub-Pages-Branch **widersprüchlich** sind, vor der Umsetzung **Rückfrage**
stellen — keine Annahmen treffen. (Stand 2026-06-07: Default-Branch = `main`,
Pages liefert aus `main`.)

## Vor jeder Änderung lesen/prüfen

- `CLAUDE.md`
- `BACKLOG.md`
- aktueller Stand von `index.html`
- aktueller Stand von `station.html`, falls betroffen
- `data/uebung.json` nur **prüfen**, nicht überschreiben

## data/uebung.json besonders schützen

`data/uebung.json` enthält **Live-Übungsdaten des Nutzers**.
- Nicht automatisch aus lokalem Altbestand überschreiben.
- Nicht unnötig neu generieren.
- Nicht in Code-Patches verändern, außer der Nutzer fordert es ausdrücklich.
- Vor **jedem** Commit prüfen, ob `data/uebung.json` versehentlich geändert wurde.
- Falls versehentlich geändert: Änderung zurücknehmen (aus `origin/main`
  wiederherstellen), sodass nur die beabsichtigten Code-Dateien im Commit sind.

## BACKLOG.md – Pflege

Umgesetzter Punkt:
- abhaken oder in einen Bereich „Erledigt" verschieben,
- kurz notieren, mit welcher Version / welchem Patch erledigt,
- **nicht** einfach löschen, solange kein Änderungsverlauf vorhanden ist.

Neu vorgemerkter Punkt: Status `offen`, Datum, kurze Beschreibung,
Randbedingungen / „Nicht ändern", Priorität (falls erkennbar).

## Keine Beispiel- oder Testdaten in Live-Dateien

Keine Beispielbilder, Beispieladressen, Demo-Texte oder Platzhalter einbauen,
insbesondere nicht: `picsum.photos`, „Musterstraße", „Testaufgabe",
„Lorem ipsum", Claude-Beispieldaten. Wenn echte Daten fehlen: **klare
Fallback-Meldung** anzeigen, keine Beispielwerte eintragen.

## UI-Begriffe konsistent halten

Bei sichtbaren Textänderungen immer den **gesamten betroffenen Bereich** prüfen.
Projektweit konsistente Begriffe:
- **Aufgabe** = kurzer Titel / kurze Stationsaufgabe (intern aktuell `title`)
- **Aufgabenbeschreibung** = ausführlicher Aufgabentext (intern aktuell `task`)
- **Einsatzort / Adresse** = Ort der echten Station
- **Laufnummer** = sichtbare Teilnehmer-Station innerhalb der Fahrzeugroute
- **echte Stationsnummer** = interne Stationsnummer aus der Planung

Keine widersprüchlichen Altbegriffe stehen lassen (z. B. „Bezeichnung",
„Aufgabe kurz", „Titel" wenn sichtbar „Aufgabe" gemeint ist).

## QR- und Stationslogik schützen

Bei Änderungen an QR-Code, Druckkarten oder `station.html` beachten:
- QR-Link nutzt **echte Stationsnummer** und **Fahrzeug-ID**.
- Sichtbare Teilnehmeranzeige nutzt ggf. die **Laufnummer** der Fahrzeugroute.
- Aufgabe, Lagebild, Code und Lösung bleiben der **echten Station** zugeordnet.
- Routenplanung nicht ungewollt ändern.

## Prüfung nach jedem Patch

- Browser-Konsole fehlerfrei.
- `node --check` (siehe Patch-Workflow).
- Versionen synchron (`APP_INFO.version` / `STATION_APP_INFO.version` + Kopf).
- Betroffene UI sichtbar geprüft.
- Keine ungewollten Änderungen an `data/uebung.json`.
- `BACKLOG.md` aktualisiert, falls ein vorgemerkter Punkt umgesetzt wurde.

## Rückfragepflicht

Bei Widersprüchen zwischen Nutzerwunsch, `CLAUDE.md`, `BACKLOG.md`, aktuellem
Code oder GitHub-Branch/Pages-Branch: **immer zuerst Rückfrage** stellen,
keine Annahmen treffen.

## Styleguide (Design-Tokens)

Alle Farben/Maße kommen aus dem `:root`-Block in `index.html` – dort ändern,
nicht hart kodieren. `station.html` hält dieselbe Optik eigenständig.

- **Flächen:** `--bg` #16181c (Seite), `--bg-2` #1b1e23 (Inhalt),
  `--surface` #23272e (Karten), `--surface-2` #2a2f37 (Hover/erhöht).
- **Linien:** `--line` #353b44, `--line-soft` #2c313a.
- **Text:** `--text` #e8eaed, `--text-muted` #99a0aa, `--text-faint` #6b727c
  (Hinweise/Labels).
- **Akzent (Feuerwehr-Rot):** `--red` #861619, `--red-strong` #a11f27 (Hover),
  `--red-soft` rgba(134,22,25,.18).
- **Statusfarben:** Offen `--status-open` #6b727c · In Bearbeitung
  `--status-prog` #e0a32e · Vollständig `--status-done` #3fae6b.
- **Buttons (Semantik → Farbe):**
  - primary = Rot (`--red`): Haupt/Speichern/Hinzufügen
  - secondary = Anthrazit (`--btn-neutral` #2a2f37): neutral
  - warning = Gelb (`--btn-warn` #d29a2b, dunkle Schrift `--btn-warn-fg` #18190d):
    Hinweis/Prüfen
  - success = Grün (`--btn-ok` #2f9e60)
  - danger = Rot-Outline: destruktiv · disabled = Grau
- **Maße/Form:** `--radius` 10px, `--radius-sm` 7px, `--shadow`
  0 2px 10px rgba(0,0,0,.35), `--sidebar-w` 250px.
- **Schrift:** `--font` Segoe UI/Roboto/system-ui; `--font-mono`
  Consolas/SF Mono (Codes/Positionscode).
- **Pflichtfeld:** mit `*` kennzeichnen; Fehlermeldung kurz und konkret
  (z. B. „Bitte eine Aufgabe eintragen."). Gedeckte Töne, kein Neon.

## Layout / Aufbau (festgeschrieben)

**`index.html` (Planungstool):**
- `.app-header` (Kopf) · `.app-body` (Flex) · `.app-footer` (Versionszeile).
- `.app-body` = `.sidebar` (`--sidebar-w` 250px, Label „Planungsbereiche",
  Buttons per JS aus der `SECTIONS`-Liste) + `.content` (`<main>`).
- `.content` enthält mehrere `.section`-Blöcke (`display:none`, nur
  `.section.active` sichtbar). Jede Section: `.section-head` (h2 + Beschreibung)
  + Inhalt; das Dashboard nutzt `.card-grid` mit anklickbaren Kacheln.
- `SECTIONS`-Reihenfolge = Navigation: Übersicht · Grunddaten · Fahrzeuge ·
  Stationsplanung · Routenplanung · Lösungssatz · QR-Code-Plan · Druck/Export.

**`station.html` (Teilnehmeransicht):**
- `.topbar` (Flex): `.st-no` (rotes Quadrat = **Laufnummer**) + `h1`
  (**Einsatzort/Adresse**) + `.veh` (Fahrzeug · Funkrufname).
- `.grid` (1 Spalte mobil, ab 760px 2 Spalten): **Aufgabe**-Karte +
  **Lagebild**-Karte. Fehlt das Lagebild, entfällt die Karte ganz (ab v1.0.7)
  → Aufgabe spannt über die volle Breite (kein `.grid`-Wrapper).
- `.code-card` darunter: Positionscode-Eingabe + freigeschaltetes Lösungszeichen.

**Druck:** QR-Druckkarten als A4-Seiten je Fahrzeug in Routenreihenfolge
(Label = Laufnummer, Link = echte Stationsnummer).

`station.html` hält die Optik eigenständig (eigene CSS), nutzt aber dieselben
Token-Werte wie der `:root`-Block in `index.html`.

## Datenmodell & Fachlogik (festgeschrieben)

- **`data/uebung.json`-Format:** äußere Hülle
  `{ app:"EA-Funkuebung-Planungstool", format:"b64", payload:<Base64(UTF-8-JSON)> }`.
  Innen: `{ app, version, generatedAt, meta:{name,date,time},
  vehicles:[{id,type,callsign}],
  stations:[{nr,id,title,task,address,images:[{url,title,vehicleIds}]}],
  assignments:{ fhzId:{ stationNr:{char,code,laufnr} } } }`.
- **Positionscode:** Format `W1P<n>`, `n` = 1-basierte Zeichenposition im
  Lösungssatz, `n = fahrzeugIndex*stationCount + routenIndex + 1`.
  `station.html` schaltet das Zeichen frei, wenn die Eingabe (case-insensitiv,
  ohne Leerzeichen) `assignments[fhz][s].code` entspricht.
- **Laufnummer:** sichtbare Nummer = Position der echten Station in der
  Fahrzeugroute (1..N). QR/URL nutzen die **echte** Stationsnummer `s=<echt>`.
  Herleitung: `assign.laufnr`, sonst aus dem Code `((n-1) mod stationCount)+1`.
- **Lösungssatz wird NIE im Klartext exportiert.** `data/uebung.json` enthält
  bewusst **keinen** `loesung.phrase` – nur die pro Fahrzeug/Station verteilten
  Einzelzeichen + Codes. Diese Trennung nicht aufweichen.
- **Eigener QR-Encoder** (`QRGEN`/`QRM`, Byte-Modus, EC-Level M, v1–10) ist in
  `index.html` eingebettet. Keine externe QR-Lib/CDN zur Laufzeit nötig.

## node --check – Hinweis

Im Kopf-Kommentar von `index.html` steht beispielhafter `<script>`-Text
(Verzeichnisbaum). Ein naives `<script>`-Regex erfasst diesen Kommentar fälschlich.
Für die Syntaxprüfung **nur den echten App-Block** extrahieren (die Zeile
`<script>` ohne `src`-Attribut bis zum zugehörigen `</script>`) und mit
`node --check` prüfen.

## Konventionen

- Commit-Autor: `Claude <noreply@anthropic.com>`. Modell-Identifier niemals in
  Commits/PRs/Code/Artefakten.
- Sprache mit dem Nutzer und in der UI: Deutsch.
- Button-Farbregel: primary=rot (Haupt/Speichern/Hinzufügen), secondary=anthrazit
  (neutral), warning=gelb (Hinweis/Prüfen), success=grün, danger=rot-outline
  (destruktiv), disabled=grau.
