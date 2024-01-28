# Integrationskonzept für Effizienzmaßnahmen enerchart
Integration von einem Maßnahmenmanagement System in enerchart

## Neue/Geänderte Menüpunkte

![MenüScreenshots](img/MenüScreenshot.png)


* Energieträger (Sichtbarkeit für Nutzer benötigt neue Berechtigung)
* Effizienzmaßnahmen (Sichtbarkeit für Nutzer benötigt neue Berechtigung)
* "Maßnahmen und Notizen" umgewidmet zu "Notizen"

## 1.) Energieträger

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
