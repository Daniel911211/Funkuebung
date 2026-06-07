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

## Konventionen

- Commit-Autor: `Claude <noreply@anthropic.com>`. Modell-Identifier niemals in
  Commits/PRs/Code/Artefakten.
- Sprache mit dem Nutzer und in der UI: Deutsch.
- Button-Farbregel: primary=rot (Haupt/Speichern/Hinzufügen), secondary=anthrazit
  (neutral), warning=gelb (Hinweis/Prüfen), success=grün, danger=rot-outline
  (destruktiv), disabled=grau.
