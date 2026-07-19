# Arbeitsanweisung – Funkübung-Planungstool

Projekt: statische GitHub-Pages-App (kein Build-Schritt). Alles inline in
`index.html` (Planungstool), `station.html` (Teilnehmer-/QR-Ansicht) und
`elw.html` (ELW-Koordination für Einsatz-/Übungsleitung); Übungsdaten in
`data/uebung.json` (Base64-verpackt). `station.html` und `elw.html` sind lesende
Schwesterseiten (laden nur `data/uebung.json`, eigene Versionierung
`STATION_APP_INFO`/`ELW_APP_INFO`). **`elw.html` zeigt NIE den Lösungssatz oder das
Lösungszeichen (`char`) – nur Positionscodes.** Live ausgeliefert wird
ausschließlich der Stand auf dem Branch `main`.

Die vollständige Versionshistorie steht in `CHANGELOG.md` (nicht mehr im
Datei-Kopf). Die Datei-Köpfe enthalten nur noch die aktuelle Versionszeile +
Kurzbeschreibung und verweisen auf `CHANGELOG.md`.

## Zusatzfunktionen (live seit 2026-07-18)

Mit dem Merge des Branches `claude/funny-lovelace-Ljjaw` nach `main` sind live
(Details in `CHANGELOG.md`/`BACKLOG.md`):
- **Aufgabentyp Multiple-Choice + Mastercode-Override** (index v1.34.0 / station v1.0.16).
- **Funkaufträge (Funkwort-Weitergabe)** (index v1.35.0 / station v1.0.17 / elw v1.3.0):
  Funkwort „Von" FHZ+Station „An" FHZ+Station oder ELW; Empfänger gibt es ein → Code frei.
  Export `relaySend` (Klartext beim Sender) / `relayRecv`/`relayElw` (nur Hash).
- **Mehrere Aufgaben-Blöcke je Station** (index v1.36.0 / station v1.1.0): Karte
  „Aufgabe" = 1..n Blöcke (`station.tasks[]`), Block 1 mit Geltungsbereich
  alle/ausgewählte Fahrzeuge, weitere Blöcke verteilen die restlichen Fahrzeuge;
  je Block eigener Aufgabentyp Text/Rätsel/MC. Export je Zelle optional
  `task`/`mc` (nur bei „ausgewählt"; „alle"-Fall weiter über `stations[].task`/`mc`
  → alte Leser kompatibel); station.html liest Zelle vor Station. Sichtbares Feld
  „Aufgabe" heißt seit v1.35.2 **„Stationsüberschrift"** (intern `title`).

## Projekt-Agenten (`.claude/agents/`)

Feste Subagenten mit klaren Rollen (Details in der jeweiligen Datei):
- **`programmierer`** – setzt Patches um (umsetzend) · **`handbuch`** –
  schreibt die eingebaute Bedienungsanleitung (umsetzend).
- **`pruefer`** – Regel-/Strukturprüfung · **`design`** – Styleguide/Optik ·
  **`test`** – Verhaltenstests per Node-Harness (alle rein prüfend/lesend).
- **`uebungsdesigner`** – fachliche Übungsinhalte (beratend).

Die Agenten dürfen miteinander reden (Vermittlung über die Hauptsitzung):
umsetzende Agenten reichen Stände bei den Prüfenden ein und bessern nach, bis
BESTANDEN. **Commit/Push/Merge macht ausschließlich die Hauptsitzung** nach
Freigabe des Nutzers; kein Agent verändert `data/uebung.json`.

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
   - `index.html`: `APP_INFO.version` + Versionszeile im Datei-Kopf
     (`APP_INFO x.y.z`).
   - `station.html`: `STATION_APP_INFO.version` + Versionszeile im Datei-Kopf.
   - **Neuen „Stand x.y.z"-Eintrag in `CHANGELOG.md` ergänzen** (neueste oben,
     im passenden Abschnitt index.html bzw. station.html) — **nicht** mehr in den
     Datei-Kopf.
   - Reine Doku/Backlog-Änderungen ohne App-Code → kein Versions-Bump.
3. `node --check` über den extrahierten `<script>`-Block (der echte App-Block
   in `index.html`: die Zeile mit reinem `<script>` ohne `src` bis zum
   zugehörigen `</script>`).
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
- `CHANGELOG.md` (Versionshistorie / letzter Stand)
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
- **Sender / Empfänger** = im Funkauftrag-Editor die beiden Seiten (früher
  „Von"/„An"; Datenmodell `from`/`to` unverändert): **Sender** sieht und funkt
  das Wort, **Empfänger** gibt es ein (ab index v1.38.0)

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
- Versionen synchron (`APP_INFO.version` / `STATION_APP_INFO.version` +
  Versionszeile im Datei-Kopf).
- `CHANGELOG.md` um den neuen Stand-Eintrag ergänzt (bei Versions-Bump).
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
  (Die Freischalt-Rätsel werden **im Stations-Detail** gepflegt, kein eigener
  Bereich mehr.)
- **Rätsel/Freischaltung im Stations-Detail (ab index v1.33.0):** Aufgabentyp
  „Text"/„Rätsel" je Station; bei „Rätsel" ein Editor mit **Geltungsbereich** (alle /
  ausgewählte Fahrzeuge, Chips wie Lagebilder), **FHZ-Rätsel** (Antwort der Besatzung)
  und **ELW-Rätsel** (Rückwort des ELW), je **Freitext oder Multiple-Choice**. Zusätzlich
  je Station „Lagebild ausblenden". (Das frühere Pflichtfeld „ELW zeigt Code direkt"
  entfiel mit index v1.37.0; intern ist `elwCodeDirect` fest `false`.)
  Speicherung im Station-Objekt (`renderStationRiddles`/`updateRiddle`).

**`station.html` (Teilnehmeransicht):**
- `.topbar` (Flex): `.st-no` (rotes Quadrat = **Laufnummer**) + `h1`
  (**Einsatzort/Adresse**) + `.veh` (Fahrzeug · Funkrufname).
- `.grid` (1 Spalte mobil, ab 760px 2 Spalten): **Aufgabe**-Karte +
  **Lagebild**-Karte. Fehlt das Lagebild – oder ist es per `stations[].hideLagebild`
  ausgeblendet (ab v1.0.15) – entfällt die Karte ganz → Aufgabe spannt über die volle
  Breite (kein `.grid`-Wrapper).
- `.code-card` darunter: Positionscode-Eingabe + freigeschaltetes Lösungszeichen.
- **Freischalt-Vor-Gate (ab v1.0.15):** Bei hinterlegten Hashes erscheint oberhalb
  der `.code-card` eine `.gate-card` mit bis zu zwei Teil-Gates: **FHZ-Gate**
  (FHZ-Rätsel `riddleFhz` + Eingabe der **Antwort der Besatzung**, Freitext oder
  Auswahl `fhzOptions` bei `fhzMode==="choice"`; Vergleich `hashFhz`) und **ELW-Gate**
  (Eingabe des **Rückworts des ELW**; Vergleich `hashElw`). Erst wenn **alle**
  hinterlegten Wörter stimmen (Hash-Vergleich, case-/leerzeichen-tolerant), wird die
  `.code-card` sichtbar. Fehlen beide Hashes → `.code-card` direkt sichtbar.

**Druck:** QR-Druckkarten als A4-Seiten je Fahrzeug in Routenreihenfolge
(Label = Laufnummer, Link = echte Stationsnummer).

`station.html` hält die Optik eigenständig (eigene CSS), nutzt aber dieselben
Token-Werte wie der `:root`-Block in `index.html`.

**`elw.html` (ELW-Koordination):**
- Eigenständige lesende Seite (lädt nur `data/uebung.json`, eigene CSS mit denselben
  Token-Werten, eigene `ELW_APP_INFO.version`). Aufruf `elw.html` (alle aktiven
  Fahrzeuge) oder `elw.html?fhz=<id>` (eines).
- Aufbau: Kopfkarte (`.elw-top`, Übungsleitung) · Karte **Funkkanäle**
  (`meta.channels`, Fallback-Hinweis bei altem Export) · Karte **Fahrzeuge &
  Aufträge**: je aktivem Fahrzeug eine `.veh`-Karte mit Routenliste nach Laufnummer —
  Laufnummer · Einsatzort (Adresse, Hauptzeile) · **Aufgabe** (Stationstitel als
  eigene Spalte, ohne Stationsnummer/Beschreibung) · **Positionscode** · Status
  **Auftrag erteilt → erledigt**. Funkkanäle-Spaltenkopf „NR", „Kanal n".
- Status nur **lokal** (localStorage `funkuebung_elw_status_v1`, je Übung über
  name+datum getrennt), keine Online-Übertragung. **Kein Lösungssatz/`char`.**
- **Code-Anzeige + ELW-Rätsel (ab v1.2.0):** `elw.html` prüft **kein** Freischalt-Wort
  mehr. Der Positionscode erscheint je Station entweder **direkt**
  (`stations[].elwCodeDirect===true` – nur noch in Alt-Exporten; seit index v1.37.0
  exportiert das Planungstool konstant `false`) oder erst nach Klick „Code anzeigen". Ein
  hinterlegtes `riddleElw` (mit `elwOptions` bei Multiple-Choice) wird per Button
  „ELW-Rätsel anzeigen" in einem **Overlay** gezeigt – die ELW löst es und funkt das
  **Rückwort des ELW** an die Besatzung (Prüfung auf `station.html`). Weiterhin
  **kein** `char`/Lösungssatz.

## Datenmodell & Fachlogik (festgeschrieben)

- **`data/uebung.json`-Format:** äußere Hülle
  `{ app:"EA-Funkuebung-Planungstool", format:"b64", payload:<Base64(UTF-8-JSON)> }`.
  Innen: `{ app, version, generatedAt,
  meta:{name,date,time,leader,channels:[{tmo,dmo}]},
  vehicles:[{id,type,callsign}],
  stations:[{nr,id,title,task,address,hideLagebild,elwCodeDirect,images:[{url,title,vehicleIds}]}],
  assignments:{ fhzId:{ stationNr:{char,code,laufnr} } } }`. `meta.leader`
  (Übungsleitung) und `meta.channels` (befüllte Funkkanäle, ohne interne id) ab
  index v1.31.0 für `elw.html`; ältere Exporte ohne `channels` → Fallback-Hinweis.
  `stations[].hideLagebild`/`elwCodeDirect` ab index v1.33.0 (siehe Rätsel);
  `elwCodeDirect` seit index v1.37.0 ohne UI-Feld und konstant `false` (bleibt nur
  aus Kompatibilität zu Alt-Lesern im Export, `elw.html` unverändert).
- **Export-Vollständigkeit (ab index v1.38.0):** Der `data/uebung.json`-Export
  **bricht hart ab**, wenn nicht alle Stationen vollständig sind (analog zur
  `invalid-start`-Prüfung des Lösungssatzes). Neu zählt der **Einsatzort/die
  Adresse** zu `isStationComplete` (`deStationAddress` muss gesetzt sein) → eine
  Station ohne Adresse gilt als „in Bearbeitung" und verhindert den Export (wirkt
  auch auf die Status-Pillen). Keine Beispieladressen einsetzen (Fallback-Anzeige
  bleibt bei fehlenden Echtdaten).
- **Rätsel-Felder (ab index v1.33.0):** Pflege je Station im Stations-Detail
  (`riddle` im Station-Objekt: `scope` „all"/„selected", `vehicleIds`, je Seite `fhz`/
  `elw` = `{q,mode,options,answer,correct,hint}`; `hint` = optionale Anweisung an
  die Besatzung, ab index v1.38.0). **Die Antwortart (`mode` Freitext/Auswahl)
  gilt ab index v1.38.1 gemeinsam für beide Rätsel-Seiten** – ein einziger
  „Antwortart"-Select (unter dem Aufgabentyp-Select) steuert FHZ und ELW; beide
  `mode`-Felder bleiben strukturell erhalten, sind aber immer gleich
  (`normalizeRiddle` koppelt beim Laden `elw.mode = fhz.mode`, FHZ kanonisch).
  Export je `assignments`-Zelle – nur für
  Fahrzeuge im Geltungsbereich – optional `riddleFhz`/`riddleElw` (Rätseltexte,
  Klartext), `fhzMode`/`elwMode` = `"choice"` (+ `fhzOptions`/`elwOptions` als
  Klartext-Optionen) bei Multiple-Choice, `hashFhz`/`hashElw` (Antwortwörter als
  **Verschleierungs-Hash** cyrb53, gesalzen mit `name+date` – KEIN Krypto, gleiches
  Niveau wie der Base64-Payload) und – falls gepflegt – `hintFhz`/`hintElw`
  (Anweisungstexte im Klartext; `station.html` zeigt sie als Gate-Hinweis statt des
  Standardtexts, leer = Standardtext). Alle Felder **optional** → fehlen sie, gibt es
  kein Gate (alter Ablauf). Gate-Logik **stationsseitig**: `station.html` prüft `hashFhz`
  (Antwort der Besatzung, vor Ort gelöst) **und** `hashElw` (Rückwort des ELW,
  heruntergefunkt); `elw.html` prüft **kein** Wort, zeigt nur `riddleElw` (Overlay)
  und den Code. Bei Multiple-Choice ist die richtige Option = `options[correct]`; ihr
  Hash landet in `hashFhz`/`hashElw`, `station.html` vergleicht den Hash der geklickten
  Option. `normWord` = Kleinschrift + alle Whitespaces raus; `hashWord` identisch in
  index/station (elw braucht es nicht mehr). **Wichtig:** Rätsel*texte*/Optionen stehen
  im Klartext in der Datei – nur die Antwortwörter sind verschleiert. Rätsel daher so
  bauen, dass sie nur mit Wissen **vor Ort** (FHZ) bzw. **der ELW** lösbar sind, sonst
  lässt sich der Funkverkehr umgehen. **Antwortwörter nie im Klartext exportieren.**
- **Positionscode:** Format `W<Wortnummer>P<Zeichenposition-im-Wort>` (beide
  1-basiert), z. B. `W2P3`. Erzeugung über den **getrimmten** Lösungssatz (nur
  vorne/hinten trimmen): ein Leerzeichen gehört noch zum bisherigen Wort und
  schließt es ab; Satz-/Sonderzeichen zählen zum aktuellen Wort; **alle** Zeichen
  erhalten einen Code. Beginnt der getrimmte Satz mit Leerzeichen/Satzzeichen →
  Fehler, keine Verteilung, Export abgebrochen. Doppelte Leerzeichen → nur Hinweis.
  Code-Erzeugung zentral im Planungstool (`loesungCodeMap`/`computeLoesungPlan`);
  `station.html` schaltet frei, wenn die Eingabe (case-insensitiv, ohne Leerzeichen)
  `assignments[fhz][s].code` entspricht (**reiner String-Vergleich, keine Berechnung**).
- **Laufnummer:** sichtbare Nummer = Position der echten Station in der
  Fahrzeugroute (1..N). QR/URL nutzen die **echte** Stationsnummer `s=<echt>`.
  Herleitung: direkt aus `assign.laufnr` (wird immer exportiert); fehlt sie, zeigt
  `station.html` eine klare Fehlermeldung.
- **Lösungssatz wird NIE im Klartext exportiert.** `data/uebung.json` enthält
  bewusst **keinen** `loesung.phrase` – nur die pro Fahrzeug/Station verteilten
  Einzelzeichen + Codes. Diese Trennung nicht aufweichen.
- **Eigener QR-Encoder** (`QRGEN`/`QRM`, Byte-Modus, EC-Level M, v1–10) ist in
  `index.html` eingebettet. Keine externe QR-Lib/CDN zur Laufzeit nötig.

## node --check – Hinweis

Für die Syntaxprüfung **nur den echten App-Block** extrahieren: die Zeile mit
reinem `<script>` ohne `src`-Attribut bis zum zugehörigen `</script>` (die andere
`<script>`-Zeile lädt Leaflet per `src` und ist auszulassen). Der frühere Stolper-
stein – beispielhafter `<script>`-Text im Kopf-Kommentar – ist entfallen, seit die
Versionshistorie in `CHANGELOG.md` ausgelagert ist.

## Konventionen

- Commit-Autor: `Claude <noreply@anthropic.com>`. Modell-Identifier niemals in
  Commits/PRs/Code/Artefakten.
- Sprache mit dem Nutzer und in der UI: Deutsch.
- Button-Farbregel: primary=rot (Haupt/Speichern/Hinzufügen), secondary=anthrazit
  (neutral), warning=gelb (Hinweis/Prüfen), success=grün, danger=rot-outline
  (destruktiv), disabled=grau.
