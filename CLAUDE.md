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

## Konventionen

- Commit-Autor: `Claude <noreply@anthropic.com>`. Modell-Identifier niemals in
  Commits/PRs/Code/Artefakten.
- Sprache mit dem Nutzer und in der UI: Deutsch.
- Button-Farbregel: primary=rot (Haupt/Speichern/Hinzufügen), secondary=anthrazit
  (neutral), warning=gelb (Hinweis/Prüfen), success=grün, danger=rot-outline
  (destruktiv), disabled=grau.
