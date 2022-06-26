# Unterschied ACID und BASE

ACID -> Konsistenz der Daten je nach level garantiert, Vertikal Skalierbar (MySQL), Einfache Komplexität und Implementierung
BASE -> Konsistenz nicht garantiert, Horizontal skalierbar (MongoDB), Hohe Komplexität und Implementierung

# CAP Theorem anhand von einem DBMS erklärt

3 Punkte: Consistency, Availability und Partition Tolerance, nur 2 können gleichzeitig gegeben sein. MongoDB als Beispiel: A und P sind befolgt im Bsp., C nicht, heisst: Daten sind überall und das System funktioniert weiter, auch wenn einzelne Knoten ausfallen. Der Nachteil: Daten welche von verschiedenen Nodes abgerufen werden sind nicht immer gleich. 

# Transaktionen, Anomalien, Zusammenhang Performance

Transaktion: Folgerung von SQL Anweisungen welche als Einheit ausgeführt wird. (Commit). Entweder Ganz oder Nicht :)

Probleme im ACID Prinzip (Dirty Read, Lost Updates, Non-Repeatable Reads, Phantom Read). Je nach Einstellung hat man bessere oder schlechtere Performance: Serializable ergibt die schlechteste Performance aber die sichersten Transaktionen, Read Uncommitted bietet eine höhere Performance mit dem Risiko von allen 4 Anomalien. 
 
# mysql_secure_installation

Root Passwort setzen, Root Accounts welche von aussen erreichbar sind löschen, anonyme Accounts löschen, Test DBs löschen. Geschieht nach Installation von MySQL

# Linux in Kombination mit MySQL-Versionen

Je nach Version von z.B. Ubuntu Server werden verschiedene MySQL Versionen ausgeliefert. Wenn erwünscht, kann das Repository von MySQL selbst verwendet werden um die offiziell, neuste unterstützte Version für sein System zu erhalten. 

# Storage Engines

InnoDB und MyISAM -> Ist die Softwarekomponente, welche bestimmt wie Daten in einer DB erstellt, gelesen, aktualisiert und gelöscht werden. InnoDB ACID kompatibel, MyISAM nicht, dafür teils schneller als InnoDB. MyISAM keine Transaktionen, keine PK, FK. 

# Wo werden MySQL Daten gespeichert

/var/lib/mysql, wird per datadir Variable im Config File definiert. /etc/mysql/my.cnf ist das Configfile :)

# FK und PK Indexierungen

Ein FK referenziert auf einen PK. PKs sind dabei Indexiert um eine höhere Leistung zu erlangen. FK und PK ist Sinnvoll für Joins. 

# Datentypen JSON, ENUM, XY

JSON -> So kann man Objekte und Arrays in MySQL speichern, Key-Value Pairs möglich. (In einem Feld) Kann verwendet werden für spärliche Daten oder benutzerdefinierte Attribute. 

ENUM -> Bis zu 65k Wert, eine Auswahlliste, nur eines davon kann ausgewählt werden. Kann bei Auswahllisten verwendet werden. (Z.B. man kann einen Wochentag auswählen)

XY -> Keine Ahnung, numerisch auf jeden Fall.

# Vorgehen Import und Export MySQL

Export via mysqldump -u benutzer -p pw -existing_db > output_db

Import via mysql -u benutzer -p pw neue_db < alte_db

CSV Export könnte z.B. für DBMS Migrationen verwendet werden. 

# Backupvarianten

Cold -> Daten nicht verfügbar während dem Backup
Warm -> Daten teilweise / reduziert verfügbar
Hot -> Daten stehen voll zur verfügung

Logisch -> Menschlich lesbare Form, zb. mysqldump mit allen Befehlen
Physisch -> Binäre Form, Datei kopieren 

# Migrationsvorgehen

Import und Export kombiniert mit SSH und / oder Fifo als temporäre Datei um so zwischen Systemen schnell migrieren zu können

# Angriffsvektoren

- Missbrauch Benutzerkonten
- Direkter Zugriff auf DB durch Benutzer
- Keine Verschlüsselung auf Datastreams
- MySQL Injections

# Views, Triggers, Stored Procedures

View: Komplizierte SELECTS können in einem View gespeichert werden, Sicherheit kann so erhöht werden, View kann angepasst werden anstatt die Query der Applikation
Triggers: Kann Vor / Nach einem Insert, Delete oder Update automatisch durchgeführt werden. 
Stored Procedures: Queries werden gruppiert um sie nicht immer einzeln ausführen zu müssen. Es kann auch die Sicherheit und Performance erhöht werden.

# Schlechte Query, Performance Verbesserungen

- Indexierungen von Attributen bei WHERE, ORDER BY, GROUP BY
- UNIONS bei WHERE oder OR verwenden, Full Table Scans verhindern
- Wildcards vermeiden, FULLTEXT verwenden
- Keine Redundante Daten, Sinnvolle Datentypen, NULL Werte vermeiden, bei Joins keine SELECT *, Nicht zu viele Spalten, Attribute
- Caching von Abfragen im Config File aktivieren

# Vor- Nachteile MongoDB

- Skalierbar, Hochverfügbar
- Schemas Dynamisch, Keine fixe Struktur
- Hohe Performance
- Gut für unstrukturierte Daten
- High-Level APIs
- Grosse Datenmengen

- Nicht ACID Kompatibel
- Generelle Probleme mit verteilten Systemen
- Mehr Speicherverbrauch
- Kaum Optimierungen für NoSQL Engines, keine Abstraktion

# Begrifflichkeiten MongoDB vs MySQL

- SELECT -> find
- INSERT -> insertOne, insertMany
- Table -> Collection
- Tupel / Reihe -> Dokument
- Column -> Field
- Table Joins -> Embedding

# Vorgehen Import und Export MongoDB

Export: mongodump --archive=pfad --db=datenbank
Import: mongorestore --archive=pfad

Kann auch mit URI verwendet werden um einen bestimmten Host, Login, Port, Pfad auszuwählen

# Benutzerverwaltung Konzept MongoDB

Benutzer werden im normalfall direkt in der DB erstellt, in welcher sie gebraucht werden. Dabei haben die User verschiedene rollen welche bestimmen, welche Aktionen ein User durchführen kann. Die Admin DB ist für globale Administratoren, einzelne DBs für DB Administratoren.

# Konzept von URIs (DBMS bzw. MongoDB)

Aufbau:
protokoll://username:password@host:port/path?query#fragment

MongoDB: mongodb://applicationuser:AdminDB1$@localhost:27017/mfgdashboard

# Designumsetzungen

Embedding oder Referencing? Embedding sollte meist verwendet werden, Referencing selten der Fall. Referencing (lookup) kann Sinn machen bei öfterem ändern von Beziehungen. So muss man nur einen Wert ändern, bei Embedding müsste man das ganze kopieren / löschen. 

# Sharding und Replication

Replication: Master, Slave prinzip, Master hat Schreibe Rechte, Slaves im normalfall nur read, springen ein wenn der Master ausfällt. Es herrscht demokratie, heisst: Die Slaves wählen einen neuen Master.

Sharding: Auf verschiedenen Nodes sind die Daten verteilt, der Router leitet Anfragen an die richtigen Nodes weiter mittels Config Server welcher besagt wo was ist. 

# Vorgehen schlechte Query Performance (explain und Indexierung)

Mit dem Explain kann man sehen, was bei einer Anfrage geschieht. So kann man z.B. herausfinden mittels wie viele Dokumente gescannt werden, wo Index nötig sind um die Anfragen zu verbessern.
Ansonsten JS Funktionen verwenden, welche die Performance auch steigern können. 

# Sharding und Replication MySQL

- Replikation über das replizieren von Binary Logs (Transaktionen)
- READ und WRITE getrennt
- Proxy vor den MySQL Instanzen
- Sharding kompliziert, kann direkt in der Applikation umgesetzt werden


