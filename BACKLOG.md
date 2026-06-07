# Backlog – vorgemerkte Änderungen

Diese Datei sammelt **vorgemerkte** Änderungen, die NICHT sofort einzeln als
eigener Patch umgesetzt werden, sondern jeweils zusammen mit dem nächsten
ohnehin geplanten Patch. Versionsregel beim Umsetzen entsprechend anwenden
(APP_INFO.version bzw. STATION_APP_INFO.version + Kopf-Kommentar synchron).

---

## [ ] Stationsplanung: Begriffe „Aufgabe" / „Aufgabenbeschreibung" + Pflichtfeld

**Status:** offen · vorgemerkt am 2026-06-07 · umzusetzen mit dem nächsten geplanten Patch

### Tabelle (Stationsübersicht)
- Spalte **„Bezeichnung"** umbenennen in **„Aufgabe"**
- Spalte **„Aufgabe kurz"** umbenennen in **„Aufgabenbeschreibung"**

### Stationsbearbeitung / Kartenbereich
- Alle sichtbaren Labels prüfen und vereinheitlichen
- **„Bezeichnung"** ersetzen durch **„Aufgabe"**
- **„Aufgabe kurz"** ersetzen durch **„Aufgabenbeschreibung"**
- Keine widersprüchlichen Begriffe wie „Titel", „Bezeichnung" oder „Aufgabe kurz"
  im Stationsbereich stehen lassen

### Feldlogik
- **„Aufgabe"** = kurze Bezeichnung / Titel der Station (intern aktuell `title`)
- **„Aufgabenbeschreibung"** = ausführlicher Aufgabentext für die Teilnehmer
  (intern aktuell `task`)
- Das Feld **„Aufgabe" ist ab dem nächsten Patch ein Pflichtfeld**
- Hinweis **„optional"** bei „Aufgabe" entfernen
- Pflichtfeld optisch kenntlich machen (z. B. mit `*`)

### Validierung
- Speichern ohne „Aufgabe" darf nicht mehr möglich sein
- Leere Eingabe oder nur Leerzeichen ist ungültig
- Fehlermeldung z. B.: **„Bitte eine Aufgabe eintragen."**

### Wichtig / Randbedingungen
- Bestehende gespeicherte Daten müssen kompatibel bleiben
- Interne Feldnamen nur ändern, wenn technisch notwendig
  (Mapping: sichtbar „Aufgabe" → intern `title`; sichtbar
  „Aufgabenbeschreibung" → intern `task`)
- Bestehende Aufgabenbeschreibungen (`task`) dürfen nicht verloren gehen
- Zusammen mit dem nächsten ohnehin geplanten Patch umsetzen
- Versionsregel beim nächsten Patch entsprechend anwenden
