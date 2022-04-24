# Indexierung und Datentypen bei MySQL

## Datentypen / Attribute

In MySQL gibt es verschiedene Datentypen, ählich wie wir es aus Java bereits kennen.

Zwei Spezialtypen werde ich hier beschreiben:

### JSON

JSON kann als Objekt oder als Array verwendet werden. So kann im Array eine Liste von Werten, separiert von einem Komma gespeichert werden. Falls man es als Objekt verwendet so können Key-Value Paare gespeichert werden. Vorteile gegenüber dem Speichern vom JSON Format in Strings ist die automatische überprüfung wie auch eine Optimierung vom Speicherformat. 

### ENUM

ENUM ist ein String Objekt welches nur einen Wert besitzen kann welches von einer Liste von möglichen Werte ausgewählt wird. Es können maximal 65535 verschiedene Werte zur Auswahl stehen, falls etwas eingegeben wird welches nicht in dieser Liste vorkommt bleibt das Feld leer.

## Indexierung

### Erklärung

Eine Indexierung ist wichtig damit MySQL schnell Daten finden kann bei grossen Tabellen. Hierfür gibt es eine spezielle Index Datenstruktur welche alle Eintrage einer Spalte indexiert. Dies wird vorallem wichtig bei Tabellen mit mehr als 10k Einträgen und WHERE oder ORDER BY Statements oft verwendet verwendet werden.

### Typen

Für die Indexierung gibt es verschiedene Typen:

- Primary Key - Pro Tabelle ist nun ein Primary-Key möglich, dies ist typischerweise die ID-Spalte. Eine Doppelung ist ausgeschlossen.
- Unique - Sie sind generell gleich wie der Primary-Key, der einzige unterschied ist das es mehrere pro Tabelle geben kann.
- Index - Spalten welche mit Index gekennzeichnet sind, können Doppelungen enthalten im gegensatz zu Unique und Primary Key.
- Fulltext - Ist geeignet für längere Texte, jedes Wort wird hier indiziert. Es ist nur Sinnvoll dies zu wählen wenn man die Volltextsuche in MySQL verwendet.

### Vor- und Nachteile

Der Vorteil liegt bei dem schnellen auffinden von Datensätzen sofern in einer Spalte gesucht wird, welche indexiert ist.

Der Nachteil liegt bei dem Speicherplatzverbrauch (Ist aber kaum merkbar) und bei INSERT, UPDATE und DELETE welche jetzt ein wenig länger brauchen da der Index ebenfalls aktualisert werden muss. 

Generell kann man also sagen, das Indexing sehr hilfreich ist und verwendet werden sollte. 