# Integrationskonzept für Effizienzmaßnahmen enerchart
Integration von einem Maßnahmenmanagement System in enerchart

## Neue/Geänderte Menüpunkte

![MenüScreenshots](img/MenüScreenshot.png)


* Energieträger (Sichtbarkeit für Nutzer benötigt neue Berechtigung)
* Effizienzmaßnahmen (Sichtbarkeit für Nutzer benötigt neue Berechtigung)
* "Maßnahmen und Notizen" umgewidmet zu "Notizen"

## 1. Energieträger

Neue Datenstruktur "Energieträger"

Menüpunkt "Energieträger" führt zu Übersicht von Energieträgern mit AG-Grid mit zwei Ansichten: Standard-Tabelle und Kachelansicht. Bei leerer Liste wird eine Erläuterung angezeigt.

Standard-Tabellenansicht (mit (o) markierte Spalten sind optional): 

|Energieträger|Name|Aktueller Preis|Zeitpunkt letztes Update (o)|CO2 (o)|Brennwert(o)|
|---|---|---|---|---|---|

Kachelansicht ähnlich wie Kachelansicht von Umrechnungsfaktoren, mit Name, Energieträger + entsprechendem Icon und aktueller Preis

### Dialog "neuer Energieträger anlegen"

1. Name (Textfeld)
2. Energieträgertyp (Dropdown Menü)
3. Preis, zwei Möglichkeiten:

    3. Fester Wert (Nummernfeld mit angezeigter Einheit Währung, Wert muss positiv sein)
    2. Verknüpfen mit Datenpunkt (Typ Wärung)
4. CO2-Ausstoß, zwei Möglichkeiten (Wie Preis, nur mit CO2 als Typ)
5. Brennwert, zwei Möglichkeiten (Wie Preis, nur mit kWh als Typ)

Jeder Energieträger muss ein eigenes Icon haben.

### Dialog "Energieträger Bearbeiten"

Ähnlich zu Energieträger anlegen, aber Energieträgertyp kann nicht mehr geändert werden.
Update/Löschen Buttons

## 2. Effizienzmaßnahmen

Neue Datenstruktur Effizienzmaßnahme

Menüpunkt Effizienzmaßnahmen führt zu Übersicht von Effizienzmaßnahmen, mit denen der eingeloggte Benutzer verbunden ist. Nutzer kann auch auf eine Ansicht wechseln, die alle Maßnahmen anzeigt, unabhängig ob er selbst damit verbunden ist.

Darstellung der Liste mit AG-Grid Standard-Tabelle. Bei leerer Liste wird eine Erläuterung angezeigt.

|Name|Datum der Realisierung|Status|Priorität|Zuständiger für die Bearbeitung|Erstellt von (o)|Letzte Änderung (o)
|---|---|---|---|---|---|---|

Detail-Ansicht von Maßnahme öffnet sich in einem Pop-up Dialog mit mehreren Tabs:

 * Basisdaten
 * Bearbeitungsprotokoll
 * Energieeffizienz
 * Wirtschaftlichkeit

Je nach Status der Maßnahme sind verschiedene Felder required oder ggf. Read-only.

 ### 2.1 Basisdaten
* Beschreibung der Maßnahme (großes Textfeld)
* Verbunde Dokumente (Dokumente Picker... existiert das? Aus bereits hochgeladenen Dokumenten auswählen? Direkt hier hochladen?)
* Verbundene Datenpunkte (Datenpunkt-picker mit Icons)
* Verbundene Bilder (Bilder Picker... Existiert das? Hochladen?)
* Zuständig für die Bearbeitung (User Select, Read-Only ab Status "In Bearbeitung")
* Zuständig für die Kontrolle (User Select, Read-Only ab Status "In Bearbeitung")
* Erstellt von (User-Select, Wert automatisch erzeugt, Read-Only)
* Erstellt am (Datum Select, Wert automatisch erzeugt, Read-Only)
* Letzte Änderung am (Datum Select, Wert automatisch erzeugt, Read-Only)

### 2.2 Bearbeitungsprotokoll
* Chronologisches Protokoll bestehend aus von manuell erstellten Einträgen von Nutzern sowie automatisch generierten Einträgen (ausgelöst von Zustandänderungen)
    * Benötigt neue Datenstruktur Protokolleintrag (1:n verhältniss von Effizienzmaßnahmen zu Protokolleintrag)
* Eingabeformular für neuen Protokolleintrag

### 2.3 Energieeffizienz


### 2.4 Wirtschaftlichkeit
* VALERI-Eingabe-Formular für Wahrscheinlichster Fall, Best-Case und Worst-Case
* Zusatzfaktoren hinzufügen/entfernen/bearbeiten (kann jeweils in eigenem Pop-up bearbeitet werden)
    * Benötigt neue Datenstruktur Zusatzfaktoren (1:n verhältniss von Effizienzmaßnahmen zu Zusatzfaktoren)
* Automatisch berechnete Cashflow-Tabelle, Ergebnisse der VALERI-Rechnung und ggf. Graph



## 3. Notizen

Menüpunkt der ehemals "Maßnahmen und Notizen" hieß wird umgewidmet zu nur "Notizen". Hier muss noch recherchiert werden, wo Notizen überall verwendet werden.