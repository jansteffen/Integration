# Integrationskonzept für Effizienzmaßnahmen enerchart
Nachdem im vorigen Kapitel ein umfassendes Funktionskonzept erarbeitet wurde, muss nun ein Plan geschaffen werden, wie diese Funktionen am besten in die bereits bestehenden Software eingebaut werden können. Dabei sollten möglichst viele Bestandskomponenten, sowohl im Front-End und im Back-End, sinnvoll wiederverwendet werden. So wird nicht nur der Entwicklungsaufwand minimiert, sondern die Bedienung und aussehen werden dadurch auch konform mit anderen Teilen der Software.



## Neue/Geänderte Funktionsbereiche
Die Integration betrifft insgesamt drei Funktionsbereiche von enerchart, die jeweils über das Hauptmenü an der linken Seite des Bildschirms erreicht werden können. Der Zugang zu diesen Funktionsbereichen kann an zugeteilten Berechtigungen eines Nutzers gekoppelt werden, sodass die dazugehörigen Menüpunkte nur sichtbar werden, wenn der aktuelle Nutzer auch dazu berechtigt ist, diesen Funktionsbereich zu verwenden.

Unter den drei betroffenen Bereichen sind zwei völlig neu und ein bereits bestehender Bereich:

![MenüScreenshots](img/MenüScreenshot.png)


1. *Neu*: Energieträger (Sichtbarkeit für Nutzer benötigt neue Berechtigung)
2. *Neu*: Effizienzmaßnahmen (Sichtbarkeit für Nutzer benötigt neue Berechtigung)
3. "Maßnahmen und Notizen" umgewidmet zu "Notizen"

Offene Frage: Gleiche Berechtigung für beides oder separat?

# 1. Energieträger

Im allgemeinen versteht man unter "Energieträger" ein medium für Energie, also beispielsweise Strom, Öl oder Gas. Da diese unterschiedliche Eigenschaften wie Preis, CO2-Ausstoß und Brennwert aufweisen, soll nun in enerchart eine neue Datenstruktur eingeführt werden, welche diese Eigenschaften logisch bündelt.

Die Benutzer müssen dazu im System eine Liste ihrer aktiv verwendeten Energieträgern anlegen und diese mit Werten versehen. Es ist außerdem möglich, einen Typ von Energieträger, z.B. Storm, mehrfach hinzuzufügen und mit unterschiedlichen Werten zu versehen. Dadurch kann das Kaufen von Strom von mehreren Anbietern modelliert werden.

Der Menüpunkt "Energieträger" führt den Nutzer zu einer Übersicht von verwendeten Energieträgern. Diese Liste soll mithilfe der UI-Komponente "AG-Grid" dargestellt werden, mit zwei möglichen Ansichten: Standard-Tabelle und Kachelansicht. Falls die Liste noch leer ist, wird stattdessen eine Erläuterung dieses Funktionsbereiches angezeigt.

Die Kachelansicht soll eine optisch attraktive Darstellung der Einträge bieten. Typ-abhängige Icons helfen dabei, schnell optisch zu verarbeiten:

![Kachelansicht von Energieträgern](img/Datenträger.png)

Anforderung:
* Jeder Energieträger benötigt ein Icon

## 1.1. Datenstruktur für Energieträger
In der Datenbank muss einene neue Datenstruktur angelegt werden, um Energieträger zu führen:

|Spalte|Typ|Sichtbarkeit in Übersicht
|---|---|---|
|id|`integer` (Primärschlüssel)|❌|
|Name|`String`|✅|
|Energieträgermedium|`Enum` (Strom/Öl/Kohle/...)|✅|
|Preisentwicklung|Verweis unsichtbarer Datenpunkt (Typ Währung)|✅ (Nur neuster Wert)|
|Preisentwicklung Datenquelle|Verweis Datenpunkt oder leer für manuelle Eingabe|❌|
|CO2-Ausstoß pro kWh|Verweis unsichtbarer Datenpunkt (Typ Ausstoß kg)|❔ (Nur neuster Wert)|
|CO2-Ausstoß Datenquelle|Verweis Datenpunkt oder leer für manuelle Eingabe|❌|
|Brennwert|Verweis unsichtbarer Datenpunkt (Typ Leistung kWh)|❔ (Nur neuster Wert)|
|Brennwert Datenquelle|Verweis Datenpunkt oder leer für manuelle Eingabe|❌|
|Letzte Änderung|`Date`|❔|
|Erstellt am|`Date`|❔|
|aktiv|`boolean`|❔|
||||

❌ = Nicht Sichtbar    
✅ = Immer Sichtbar     
❔ = Optional

Für Preisentwicklung, CO2-Ausstoß und Brennwert werden im Hintergrund für den Benutzer nicht sichtbare Datenpunkte verwendet. Die Werte dafür können entweder manuell vom Benutzer angegeben werden, oder von einem anderen vom Benutzer ausgewählten Datenpunkt vom gleichen Typ bezogen werden.

## 1.2. Pop-Up Dialog "neuen Energieträger anlegen"

Der Nutzer kann einen neuen Eintrag in die Liste der Energieträger erstellen über die Schaltfläche "Hinzufügen" in der Aktionsleiste, welche über der Tabelle bzw. Kachelansicht sitzt. Dazu wird die enerchart Komponente `kruFormWizard` und dazugehörige `kruForm`-Elemente verwendet, mit denen ein Pop-up-Dialog erstellt und mit standardisierten Eingabeformular-Elementen befüllt werden kann. Der Dialog soll folgende Elemente aufweisen:

1. Name (Textfeld)
2. Energieträgermedium (Dropdown Menü, idealerweise mit Icons)
3. Aktiv (Checkbox, standardmäßig true)
3. Preis, kann über zwei Möglichkeiten angegeben werden:
    1. Direkt eingegebener Wert (Nummernfeld mit angezeigter Einheit Währung, Wert muss positiv sein)
    2. Beziehen der Werte von einem anderen Datenpunkt (vom Typ Wärung)
4. CO2-Ausstoß, zwei Möglichkeiten (Wie Preis, nur mit CO2 als Typ)
5. Brennwert, zwei Möglichkeiten (Wie Preis, nur mit kWh als Typ)

## 1.3. Pop-Up Dialog "Energieträger Bearbeiten"

Ein Energieträger kann bearbeitet werden, beispielsweise um ihn umzubenennen oder mit neuen Werten zu versehen.
Dazu wird auch wieder ein Pop-up Dialog verwendet, ähnlich wie "Energieträger anlegen", mit dem Unterschied das der Energieträgermedium nicht mehr geändert werden kann. Weiterhin gibt es einen "Löschen"-Button, allerdings ist das Löschen nur möglich wenn der Energieträger in keiner Maßnahme verwendet wird.

# 2. Effizienzmaßnahmen

Eine Energieeffizienzmaßnahme im Kontext der ISO-50001 bezieht sich auf eine gezielte Handlung oder Initiative, die darauf abzielt, den Energieverbrauch und die Energieeffizienz in einer Organisation zu verbessern.

Solche Maßnahmen können vielfältig sein und beispielsweise die Verbesserung von Produktionsprozessen, den Einsatz energieeffizienterer Technologien, die Optimierung von Gebäudeenergiesystemen oder die Schulung von Mitarbeitern zu bewusstem Energieverbrauch umfassen. Das Ziel besteht darin, den Energieverbrauch zu reduzieren, Kosten zu senken, Umweltauswirkungen zu minimieren und insgesamt die betriebliche Energieleistung zu optimieren.

In *enerchart* wird nun ein Managementsystem integriert, welches eine digitale Buchhaltung von Effizienzmaßnahmen ermöglicht. Der neue Menüpunkt "Effizienzmaßnahmen" führt zu einer Übersicht von Effizienzmaßnahmen, mit denen der eingeloggte Benutzer verbunden ist. Dadurch bildet sich eine Art "To-Do Liste", welche dem Benutzer alle für ihn anstehenden Aufgaben auf einen Blick überschaubar dargestellt werden. Für eine umfassendere Übersicht kann der Nutzer auch auf eine Ansicht wechseln, welche alle Maßnahmen anzeigt, unabhängig davon ob er selbst damit verbunden ist oder icht.

Die Darstellung wird auch wieder mittels AG-Grid realisiert, mit sowohl Tabellen- und Kachelansicht. Bei einer leeren Liste wird stattdessen eine Erläuterung/Hilfetext angezeigt.

## 2.1 Neue Datenstruktur "Effizienzmaßnahme":

|Spalte|Typ|Notiz|Sichtbarkeit in Übersicht
|---|---|---|---|
|id|`integer`|Primärschlüssel|❌|
|Name|`String`|Anzeigename|✅|
|Beschreibung|`String`||❌|
|Rubrik|`Enum`|Rein Organisatorisch, siehe ESB Rubriken|❔|
|Status|`Enum`|Entwurf, Planung, Warten auf Umsetzung, In Umsetzung, Kontrolle, Abgeschlossen|✅|
|Priorität|`Enum`|Hoch, Mittel, Niedrig|✅|
|Zuständiger Bearbeiter|Verweis Mitarbeiter|n:1|✅|
|Zuständiger Kontrolleur|Verweis Mitarbeiter|n:1|❔|
|Erstellt von|Verweis Mitarbeiter|n:1|❔|
|Benachrichtigungsgruppe|Verweis Benachrichtigungsgruppe|n:1|❌|
|Datum der Realisierung|`Date`||✅|
|Letzte Änderung|`Date`|Wert wird automatisch befüllt|❔|
|Erstellt Am|`Date`|Wert wird automatisch befüllt|❔|
|Verbundene Datenpunkte|Verweis Datenpunkte|n:m|❌|
|Verbundene Dokumente|Verweis Dokumente|n:m|❌|
|Bild|Verweis Bilddatei|n:1|✅ (nur in Kachelansicht)|
|VALERI-Eingabefelder|Ganz viele floats...|Siehe VALERI-Rechnung in ESB|❌|
||||

Detail-Ansicht von Maßnahme öffnet sich in einem Pop-up Dialog mit mehreren Tabs: (`kruFormWizard`, beispiel "Datenpunkt bearbeiten")

 1. Basisdaten
 2. Bearbeitungsprotokoll
 3. Energieeffizienz
 4. Wirtschaftlichkeit

Je nach Status der Maßnahme sind verschiedene Felder required oder ggf. Read-only.
Rechts vom Dialog-Popup werden über das ExtraActions Panel wichtige Aktionen wie der Statuswechsel angeboten.

 ## 2.2 Basisdaten

 ![Maßnahme Basisdaten Dialog](img/MaßnahmeDialog.png)
Anforderung: Möglichkeit Dateien hochzuladen bzw. aus bereits hochgeladenen Dateien auszuwählen

## 2.3 Bearbeitungsprotokoll
Damit der Lebenslauf einer Maßnahme auch rückwirkend nachvollziehbar ist, wird als Teil jeder Maßnahme ein chronologisches Bearbeitungsprotokoll geführt. Dieses besteht zum einen Teil aus automatisch generierten Einträgen, welche beispielsweise Zustandsänderungen vermerken, und zum anderen Teil aus manuell geschriebenen Einträgen von Mitarbeitern.

![Bearbeitungsprotokoll Bild](img/MaßnahmeDialog2.png)

* Eingabeformular für neuen Protokolleintrag:
    * Großes Textfeld
    * Speichern-Button
* Chronologisches Protokoll bestehend aus von manuell erstellten Einträgen von Nutzern sowie automatisch generierten Einträgen (ausgelöst von Zustandänderungen)
    * Benötigt neue Datenstruktur Protokolleintrag (1:n Verhältniss von Effizienzmaßnahmen zu Protokolleintrag)

### 2.3.1. Neue Datenstruktur "Protokolleintrag"
|Spalte|Typ|Notiz|
|---|---|---|
|id|`integer`|Primärschlüssel|
|Zugehörige Maßnahme|Verweis Maßnahme|n:1|
|Protokoll Inhalt|`String`||
|Systemnachricht?|`boolean`||
|Verfassungsdatum|`Date`||
||||

### 2.3.2. Ausbaumöglichkeiten:
* Abgeschätzter Gesamtfortschritt, visualisiert als Fortschrittsbalken. Bei Erstellung eines neuen Protokolleintrags kann die Schätzung geändert werden.
* Dokumente/Fotos an Protokolleintrag hängen.


## 2.4. Energieeffizienz
Der Reiter Energieeffizienz dient als Kalkulationshilfe um die monetäre Einsparung einer Maßnahme zu schätzen. Der Nutzer gibt an, wie viel von welchen Energieträgern eingespart wird, und basierend darauf wird ein Gesamtjahreswert von Euro und CO2 berechnet. Dieser Gesamtwert kann anschließend im Tab Wirtschaftlichkeit für d
Einsparungen für Energieträger können hinzugefügt werden.
Zur Darstellung `EditDisplayBox` verwenden.

(TODO Bild)

Speichern entweder als eigene Datenstruktur oder als JSON-String innerhalb der Datenstruktur Effizienzmaßnahmen

### 2.4.1. Neue Datenstruktur "Energieersparniss"
|Spalte|Typ|
|---|---|
|id|`integer` (Primärschlüssel)|
|zugehörige Maßnahme|Verweis Maßnahme|
|Betroffener Energieträger|Verweis Energieträger|
|Einsparung|`float`|
|||

## 2.5. Wirtschaftlichkeit
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

Bisher existiert in *enerchart* ein Funktionsbereich "Maßnahmen und Notizen", der sehr oberflächlich zur Verwaltung von Effizienzmaßnahmen dienen sollte. Die Funktionalität war aber darauf begrenzt, das einem Datenpunkt zu einem bestimmten Zeitpunkt eine Notiz angehängt, und die Maßnahme als "Durchgeführt" markiert werden kann.

Mit der Einführung des neuen, umfangreicheren Managementsystems für Effizienzmaßnahmen soll dieser Funktionsbereich nun in lediglich "Notizen" umgewidmet werden. Der Funktionsumfang bleibt vorerst gleich, sodass eine Migration von Bestandssystemen möglichst simpel ist.

Hier muss noch recherchiert werden, wo Notizen überall verwendet werden.