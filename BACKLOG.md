# Backlog – vorgemerkte Änderungen

Diese Datei sammelt **vorgemerkte** Änderungen, die NICHT sofort einzeln als
eigener Patch umgesetzt werden, sondern jeweils zusammen mit dem nächsten
ohnehin geplanten Patch. Versionsregel beim Umsetzen entsprechend anwenden
(APP_INFO.version bzw. STATION_APP_INFO.version + Kopf-Kommentar synchron).

---

## Offen

### [ ] Stationsplanung: Feld „Kurzbeschreibung / Hinweise" umbenennen

**Status:** offen · vorgemerkt am 2026-06-07 · Priorität: niedrig

- Sichtbares Label **„Kurzbeschreibung / Hinweise"** → **„Notiz für die Übungsleitung"**.
- Betrifft `index.html` → `renderStationDetail()` (Label des `detDesc`-Feldes;
  ggf. Platzhalter „optionale Hinweise" passend anpassen).
- Es ist ein **internes Feld** (intern `description`): wird **nicht** exportiert,
  Teilnehmer sehen es nie → nur das **Label** ändern, Feldname/Logik bleiben.
- Mit dem nächsten ohnehin geplanten Patch umsetzen, Versionsregel beachten.

---

## Erledigt

### [x] Stationsplanung: Begriffe „Aufgabe" / „Aufgabenbeschreibung" + Pflichtfeld

**Status:** erledigt am 2026-06-07 · umgesetzt mit index.html v1.27.0 / station.html v1.0.8

Umsetzung: Tabelle/Detail-Labels umbenannt („Bezeichnung"→„Aufgabe",
„Aufgabe kurz"/„Aufgabe der Station"→„Aufgabenbeschreibung"); „Aufgabe" (intern
`title`) ist Pflichtfeld mit `*`, Inline-Fehlermeldung „Bitte eine Aufgabe
eintragen." und steuert die Vollständigkeit; „Aufgabenbeschreibung" (intern
`task`) optional. station.html zieht mit (Block „Aufgabe" oben, sofern Titel
hinterlegt; bisherige Karte heißt „Aufgabenbeschreibung"). Interne Feldnamen
unverändert (`title`/`task`), bestehende Daten bleiben erhalten.
