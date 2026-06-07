# Changelog – Funkübung-Planungstool

Vollständige Versionshistorie (neueste oben). Live ausgeliefert wird
ausschließlich der Stand auf dem Branch `main`.

Pflege bei jedem Patch (siehe `CLAUDE.md`): `APP_INFO.version` (index.html)
bzw. `STATION_APP_INFO.version` (station.html) **und** die Versionszeile im
Datei-Kopf synchron halten; den neuen Eintrag hier oben ergänzen.

---

## index.html (Planungstool)

Stand 1.32.0 (Rätsel-Freischaltung – TESTFUNKTION, nicht live): Neuer Bereich
„Rätsel (Test)" zwischen „Lösungssatz" und „QR-Code-Plan". Pro Fahrzeug × echte
Station lassen sich vier Felder pflegen – FHZ-Rätsel + FHZ-Wort und ELW-Rätsel +
ELW-Wort (Auswahl je Fahrzeug, Stationen in Routenreihenfolge; lokal gespeichert
unter eigenem Key `funkuebung_raetsel_test_v1`). Der Export ergänzt je Zelle
optional `riddleFhz`/`riddleElw` (Klartext) und `hashFhz`/`hashElw`
(Verschleierungs-Hash cyrb53, gesalzen mit name+date – KEIN Krypto, gleiches
Niveau wie der Base64-Payload; Antwortwörter stehen NICHT im Klartext in
data/uebung.json). Zweck: symmetrischer ELW↔FHZ-Funkverkehr – Besatzung funkt das
FHZ-Wort hoch (ELW gibt es auf elw.html ein → Positionscode), ELW funkt das
ELW-Wort runter (Besatzung gibt es auf station.html ein → Code-Feld frei). Alle
neuen Felder optional → fehlen sie, bleibt der bisherige Ablauf unverändert
(rückwärtskompatibel). loesung.phrase weiterhin NICHT exportiert; char unverändert.

Stand 1.31.0 (Export um Funkkanäle/Übungsleitung erweitert + ELW-Link):
data/uebung.json enthält jetzt in meta zusätzlich `leader` (Übungsleitung) und
`channels` (befüllte Funkkanäle TMO/DMO, ohne interne id) – Grundlage für die neue
ELW-Koordinationsseite. Rückwärtskompatibel (station.html ignoriert die Zusatzfelder;
char/code/laufnr unverändert; loesung.phrase weiterhin NICHT exportiert). Im Bereich
„Druck / Export" → „Stationsdaten" ein dezenter Link „ELW-Koordination öffnen"
(elw.html, neuer Tab; nur im Planungstool, nicht in der Teilnehmeransicht). Für
Live-Funkkanäle einmal neu exportieren und data/uebung.json hochladen.

Stand 1.30.0 (Bildersammlung – Lagebilder live aus GitHub-Repo):
Neue „Bildersammlung" in der Stationsplanung (Button im Lagebild-Kopf, analog zur
Satzsammlung als Overlay). Lädt die Bilder live aus dem öffentlichen Repository
Daniel911211/Bilder über die GitHub-Contents-API (im Browser, In-Memory-Cache
10 min gegen das unauth. Limit von 60 Abrufen/Stunde; Button „Aktualisieren"
erzwingt einen Neuabruf). Thumbnail-Raster; Klick/„Übernehmen" fügt das Bild als
Lagebild zur Station hinzu (raw-URL aus GitHub) – ist bereits ein leeres Lagebild
vorhanden, wird dessen URL gefüllt, sonst ein neues angelegt. Das manuelle
Bild-URL-Feld bleibt unabhängig nutzbar; bei Limit/Offline klare Fallback-Meldung.
Bilder werden NICHT lokal gespeichert. Neue Funktionen bildFetchList/renderBildGrid/
bildTake/openBildOverlay/closeBildOverlay/addStationImageWithUrl; kein eigener
Bereich/Tab. station.html und data/uebung.json unverändert.

Stand 1.29.0 (QR-Code-Plan – Vollständigkeitsprüfung + Dashboard-Pill, aktiv-basiert):
Der QR-Code-Plan hat jetzt einen eigenen Status (vorher dauerhaft „Offen", zählte
nie zur Fortschrittsleiste → 100 % unmöglich). SOLL = vorhandene Stationen × AKTIVE
Fahrzeuge (jeder Link enthält echte Stationsnummer + Fahrzeug-ID, also je Kombination
ein QR-Code). Vollständig, wenn: GitHub-Basislink gesetzt, mindestens ein Fahrzeug
aktiv, Stationen vorhanden und für jede Kombination ein gültiger QR-Code erzeugbar
(Anzahl erzeugbarer Codes = SOLL). Der Aktiv-Schalter zählt, Min./Max.-Personen sind
irrelevant, deaktivierte Fahrzeuge erzeugen keine Pflicht-Codes. Neue Funktionen
computeQrStatus()/qrCanEncode(); QR-Pill + Fortschritt in updateStatus() verdrahtet;
Basislink-Eingabe koppelt die Pill live. Effizient: nur der längste Link wird auf
Erzeugbarkeit geprüft (QR-Kapazität wächst monoton mit der Version). Keine Änderung
an QR-Übersicht/Druck/Export oder data/uebung.json.

Stand 1.28.2 (Loesungssatz – Buchstaben gross anzeigen): Die Loesungszeichen-Chips
in den Fahrzeugkarten zeigen Buchstaben jetzt immer GROSS (nur Anzeige via
toUpperCase; exportierter char und der Code-Vergleich in station.html bleiben
unveraendert; Satz-/Leerzeichen unberuehrt). Nur Anzeige, keine Logikaenderung.

Davor – Stand 1.28.1 (Loesungssatz – Beschreibungstext gekuerzt): Hinweis ueber der
Zeichen-Zuordnung je Fahrzeug lautet jetzt "Je aktivem Fahrzeug die Stations-
reihenfolge mit Loesungszeichen und Positionscode." (nennt den neuen Positions-
code; Funktion unveraendert). Nur Textaenderung, keine Logikaenderung.

Davor – Stand 1.28.0 (Positionscode-Logik Wort/Zeichen + Anzeige im Loesungssatz):
Positionscode ist jetzt W<Wortnummer>P<Zeichenposition-im-Wort> (1-basiert)
statt fortlaufender Gesamtposition. Leerzeichen gehoert zum bisherigen Wort und
schliesst es ab; Satz-/Sonderzeichen zaehlen zum aktuellen Wort; alle Zeichen
erhalten einen Code. Loesungssatz wird vor der Auswertung getrimmt (nur vorne/
hinten); beginnt er mit Leerzeichen/Satzzeichen -> Fehlermeldung, keine Verteilung,
Export abgebrochen. Doppelte Leerzeichen -> nicht-blockierender Hinweis. Die
Fahrzeugkarten im Bereich "Loesungssatz" zeigen den Positionscode (neue Spalte).
Code-Erzeugung zentral in computeLoesungPlan/loesungCodeMap; Export uebernimmt den
Code aus dem Plan. station.html prueft weiterhin nur per String-Vergleich.

Davor – Stand 1.27.3 (Aufraeumung): Ungenutzte CSS-Regeln (.qr-note, .label-optional)
entfernt; Dashboard-Kachel "Loesungssatz" gekuerzt (ohne Klammerzusatz, analog
zum Bereichskopf). Nur Aufraeumung, keine Funktions-/Logikaenderung.

Davor – Stand 1.27.2 (Export-Button gekuerzt): Button im Block "Stationsdaten" heisst
jetzt "Stationsdaten exportieren" (Funktion unveraendert, Datei bleibt
data/uebung.json).

Davor – Stand 1.27.1 (Text-/UI-Aufraeumung): Bereichstexte gekuerzt/aktualisiert
(Stationsplanung, Routenplanung, Loesungssatz), Hinweistext unter "Automatische
Fahrzeugrouten" und QR-Hinweisblock entfernt, Export-Button heisst jetzt
"Stationsdaten fuer QR-Seite exportieren" (Funktion unveraendert, Datei bleibt
data/uebung.json). Stations-Notizfeld "Kurzbeschreibung / Hinweise" -> "Notiz
fuer die Uebungsleitung" (internes Feld, nur Label). Keine Logikaenderung.

Davor – Stand 1.27.0 (Stationsbegriffe + Pflichtfeld "Aufgabe"): In der Stationsplanung
heisst die Spalte/das Feld "Bezeichnung" jetzt "Aufgabe" (kurzer Titel, intern
title) und "Aufgabe kurz"/"Aufgabe der Station" heisst "Aufgabenbeschreibung"
(ausfuehrlicher Text, intern task). "Aufgabe" ist Pflichtfeld (*, Fehlermeldung
"Bitte eine Aufgabe eintragen.") und steuert die Vollstaendigkeit; die
"Aufgabenbeschreibung" ist optional. Sidebar-Ueberschrift "PLANUNGSBEREICHE"
besser lesbar (groesser/kontrastreicher). Interne Feldnamen unveraendert
(title/task) – bestehende Daten bleiben erhalten.

Davor – Stand 1.26.9 (Stationsplanung-Zusammenfassung gestrafft): Die Zeile zeigt jetzt
zuerst "Stationen vollstaendig: X von Y", danach "Lagebilder: Z". Die separate
Gesamtzahl "Stationen: X" entfaellt (in "X von Y" bereits enthalten).

Davor – Stand 1.26.8 (data/uebung.json: Adresse robust abbilden): Generator bildet die
Stationsadresse robust auf address ab (position.address bzw. alternative Felder
ort/location/einsatzort/adresse). Nur Export-Aufbau, keine Logikaenderung.

Davor – Stand 1.26.7 (data/uebung.json: Laufnummer + Adresse fuer Teilnehmeranzeige): Der
Generator schreibt je Zuordnung zusaetzlich laufnr (Routenposition) und je Station
address (Einsatzort) in data/uebung.json. station.html zeigt damit Laufnummer +
Adresse statt echter Stationsnummer/Bezeichnung (echte Nr bleibt intern fuer
Aufgabe/Lagebild/Code/QR). QR-Druckkarten/-Pruefung unveraendert (v1.26.6).

Davor –   Stand 1.26.6 (QR-Druckkarten: Label = Laufnummer, Link = echte Station + Pruefung):
Sichtbare Kartenbeschriftung laeuft je Fahrzeug fortlaufend Station 1..N (Lauf-
nummer der Route); der QR-Code zeigt weiter auf die ECHTE Stationsnummer
(station.html?s=<echt>&fhz=<id>). Vor dem Drucken pruefen: Kartenanzahl=Stationen,
Laufnummern fortlaufend, Link je Karte korrekt (s+fhz), keine doppelte/fehlende
echte Station; bei Fehler kein Druck + Warnung, sonst "QR-Druckpruefung erfolgreich".

Davor – Stand 1.26.5 (Stationsplanung-Status: Standardstationen = Offen): Die 8 Standard-
Stationen (leer) ergeben jetzt Status „Offen" statt „In Bearbeitung". „In
Bearbeitung" erst, sobald Inhalte (Bezeichnung/Aufgabe/Hinweis/Lagebild) erfasst
wurden, aber noch nicht alle Stationen vollständig sind (neu: isStationEmpty()).

Davor – Stand 1.26.4 (Druck/Export – Planung zurücksetzen): Im Block „Planung" gibt es
jetzt „Planung zurücksetzen" (btn-danger) mit Sicherheitsabfrage („Zurücksetzen
bestätigen"/„Abbrechen"). Beim Bestätigen werden ALLE funkuebung_*-localStorage-
Keys gelöscht (fremde Daten bleiben) und die Seite neu geladen → leerer Start.

Davor – Stand 1.26.3 (Druck/Export – Abstand zwischen den Karten): 18px-Abstand zwischen
den Bloecken ergaenzt (wie andere Bereiche). Nur CSS.

Davor – Stand 1.26.2 (Druck/Export optisch als Karten): Die drei Blöcke (Planung,
Stationsdaten, QR-Code-Karten) haben jetzt roten Seitenakzent + Icon-Chip neben
dem Titel (Stil wie QR-/Routenkarten). Nur Optik, keine Logikänderung.

Davor – Stand 1.26.1 (QR-Druckkarten schlanker): Auf den einzelnen QR-Druckkarten
entfällt die Fahrzeug-/Funkrufname-Zeile (steht bereits im Seitenkopf) – Karte
zeigt nur noch „Station N" + QR. Nur Druck-Layout, keine Logikänderung.

Davor – Stand 1.26.0 (Druck / Export aufgebaut): Neuer Funktionsblock „Druck / Export":
(1) Planung exportieren/importieren als vollständiges Tool-JSON (alle
funkuebung_*-Keys; Import mit Bestätigung → Überschreiben → Reload, ungültige
Datei wird abgefangen). (2) „data/uebung.json exportieren" für station.html –
enthält nur meta/vehicles/stations/assignments (char+code), Base64-Wrapper wie
bisher, OHNE Lösungssatz-phrase. (3) QR-Code-Karten drucken: je Fahrzeug eine
A4-Seite (2×4), Reihenfolge aus der Routenplanung, Karten nur mit Fahrzeug/
Funkrufname/Station/QR (keine Bezeichnung, kein Link). Nutzt QRGEN/Canvas, kein
CDN. Zwei veraltete QR-Hinweistexte korrigiert. station.html/Repo-data/uebung.json
unverändert.

Davor – Stand 1.25.1 (QR-Code-Großvorschau im Overlay): Klick/Enter auf einen QR-Code in
der Übersicht öffnet ihn groß in einem Overlay (analog zur Lagebild-Vorschau,
Schließen per X/Backdrop/Escape) – mit Stations-/Fahrzeug-Beschriftung und Link.

Davor – Stand 1.25.0 (QR-Code-Grafiken je Station × Fahrzeug): Jede Fahrzeug-Zeile der
QR-Übersicht zeigt nun eine echte QR-Code-Grafik (Canvas) zum jeweiligen Link.
Eigener, abhängigkeitsfreier QR-Encoder (QRGEN, Byte-Modus, EC-Level M, v1..10,
4-Modul-Ruhezone) – komplett lokal, KEIN CDN/Fremdskript; bit-identisch gegen die
Referenz qrcode-generator für alle erzeugten Links verifiziert. QR enthält exakt
den angezeigten Link; aktualisiert sich (debounced) bei Änderung des Basislinks;
relative Links funktionieren weiterhin. Textlink + „Alle Links kopieren" bleiben.
station.html/data/uebung.json sowie übrige Logik unverändert.

Davor – Stand 1.24.2 (QR-Übersicht – Stationen als Karten): Jede Station ist in der
QR-Übersicht nun eine eigene Karte (Rahmen + roter Akzent links, wie die
Fahrzeugrouten-Karten), volle Breite gestapelt → klar getrennt. Nur Anzeige.

Davor – Stand 1.24.1 (QR-Bereich entschlackt): In den Zeilen der QR-Übersicht entfällt
die dezente FHZ-ID-Spalte (war doppelt zum Fahrzeugtyp und steht ohnehin im Link).
Zusätzlich wurde der Grundlagen-Info-Block (GitHub-Basislink/Stationsseite/
Beispiel-Link/Format-Anzeige) entfernt; das Eingabefeld „GitHub-Basislink" bleibt.
Nur Anzeige, keine Logikänderung.

Davor – Stand 1.24.0 (QR-Code-Plan – Link-Übersicht Station × Fahrzeug): Der QR-Bereich
ist kein Platzhalter mehr. Grundlagen-Panel mit GitHub-Basislink (Eingabefeld,
auto-gespeichert in funkuebung_qr_settings_v1; „Aus aktueller Adresse übernehmen";
Stationsseite/Beispiel-Link/Format als Hinweis) und eine Übersicht für ALLE
aktiven Fahrzeuge × ALLE Stationen, sortiert nach Station, je Eintrag Fahrzeugtyp,
Funkrufname, FHZ-ID und fertiger Link <Basislink>/station.html?s=<Nr>&fhz=<ID>
(ohne doppelte Slashes; leerer Basislink → relative Links). „Alle Links kopieren".
Noch keine QR-Grafiken; Export von data/uebung.json folgt unter Druck/Export.
station.html, data/uebung.json sowie Lösungssatz-/Routen-/Fahrzeug-/Stationslogik
unverändert.

Davor – Stand 1.23.2 (Lösungssatz – Hinweisbox größere Schrift): Titel und Text der
Zeichenstatus-Hinweisbox sind etwas größer (1.05rem / .95rem) für bessere
Lesbarkeit. Nur CSS, keine Logikänderung.

Davor – Stand 1.23.1 (Karten-Suche oben rechts): Das Adress-/Straßensuche-Control sitzt
jetzt oben rechts in der Karte (Position "topright") statt oben links, damit die
Zoom-Buttons frei bleiben und nichts verdeckt wird. Verhalten unverändert.

Davor – Stand 1.23.0 (Routenplanung – Adress-/Straßensuche in der Karte): Die Karte hat
jetzt ein Leaflet-Such-Control oben links (unter den Zoom-Buttons): Eingabefeld
„Straße oder Adresse suchen" + Lupen-Button. Gesucht wird NUR auf Klick/Enter
(kein Autocomplete) über Nominatim (format=jsonv2, countrycodes=de, limit=5,
Query um „…, Neuhof, Landkreis Fulda, Hessen, Deutschland" ergänzt). Ein Treffer
zentriert die Karte und setzt einen temporären blauen Such-Marker; mehrere
Treffer erscheinen als kleine Auswahlliste; „kein Treffer"/Fehler werden als
Hinweis angezeigt. Such-Marker ist separat – Stations-/Marker-/Routenlogik
unverändert. Eingaben im Control werden nicht an die Karte weitergereicht.

Davor – Stand 1.22.5 (Button-Farben an die Regel angeglichen): „+ Lagebild hinzufügen"
von secondary auf primary (Hinzufügen = rot); „Felder leeren" (Grunddaten) von
secondary auf danger (destruktiv = rot, wie „Feld leeren" im Lösungssatz).
„Aktuellen Lösungssatz speichern" bleibt bewusst secondary (sekundäres Speichern).
Nur Optik, keine Logikänderung.

Davor – Stand 1.22.4 (Lösungssatz – Button „Feld leeren" als Danger-Stil): Der Button
„Feld leeren" in Karte 1 nutzt jetzt den roten Danger-Stil (.btn-danger) wie
„Marker entfernen". Nur Optik, keine Logikänderung.

Davor – Stand 1.22.3 (Lösungssatz – Klarnamen für Satzzeichen): Im Bereich „Zeichen-
Zuordnung je Fahrzeug" zeigen die Chips für Satzzeichen und das Leerzeichen jetzt
einen kleinen Untertitel mit dem Klarnamen (z. B. „!" → Ausrufezeichen, „␣" →
Leerzeichen). Buchstaben/Ziffern und „–" (keine Zuordnung) bleiben ohne Text.
Nur Anzeige, keine Logikänderung.

Davor – Stand 1.22.2 (Lösungssatz – Zuordnungs-Karten im Raster): Die Fahrzeugkarten im
Bereich „Zeichen-Zuordnung je Fahrzeug" liegen jetzt im selben mehrspaltigen
Raster wie „Automatische Fahrzeugrouten" (auto-fill, minmax 300px). Nur Layout,
Inhalt unverändert. Keine Logikänderung.

Davor – Stand 1.22.1 (Lösungssatz – kleine Aufräumarbeiten): Die Hinweiszeile unter dem
Eingabefeld („Pro aktivem Fahrzeug … Standardsatz als Referenz: …") entfällt; in
den Vorgabewörtern wird die Zeile „Bei 6 Einheiten" nicht mehr gezeigt (nur noch
5 und 4 Einheiten). Tote CSS (.loes-note/.loes-mono) entfernt. Keine Logikänderung.

Davor – Stand 1.22.0 (Lösungssatz – Satzsammlung als Overlay): Der Button „Satzsammlung"
in Karte 1 öffnet jetzt ein Overlay/Modal (KEIN eigener Bereich/Tab/Nav-Eintrag)
mit einer einfachen lokalen Sammlung guter Lösungssätze. Eigener Storage-Key
funkuebung_satzsammlung_v1 (Array aus Strings). Funktionen: Satz manuell
hinzufügen, aktuellen Lösungssatz speichern, je Satz Übernehmen/Löschen
(mit Sicherheitsabfrage), TXT-Import (ein Satz/Zeile), JSON-Import (Array aus
Strings) – beide ergänzend mit Duplikat-Übersprung und Import-Zusammenfassung –
sowie JSON-Export (funkuebung-satzsammlung.json). Duplikate werden getrimmt und
case-insensitiv erkannt; leere Sätze nicht gespeichert; Inhalte escaped.
„Übernehmen" verhält sich wie manuelle Eingabe (Lösungssatz + Status + Zuordnung
+ Dashboard aktualisiert). Die früheren 3 Beispielsätze entfallen (Start leer).

Davor – Stand 1.21.2 (Lösungssatz – Hinweisbox aufgeräumt): Die Metazeile „SOLL: … ·
IST: …" unter dem Hinweistext entfällt (Information steht bereits in den
SOLL/IST/STATUS-Boxen). Zugehörige CSS-Regel entfernt. Keine Logikänderung.

Davor – Stand 1.21.1 (Routenplanung – Tabelle & Routenliste auf Adresse umgestellt): In
der Tabelle „Stationspositionen" heißt der Spaltenkopf jetzt „Station" (Zellen
weiter nur die Nummer) und die separate Namensspalte „Station" entfällt
(Spalten: Station | Adresse | Koordinaten | Status). In der Liste „Automatische
Fahrzeugrouten" zeigt jede Station nun die Adresse statt der Bezeichnung; bei
leerer Adresse dezent „Adresse nicht gesetzt". Dropdown, Karten-Tooltip und
Stationsplanungs-Tabelle unverändert. Keine Logikänderung.

Davor – Stand 1.21.0 (Lösungssatz – Neuaufbau in 3 Kartenbereichen): Der Bereich
„Lösungssatz" wurde komplett neu aufgebaut. (1) ERFASSEN: Textarea mit Auto-
Speicherung, Buttons „Feld leeren" + „Satzsammlung" (kleine Satzliste).
(2) ZEICHENSTATUS: SOLL (=aktive Fahrzeuge×Stationen) / IST (Zeichen) / STATUS
(Vollständig | Fehlen: X | Zu viel: X) mit Hinweisbox + „Vorgabewörter für die
Übungsleitung" (Resttext bei 6/5/4 Einheiten). (3) ZUORDNUNG: je aktivem
Fahrzeug eine Karte mit Stationsreihenfolge (bestehende Routenlogik) und
automatisch zugeordnetem Zeichen – Prinzip 8.9: max. 1 Zeichen je Fahrzeug/
Station, Rest → Übungsleitung. Zentrale Funktion computeLoesungPlan(). Dashboard-
Pill: done nur bei IST=SOLL, sonst In Bearbeitung. Storage unverändert
(funkuebung_loesungssatz_v1 = { phrase, mode:"vehicles" }). Routen-/Fahrzeug-/
Stations-/QR-Logik unverändert. Einmal-Bereinigung des Storage-Keys entfernt.

Davor – Stand 1.20.1 (Stationsplanung: Bezeichnung optional, Nummer = Identität): Der
Tabellenkopf der Stationsplanung heißt jetzt „Station" (Zellen zeigen weiterhin
nur die Nummer 1, 2, 3 …). Die Bezeichnung (title) ist fachlich OPTIONAL und darf
leer bleiben – sie ist KEINE Pflicht für die Vollständigkeit (isStationComplete
unverändert: Aufgabe + Lagebild-URLs + Fzg-Zuordnung ab 2 Bildern). Leere
Bezeichnung: in der Stationsplanungs-Tabelle dezent „–", sonst (Auswahlfelder,
Routen, Karte) Fallback „Station X" (X = Stationsnummer) via stationDisplayName().
normalizeStation füllt leere Titel nicht mehr automatisch auf; neue/geseedete
Stationen starten ohne Bezeichnung. Bestehende Titel bleiben unverändert (keine
Bereinigung). Detail-Label „Bezeichnung (optional)". Storage-Struktur unverändert.

Davor – Stand 1.20.0 (Lösungssatz-Logik entfernt – Rahmen bleibt, Neuaufbau folgt): Die
gesamte Lösungssatz-LOGIK (Verteilung, Status, Tabelle, Übungsleitung, CSS, JS)
wurde entfernt. Der Bereich bleibt als RAHMEN bestehen: Nav-Button, Dashboard-
Karte (Status „Offen") und Handbuch-Kapitel über den SECTIONS-Eintrag, dazu eine
Platzhalter-Section („In Neuaufbau") analog zum QR-/Druck-Bereich. Der alte
Storage-Key funkuebung_loesungssatz_v1 wird beim Laden einmalig entfernt (die
Bereinigungszeile beim Neuaufbau wieder löschen).

Davor (1.19.1, Lösungssatz – Zuordnung exakt wie 8.9): Die Fahrzeug-Zuordnung
vergibt jetzt FESTE Blöcke von genau (Stationsanzahl) Zeichen je Fahrzeug der
Reihe nach (Fzg1 = Zeichen 1..S, Fzg2 = S+1..2S …) statt einer gleichmäßigen
Verteilung. Bei voller Belegung (z. B. 6 Fzg × 8 Stat. = 48) unverändert; der
Unterschied zeigt sich nur bei Teilbelegung (z. B. 20 Zeichen/6 Fzg → 8,8,4,0,0,0
statt 4,4,3,3,3,3). Übungsleitungs-Anteil/Hinweise/Ampel unverändert. Positions-
code W?P? weiterhin NICHT umgesetzt (Zukunftsthema ELW-Koordination).

Davor (1.19.0, Lösungssatz – Prinzip 8.9: max. 1 Zeichen je Fahrzeug/Station):
Verteilung fachlich umgebaut. Fahrzeug-Kapazität = aktive Fahrzeuge × Stationen;
die ersten (Kapazität) Zeichen gehen blockweise an die Fahrzeuge (je ≤ Stationen
→ max. 1 Zeichen je Station, keine Mehrfachzeichen), ALLE restlichen Zeichen
werden als „Übungsleitung ergänzt" ausgewiesen (neuer Bereich unter der Tabelle:
Boxen + Anzahl + Hinweis). Tabelle jetzt Fahrzeug | Funkrufname | Verteilte
Zeichen | Stationen belegt (X von Y). Kontrollübersicht: Gesamtzeichen, aktive
Fahrzeuge, Stationen, Fahrzeug-Kapazität, über Fahrzeuge verteilt, Übungsleitung
ergänzt, Ampel. Ampel NUR Gelb/Grün (kein Rot): Grün = Satz + ≥1 Fahrzeug + ≥1
Station; Restzeichen sind KEIN Fehler. computeLoesungDistributionInfo() neu;
LOESUNG_TARGET_LENGTH/Soll-Längenprüfung entfernt. Dashboard-Pill open/prog/done.
Positionscode W1P3 bleibt ELW-Koordination vorbehalten.

Davor (1.18.0, Lösungssatz – Ampel & Soll-Längenprüfung): Kontrollübersicht
umgebaut, Soll-Längenprüfung gegen LOESUNG_TARGET_LENGTH (in 1.19.0 ersetzt).

Davor (1.17.1, Lösungssatz – Status zählerbasiert): Der Status in der Kontroll-
übersicht wird jetzt aus dem Vergleich „verteilte Zeichen (X) gegenüber gesamt
benötigten Zeichen (Y)" abgeleitet (X==Y → Vollständig, 0≤X<Y → In Bearbeitung,
Y==0 → Offen) statt aus der Anzahl aktiver Fahrzeuge. Ergebnisse identisch,
Logik sauber an die Zähler gekoppelt. Anzeige „X von Y" unverändert.

Davor (1.17.0, Lösungssatz – Auto-Speichern + Übersicht): Der Lösungssatz wird
jetzt bei jeder Eingabe automatisch gespeichert (Indikator „Automatische
Speicherung aktiv", flasht „Gespeichert"), analog zur Grunddaten-Maske. Dadurch
bleiben Kontrollübersicht UND Dashboard-Status live konsistent (vorher änderte
sich der Dashboard-Status erst nach Klick auf „Speichern"). „Speichern"-Button
bleibt als manueller Auslöser. Kontrollübersicht: „Gesamtzeichen" und „Verteilte
Zeichen" sind zu „Verteilte Zeichen: X von Y" zusammengefasst.

Davor (1.16.0, Lösungssatz – fahrzeugbezogen): Die Verteilung ist nicht mehr
stationsbezogen, sondern verteilt den Satz BLOCKWEISE und gleichmäßig auf die
AKTIVEN FAHRZEUGE (Reihenfolge aus dem Bereich Fahrzeuge; Satzteile hinter-
einander = Originalsatz). Panel „Verteilung auf Fahrzeuge" (Tabelle: Fahrzeug,
Funkrufname, Satzteil, Zeichen) ersetzt „Verteilung auf Stationen"; neue
Kontrollübersicht (Gesamt-/verteilte Zeichen, aktive Fahrzeuge, Status). Storage
funkuebung_loesungssatz_v1 jetzt { phrase, mode:"vehicles" } (alt "stations" wird
toleriert/normalisiert). Statuslogik: open=kein Satz, prog=Satz ohne aktive
Fahrzeuge, done=Satz + ≥1 aktives Fahrzeug. Sichtbarer Inhalt wird per escapeHTML
escaped. Zukunftshinweis: stationsweise Freigabe mit Positionscode W1P3 folgt im
Bereich ELW-Koordination (hier NICHT umgesetzt). Übrige Logik unverändert.

Davor (1.15.1, Lösungssatz – Text): Standard-Lösungssatz endet jetzt mit „!"
(„ORDENTLICHER FUNK SCHAFFT KLARE LAGEN IN NEUHOF!"). Reine Textänderung.

Davor (1.15.0, Neuer Bereich „Lösungssatz"): Zwischen Routenplanung und QR-Code-Plan
ergänzt. Ein globaler Lösungssatz (Storage funkuebung_loesungssatz_v1, { phrase, mode:
"stations" }) wird BLOCKWEISE und gleichmäßig auf die vorhandenen Stationen verteilt
(stationsbezogen; St1..N hintereinander = Originalsatz). Live-Vorschau beim Tippen,
dauerhaft via „Speichern"; „Standard einsetzen"/„Feld leeren" ändern nur das Feld.
Leerzeichen werden als „␣" dargestellt. Dashboard-Status/-Fortschritt integriert
(open/prog/done). Bestehende Stations-/Routen-/Fahrzeug-/QR-Logik unverändert.

Davor (1.14.2, Routenplanung – Abstände): Gleichmäßiger vertikaler Abstand (18px)
zwischen Kartenbereich, Stationspositionen und Fahrzeugrouten ergänzt (vorher ohne
Abstand → gequetscht). Reine Layout/CSS-Änderung.

Davor (1.14.1, Text): Überschrift im Routenplanungs-Panel von „Stationspositionen /
ELW-Übersicht" auf „Stationspositionen" gekürzt. Reine Textänderung.

Davor (1.14.0, Lagebilder – Lightbox): Klick auf das kleine Vorschaubild eines
Lagebilds öffnet eine große Vorschau (Overlay/Lightbox). Schließen per X-Button,
Klick auf den Hintergrund oder Escape. Reines Anzeige-Feature, keine Storage-/
Logikänderung.

Davor (1.13.6, Lagebilder – Aufräumen): Das Titel-Eingabefeld je Lagebild wurde
entfernt; es bleiben Vorschau, Bild-URL-Feld und (bei 2+ Lagebildern) die
Fahrzeug-Zuordnung. Das title-Datenfeld bleibt aus Kompatibilitätsgründen erhalten
(nicht mehr editierbar). Reine UI-Bereinigung, keine Logik-/Storage-Strukturänderung.

Davor (1.13.5, ELW-Übersicht – Aufräumen): Der erklärende Hinweistext unter der
Überschrift „Stationspositionen / ELW-Übersicht" wurde entfernt. Reine Anzeige-
Bereinigung, keine Logikänderung.

Davor (1.13.4, Fahrzeugrouten – Aufräumen): Die Kopfzeile „Start: … · N Stationen"
je Fahrzeugkarte wurde entfernt; die Reihenfolge steht ohnehin in der nummerierten
Liste darunter. Reine Anzeige-/CSS-Bereinigung, keine Logikänderung.

Davor (1.13.3, Streckenübersicht – Aufräumen): Die Grundroute-Zeile (Station 1 → …)
wurde aus der Streckenübersicht entfernt; die Stationsanzahl ist über „Stationen
gesetzt: X von Y" ablesbar. Reine Anzeige-/CSS-Bereinigung, keine Logikänderung.

Davor (1.13.2, Layout – Vollbreite + Kartenbereich): Alle Bereiche nutzen die volle
Bildschirmbreite (Deckelung max-width 1080px entfernt). Routenplanung: links
Kartensteuerung + Streckenübersicht (gestapelt), rechts die große Karte; die Karte ist
durch Grid-Stretch genau so hoch wie die linke Spalte (gleichmäßig). Unter 860 px
einspaltig. Reine Layout/CSS-Änderung.

Davor (1.13.1, Routenplanung – Layout): Kartenbereich zweispaltig, Karte quadratisch.

Davor (1.13.0, Routenplanung – Bedienung): Neuer Button „Alle Marker entfernen" in
der Kartensteuerung – löscht mit Sicherheits-Rückfrage die Koordinaten ALLER
Stationen (Adressen bleiben); Marker, Radiuskreise und berechnete Route verschwinden.
Nutzt den vorhandenen Pfad afterPositionChange(); keine Logik-/Storage-Änderung.

Davor (1.12.1, Routenplanung – Aufräumen): Die technische Zusammenfassungszeile
(#routeSummary) unter der Streckenübersicht wurde entfernt – die Informationen
stehen bereits an besseren Stellen (Streckenübersicht, ELW-Übersicht, Dashboard).
renderRouteSummary() samt Aufrufen entfernt; keine Logik-/Storage-Änderung.

Davor (1.12.0, Routenplanung – interaktive Karte): Stationen werden per Klick auf
einer Leaflet/OpenStreetMap-Karte positioniert (Marker setzt lat/lng automatisch).
Globaler GPS-Radius (funkuebung_routen_settings_v1, Standard 65 m) als Kreis je
Marker. Grundroute Station 1 → … → letzte Station (ohne Rückweg) wird per OSRM-
Straßenrouting mit Strecke/Fahrzeit berechnet; bei Routingfehler Fallback auf
Luftlinie (deutlich als geschätzt markiert). Stationspositions-Tabelle wird zur
ELW-Übersicht (Nr.|Station|Adresse|Koordinaten|Status): Adresse manuell, Koordinaten
nur Anzeige. Automatische, zyklisch versetzte Fahrzeugrouten unverändert.

Davor (1.11.1, Text): SECTIONS-Beschreibung der Dashboard-Karte „Routenplanung"
präzisiert („Stationspositionen pflegen und automatisch versetzte Fahrzeugrouten
anzeigen."). Reine Textänderung, keine Logik/Layout.

Davor (1.11.0, Routenplanung – automatische Routen): Fachliche Korrektur des
Routenmodells. Statt manueller Routenpflege fahren jetzt ALLE aktiven Fahrzeuge
ALLE Stationen an; nur der Startpunkt ist je Fahrzeug zyklisch versetzt
(Fahrzeugindex i ⇒ Stationsliste um i gedreht, Modulo bei mehr Fahrzeugen als
Stationen). Routen werden beim Rendern berechnet – KEIN Routen-Storage mehr
(funkuebung_routen_v1 entfällt), keine Auswahl-/Hinzufügen-/↑↓-/Entfernen-Bedienung.
Neue Helfer buildAutoRouteForVehicle(index, stations) und getRouteForVehicle(id,
berechnet). Dashboard-Status jetzt positionsbasiert (open ohne Station/aktives Fzg.;
done wenn alle Stationspositionen vollständig; sonst prog). Zusammenfassung: Aktive
Fahrzeuge · Stationen je Fahrzeug · Automatische Routen · Positionen vollständig.
Stationspositionen (position{address,lat,lng}) unverändert. Keine Karten/API/Datei.

Davor (1.10.0, Routenplanung): erste funktionale Version mit manueller Routenpflege.

Davor (1.9.1, Layout/Text): Spaltenbreiten nachjustiert – Fahrzeugtabelle
Aktiv/Nr./Fahrzeug/Funkrufname/Min./Max. = 7/6/18/47/11/11 %, Stationsliste
Nr./Bezeichnung/Aufgabe-kurz/Lagebilder/Aktionen = 6/28/42/10/14 %. Die
Min./Max.-Eingabefelder werden zusätzlich per max-width:64px begrenzt und
zentriert, damit sie trotz Spaltenbreite kompakt bleiben. Text korrigiert:
Fahrzeug-Beschreibung „Kurzbezeichnungen" → „Personenrahmen" (Bereich und
Dashboard-Karte). Reine Layout-/Textänderung, keine Logik.

Davor (1.9.0): Versions-Baseline nachgezogen. Seit 1.8.1 sind sichtbare
Bedienerweiterungen hinzugekommen – Lagebild-Fahrzeug-Zuordnung je Station,
erweiterte Stations-Statuslogik (Aufgabe + Lagebild-URL je Bild + ab 2
Lagebildern je Bild mindestens ein Fahrzeug) sowie Entfall des Aktiv-
Schalters der Stationen. Nach der Versionsregel ist das eine Minor-Version.
Enthält außerdem die Layout-Anpassung der Spaltenbreiten: Stationsliste
(Nr./Bezeichnung/Aufgabe-kurz/Lagebilder/Aktionen = 7/35/36/10/12 %,
table-layout:fixed via colgroup) und Fahrzeugtabelle (Aktiv/Nr./Fahrzeug/
Funkrufname/Min./Max. = 8/6/16/36/17/17 %). Keine neue Logik in diesem Schritt.

Davor (1.8.1, Bereinigung): Kommentare/Beschreibungen aktualisiert (Script-
Übersicht, SECTIONS-Text Stationsplanung, updateStatus-Hinweis); neutrale
Klasse "section-summary" für Fahrzeug- und Stationszusammenfassung (Optik
unverändert). Keine Funktionsänderung.

Davor (1.8): Stationsplanung als flexible Verwaltung – Liste (Aktiv, Nr. =
Position, Bezeichnung, Aufgabe-kurz, Lagebild-Anzahl, Aktionen oben/unten/
löschen), "Station hinzufügen", Detailbereich (Bezeichnung, Aufgabe als
großes Textfeld, Kurzbeschreibung) und Lagebilder je Station (Titel, URL,
Vorschau). Inhalt only – Standort/Adresse/Koordinaten folgen in der Routen-
planung. Speicherung funkuebung_stationen_v1 (altes Format wird migriert),
Reihenfolge = Array-Reihenfolge. Status: Offen/In Bearbeitung/Vollständig
nach Aufgabe je aktiver Station. Fahrzeug-Funkrufnamen in Kurzschreibweise
(z. B. "Florian Neuhof 1/11/1").

Davor (1.7.3): Funkrufnamen-Format, feste Felder aus Vorgaben, Spalten­breiten.

Davor (1.7.2): Fahrzeug-Kurzname entfernt; Fahrzeug/Funkrufname feste Texte.

Davor (1.7): Bereich "Stationsplanung" als editierbare Standard-Tabelle (acht
feste Stationen, Nummern 1–8 fest). Spalten Aktiv | Nr. | Bezeichnung |
Standort/Adresse | Kurzbeschreibung; aktiv/inaktiv per Toggle, Speicherung
unter funkuebung_stationen_v1, Statuskarte (Offen/Vollständig) +
Zusammenfassung "Aktive Stationen: X von 8".

Davor (1.6.1): Fahrzeuge-Tabelle + Validierung Min./Max. Personen.

Davor (1.5): Pflichtfeld-Sterne + Hinweis, nummerierte Funkkanäle, APP_INFO;
dreigeteilte Kopfzeile, Handbuch-Overlay, Button-System, Funkkanal-Matrix.

---

## station.html (Teilnehmer-/QR-Ansicht)

> Eigene Versionierung, unabhängig von `APP_INFO.version` des Planungstools
> (zentral in `STATION_APP_INFO.version`, im Footer sichtbar).

Stand 1.0.14 (Freischalt-Vor-Gate – TESTFUNKTION, nur bei gepflegten Rätseln):
Ist für eine Fahrzeug-/Stationskombination ein FHZ-Rätsel und/oder ein ELW-Wort
hinterlegt (`assignments[fhz][s].riddleFhz` / `hashElw` aus data/uebung.json),
zeigt station.html oberhalb des Lösungszeichens eine Karte „Freischaltung": das
FHZ-Rätsel (die Besatzung löst es vor Ort und funkt das FHZ-Wort an die ELW) und
ein Eingabefeld für das von der ELW herunter­gefunkte ELW-Wort. Erst wenn dieses
Wort stimmt (Verschleierungs-Hash-Vergleich, case-/leerzeichen-tolerant), wird das
bisherige Positionscode-Feld sichtbar. Fehlt `hashElw`, erscheint das Code-Feld
direkt wie bisher (rückwärtskompatibel). Lösungszeichen-Logik unverändert; kein
Lösungssatz im Klartext.

Stand 1.0.13: Lagebild-Lightbox – Tipp/Klick auf ein Lagebild öffnet es als
Vollbild (max. Bildschirmgröße, dunkler Hintergrund). Schließen per Tipp/Klick
irgendwo im Overlay, ×-Button oder Escape. Funktioniert auf Tablet/Handy (Tap =
click) wie am Desktop; Bild ist per Tastatur fokussierbar (Enter/Leertaste).
Reine Anzeige-Erweiterung, keine Änderung an Daten/Positionscode-Logik.

Stand 1.0.12: Positionscode-Format auf W<Wortnummer>P<Zeichenposition> umgestellt
(Erzeugung im Planungstool; station.html vergleicht weiterhin nur als String).
Obsoleten Fallback laufnrFromCode entfernt – Laufnummer kommt direkt aus
assign.laufnr (wird immer exportiert); fehlt sie, klare Fehlermeldung.

Stand 1.0.11: Aufraeumung – ungenutzte CSS-Regel .code-hint entfernt (der
Hinweistext wurde in 1.0.9 entfernt). Keine sichtbare Aenderung.

Stand 1.0.10: Aufgabenkarte optisch feiner abgestimmt – Beschreibungstext
(station.task) etwas kleiner, ruhigere Farbe (text-muted) und mehr Abstand zum
hervorgehobenen Aufgabentitel (station.title). Nur Optik, keine Logikaenderung.

Stand 1.0.9: Aufgabe und Aufgabenbeschreibung in EINER Karte "Aufgabe" zusammen-
gefuehrt (Titel hervorgehoben, darunter optional der ausfuehrliche Text; kein
Platzhalter bei leeren Feldern). Positionscode-Hinweistext entfernt (Platzhalter
im Eingabefeld genuegt). Code-Pruefung/Freischaltung unveraendert.

Stand 1.0.8: Begriffe an das Planungstool angeglichen. Neuer Block "Aufgabe"
(kurzer Titel, intern title) wird oben angezeigt, sofern hinterlegt; die bisherige
"Aufgabe"-Karte heisst jetzt "Aufgabenbeschreibung" (ausfuehrlicher Text, intern
task). Fehlt der Titel, wird der Aufgabe-Block ausgeblendet.

Stand 1.0.7: Ist fuer das Fahrzeug kein Lagebild hinterlegt, wird der Lagebild-
Kartenbereich komplett ausgeblendet (statt Hinweistext); die Aufgabe-Karte nimmt
dann die volle Breite ein.

Stand 1.0.6: Stationskopf umgestellt – grosse weisse Ueberschrift zeigt jetzt die
Adresse/Einsatzort (aus der Routenplanung), die 2. Zeile wieder Fahrzeug + Funk-
rufname; rote Kachel zeigt weiterhin die Laufnummer.

Stand 1.0.5: Einsatzort-Fallback erweitert (address -> location -> ort -> einsatzort
-> title -> neutral).

Stand 1.0.4: Teilnehmeranzeige zeigt jetzt die LAUFNUMMER der echten Station in
der Fahrzeugroute (Station 1..N) statt der echten Stationsnummer/Bezeichnung; die
2. Kopfzeile zeigt Fahrzeug · Einsatzort/Adresse der echten Station (Fallback:
Adresse → Titel → neutral). Laufnummer aus assign.laufnr oder aus dem Positions-
code abgeleitet; ohne Ermittelbarkeit klare Fehlermeldung. Echte Stationsnummer
bleibt intern für Aufgabe/Lagebild/Code/Lösung erhalten.

Stand 1.0.3: Bildunterschrift des Lagebilds (z. B. „Lagebild 1") wird nicht mehr
angezeigt (title bleibt nur als alt-Text).

Stand 1.0.2: Titel in der Kopfzeile nicht mehr fett – gleiche Optik wie Datum
und Uhrzeit.

Stand 1.0.1: Kopfzeile wieder kompakt einzeilig (Titel · Datum · Uhrzeit), Datum
als TT.MM.JJJJ. Werte aus data/uebung.json → meta.

Stand 1.0.0: Neutraler Positionscode-Hinweis (ohne Formatbeispiel),
Versionsanzeige im Footer, Positionscode-Freischaltung des Lösungszeichens.

---

## elw.html (ELW-Koordination)

> Eigene Versionierung (zentral in `ELW_APP_INFO.version`, im Footer sichtbar).
> Lesende Schwesterseite zu station.html für ELW/Übungsleitung; lädt
> data/uebung.json. Zeigt NIEMALS Lösungssatz/Lösungszeichen (nur Positionscodes).

Stand 1.1.0 (FHZ-Wort-Gate vor dem Positionscode – TESTFUNKTION): Ist für eine
Fahrzeug-/Stationskombination ein `hashFhz` hinterlegt, erscheint statt „Code
anzeigen" ein kleines Eingabefeld „FHZ-Wort" + „Code freigeben". Erst wenn das von
der Besatzung herauf­gefunkte FHZ-Wort stimmt (Verschleierungs-Hash, case-/
leerzeichen-tolerant), wird der Positionscode eingeblendet. Zusätzlich wird – falls
vorhanden – das `riddleElw` als „ELW-Rätsel" je Route-Eintrag angezeigt (die ELW
löst es selbst und funkt das ELW-Wort an die Besatzung; geprüft auf station.html).
Ohne `hashFhz` bleibt „Code anzeigen" wie bisher. Weiterhin KEIN Lösungssatz/`char`.

Stand 1.0.0: Neue Koordinationsseite. Übungskopf (Name/Datum/Uhrzeit, Übungsleitung)
+ Funkkanäle (TMO/DMO aus meta.channels; Fallback-Hinweis, wenn der Export älter als
index v1.31.0 ist). Je aktivem Fahrzeug eine Karte mit der Route nach Laufnummer:
Laufnummer · Einsatzort (echte Stationsnummer + Aufgabe) · Positionscode (verdeckt,
per „Code anzeigen") · Status „Auftrag erteilt" → „erledigt". Status nur LOKAL je
Gerät (localStorage `funkuebung_elw_status_v1`, je Übung getrennt über name+datum),
Reset je Fahrzeug. Optionaler Filter `?fhz=<id>`. Gleiche dunkle Optik/Design-Tokens
wie station.html; kein Lösungszeichen, keine Online-Übertragung, keine Änderung an
index.html-Logik oder data/uebung.json.
