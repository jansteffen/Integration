# Integrationskonzept für Effizienzmaßnahmen enerchart
Integration von einem Maßnahmenmanagement System in enerchart

## Neue/Geänderte Menüpunkte

![MenüScreenshots](img/MenüScreenshot.png)


1. *Neu*: Energieträger (Sichtbarkeit für Nutzer benötigt neue Berechtigung)
2. *Neu*: Effizienzmaßnahmen (Sichtbarkeit für Nutzer benötigt neue Berechtigung)
3. "Maßnahmen und Notizen" umgewidmet zu "Notizen"

Offene Frage: Gleiche Berechtigung für beides oder separat?

## 1. Energieträger

Der Menüpunkt "Energieträger" führt zu Übersicht von Energieträgern mit AG-Grid mit zwei Ansichten: Standard-Tabelle und Kachelansicht. Bei leerer Liste wird eine Erläuterung angezeigt.

#### Neue Datenstruktur "Energieträger":

|Spalte|Typ|Sichtbarkeit in Übersicht
|---|---|---|
|id|`integer` (Primärschlüssel)|❌|
|Name|`String`|✅|
|Beschreibung|`String`|❌|
|Energieträger|`Enum` (Strom/Öl/Kohle/...)|✅|
|Preisentwicklung|Verweis unsichtbarer Datenpunkt (Typ Währung)|✅ (Nur neuster Wert)|
|Preisentwicklung Datenquelle|Verweis Datenpunkt oder leer für manuelle Eingabe|❌|
|CO2-Ausstoß pro kWh|Verweis unsichtbarer Datenpunkt (Typ Ausstoß kg)|❔ (Nur neuster Wert)|
|CO2-Ausstoß Datenquelle|Verweis Datenpunkt oder leer für manuelle Eingabe|❌|
|Brennwert|Verweis unsichtbarer Datenpunkt (Typ Leistung kWh)|❔ (Nur neuster Wert)|
|Brennwert Datenquelle|Verweis Datenpunkt oder leer für manuelle Eingabe|❌|
|Letzte Änderung|`Date`|❔|
|Erstellt am|`Date`|❔|
||||

❌ = Nicht Sichtbar    
✅ = Immer Sichtbar     
❔ = Optional

Für Preisentwicklung, CO2-Ausstoß und Brennwert werden im Hintergrund für den Benutzer nicht sichtbare Datenpunkte verwendet. Die Werte dafür können entweder manuell vom Benutzer angegeben werden, oder von einem anderen vom Benutzer ausgewählten Datenpunkt vom gleichen Typ bezogen werden.

Kachelansicht ähnlich wie Kachelansicht von Umrechnungsfaktoren, mit Name, Energieträger + entsprechendem Icon und aktueller Preis:

![Kachelansicht von Energieträgern](img/Datenträger.png)

Anforderung:
* Jeder Energieträger benötigt ein Icon

### Pop-Up Dialog "neuer Energieträger anlegen"

1. Name (Textfeld)
2. Energieträgertyp (Dropdown Menü)
3. Preis, zwei Möglichkeiten:
    1. Fester Wert (Nummernfeld mit angezeigter Einheit Währung, Wert muss positiv sein)
    2. Verknüpfen mit Datenpunkt (Typ Wärung)
4. CO2-Ausstoß, zwei Möglichkeiten (Wie Preis, nur mit CO2 als Typ)
5. Brennwert, zwei Möglichkeiten (Wie Preis, nur mit kWh als Typ)

### Pop-Up Dialog "Energieträger Bearbeiten"

Ähnlich zu Energieträger anlegen, aber Energieträgertyp kann nicht mehr geändert werden.
Update/Löschen Buttons.
Löschen nur möglich wenn in keiner Maßnahme verwendet

Ausbaumöglichkeit: Archivieren btw als inaktiv markieren von Energieträgern?

## 2. Effizienzmaßnahmen


Menüpunkt Effizienzmaßnahmen führt zu Übersicht von Effizienzmaßnahmen, mit denen der eingeloggte Benutzer verbunden ist. Nutzer kann auch auf eine Ansicht wechseln, die alle Maßnahmen anzeigt, unabhängig ob er selbst damit verbunden ist.

Darstellung der Liste mit AG-Grid Standard-Tabelle oder Kachelansicht. Bei leerer Liste wird eine Erläuterung angezeigt.

#### Neue Datenstruktur Effizienzmaßnahme:

|Spalte|Typ|Sichtbarkeit in Übersicht
|---|---|---|
|id|`integer` (Primärschlüssel)|❌|
|Name|`String`|✅|
|Beschreibung|`String`|❌|
|Status|`Enum` (Entwurf/Planung/Warten auf Umsetzung/In Umsetzung/Kontrolle/Abgeschlossen)|✅|
|Priorität|`Enum` (Hoch/Mittel/Niedrig)|✅|
|Zuständiger Bearbeiter|Verweis Mitarbeiter|✅|
|Zuständiger Kontrolleur|Verweis Mitarbeiter|❔|
|Erstellt von|Verweis Mitarbeiter|❔|
|Benachrichtigungsgruppe|Verweis Benachrichtigungsgruppe|❌|
|Datum der Realisierung|`Date`|✅|
|Letzte Änderung|`Date`|❔|
|Erstellt Am|`Date`|❔|
|Verbundene Datenpunkte|Verweis Datenpunkte|❌|
|Verbundene Dokumente|Verweis Dokumente|❌|
|Titelbild|Verweis Bilddatei (Aus vorgegebener Liste oder selbst hochgeladen)|✅ (nur in Kachelansicht)|
|VALERI-Eingabefelder|Ganz viele floats...|❌|
||||

Detail-Ansicht von Maßnahme öffnet sich in einem Pop-up Dialog mit mehreren Tabs: (`kruFormWizard`, beispiel "Datenpunkt bearbeiten")

 1. Basisdaten
 2. Bearbeitungsprotokoll
 3. Energieeffizienz
 4. Wirtschaftlichkeit

Je nach Status der Maßnahme sind verschiedene Felder required oder ggf. Read-only.
Rechts vom Dialog-Popup werden über das ExtraActions Panel wichtige Aktionen wie der Statuswechsel angeboten.

 ### 2.1 Basisdaten

 ![Maßnahme Basisdaten Dialog](img/MaßnahmeDialog.png)
Anforderung: Möglichkeit Dateien hochzuladen bzw. aus bereits hochgeladenen Dateien auszuwählen

### 2.2 Bearbeitungsprotokoll
Um den Lebenslauf einer Maßnahme nachvollziehbar zu machen soll zu einer Maßnahme ein chronologisches Bearbeitungsprotokoll gehören. Das besteht zum einen Teil aus automatisch generierten Einträgen, welche Zustandsänderungen vermerken, und zum anderen Teil aus manuell geschriebenen Einträgen von Nutzern.

![Bearbeitungsprotokoll Bild](img/MaßnahmeDialog2.png)

* Eingabeformular für neuen Protokolleintrag
    * Großes Textfeld
    * Speichern-Button
* Chronologisches Protokoll bestehend aus von manuell erstellten Einträgen von Nutzern sowie automatisch generierten Einträgen (ausgelöst von Zustandänderungen)
    * Benötigt neue Datenstruktur Protokolleintrag (1:n Verhältniss von Effizienzmaßnahmen zu Protokolleintrag)

#### Neue Datenstruktur "Protokolleintrag"
|Spalte|Typ|
|---|---|
|id|`integer` (Primärschlüssel)|
|Zugehörige Maßnahme|Verweis Maßnahme|
|Protokoll Text|`String`|
|Systemnachricht?|`boolean`|
|Verfassungsdatum|`Date`|
|||

#### Ausbaumöglichkeit:
* Abeschätzter gesamtfortschritt, visualisiert als Fortschrittsbalken, bei Erstellung eines neuen Protokolleintrags kann die Schätzung geändert werden.
* Dokumente/Fotos an Protokolleintrag hängen


### 2.3 Energieeffizienz

Für Kalkulationshilfe um Einsparung einer Maßnahme zu schätzen.
Einsparungen für Energieträger können hinzugefügt werden.
Zur Darstellung `EditDisplayBox` verwenden.

(TODO Bild)

Speichern entweder als eigene Datenstruktur oder als JSON-String innerhalb der Datenstruktur Effizienzmaßnahmen

#### Neue Datenstruktur "Energieersparniss"
|Spalte|Typ|
|---|---|
|id|`integer` (Primärschlüssel)|
|zugehörige Maßnahme|Verweis Maßnahme|
|Betroffener Energieträger|Verweis Energieträger|
|Einsparung|`float`|
|||

### 2.4 Wirtschaftlichkeit
* VALERI-Eingabe-Formular für Wahrscheinlichster Fall, Best-Case und Worst-Case (Wie in ESB)
* Zusatzfaktoren hinzufügen/entfernen/bearbeiten
    * Zur Darstellung `EditDisplayBox` verwenden.
    * Bearbeitung jeweils in separatem Pop-up
    * Benötigt neue Datenstruktur Zusatzfaktoren (1:n verhältniss von Effizienzmaßnahmen zu Zusatzfaktoren)
* Automatisch berechnete Cashflow-Tabelle und Ergebnisse der VALERI-Rechnung (Wie in ESB)

#### Neue Datenstruktur "Zusatzfaktor"
|Spalte|Typ|
|---|---|
|id|`integer` (Primärschlüssel)|
|zugehörige Maßnahme|Verweis Maßnahme|
|Bezeichnung|`String`|
|Einsparungsbetrag|`float`|
|Art der Preisteigerung|`Enum` (Energie, sonstige, keine)|
|Häufigkeit|`Enum` (Jährlich, periodisch, einmalig)|
|Periode|`integer`|
|stop nach wievielen Jahren|`integer`|
|||

(TODO Bild)

#### Ausbaumöglichkeit
* Graph-Darstellung des Cashflows

## 3. Notizen

Menüpunkt der ehemals "Maßnahmen und Notizen" hieß wird umgewidmet zu nur "Notizen". Hier muss noch recherchiert werden, wo Notizen überall verwendet werden.