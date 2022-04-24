# Woche 4

In der 4. Woche geht es um die Konfiguration und das verstehen von MySQL.

## Storage Engines

Eine Storage Engine ist eine zugrundeliegende Softwarekomponente die ein DBMS verwendet, um Daten einer Datenbank zu erstellen, lesen, aktualisieren und löschen. 

Die grösste und auch default Storage Engine von MySQL ist InnoDB. An zweiter Stelle kommt MyISAM, dies war vor Version 5.5 von MySQL die standard Engine. 

### InnoDB

- Zugriffe sperren nur eine Spalte anstatt einem ganzen Datensatz
- Transaktionen werden verwendet
- ACID Prinzip wird befolgt
- Relational mit Primär- und Fremdschlüsseln

### MyISAM

- Integrierte Volltextsuche
- Keine unterstützung für referenzielle integrität
- Zugriff in einzelnen Schritten (Keine Transaktionen)
- Keine kontrolle (ACID)
- Schnelle Inserts und Updates

### Installierte Engines

Kann man sich mithilfe vom SQL Statement "SHOW ENGINES\G" anzeigen lassen.

- Archive - Keine Transaktionen
- Blackhole - /dev/null Engine, alles wird gelöscht
- Mrg_myisam - Keine Transaktionen, Kollektion von identischen MyISAM Tabellen
- Federated - Keine Transaktionen
- MyISAM - Keine Transaktionen
- Performance_Schema - Keine Transaktionen
- InnoDB - Transaktionen, ACID
- Memory - Hash basiert im Memory, Keine Transaktionen
- CSV - Keine Transaktionen 

### Verwendung

Eine Storage Engine ist ein Modul von einem RDBMS. Sie ist dafür da das alle essentiellen Operationen richtig durchgeführt werden. Es übernimmt alle CRUD Aufgaben von einem RDBMS. Bedeutet also auf Tabellen bezogen, eine Storage Engine ist für erstellen, lesen, updaten und löschen zuständig. 

So ist InnoDB z.B. für sehr kritische Daten sehr geeignet da es nach dem ACID Prinzip funktioniert, eine Engine wie Memory ist sehr praktisch für einen Cache von einer Website z.B. da sie im RAM ist und sehr hohe Leistung bietet. 

## Benutzer & Berechtigungen

### Demo DB und Tabellen

Erstellen einer DB:
```CREATE DATABASE demodb```

Erstellen der beiden Tabellen:
```sql
CREATE TABLE Persons (
    PersonID int,
    LastName varchar(255),
    FirstName varchar(255),
    Address varchar(255),
    City varchar(255)
);
CREATE TABLE Pet (
    PetID int,
    Name varchar(255),
    Animal varchar(255),
    Age int
);
```

### Root Konfiguration

Der Root Benutzer wurde bereits bei der Installation konfiguriert mittels dem Script welches die Installation sicherer gemacht hat. 

### Regulärer Benutzer auf DB

Um den Regulären Benutzer zu erstellen folgendes eingeben:
```CREATE USER 'anwender'@'localhost' IDENTIFIED BY 'Admin1234$';```

Ich werde nur Lese-Berechtigungen auf die Datenbank vergeben welche gerade erstellt wurden. Dies funktioniert wie folgt:
```GRANT SELECT ON demodb.* TO 'anwender'@'localhost';```

Um dies alles in Kraft treten zu lassen:
```FLUSH PRIVILEGES;```

### Admin Benutzer auf DB

Ähnlich zum Regulären Benutzer hier der Admin: 
```CREATE USER 'admin'@'localhost' IDENTIFIED BY 'Admin1234$';```

Dieses mal aber mit vollen Berechtigungen:
```GRANT ALL PRIVILEGES ON demodb.* TO 'admin'@'localhost';```

Um dies alles in Kraft treten zu lassen:
```FLUSH PRIVILEGES;```

### Verifizierung

Zuerst mit dem normalen Benutzer einen Insert Probieren (Achtung, mit richtigem User einloggen!): 
```INSERT INTO Persons(LastName, FirstName, Address, City) VALUES ('Rissi', 'Hans', 'Lohstrasse 20', 'Kreuzlingen');```

Output: 
```ERROR 1142 (42000): INSERT command denied to user 'anwender'@'localhost' for table 'Persons'```

Jetzt noch als Admin der Output:
```sql
Query OK, 1 row affected (0.07 sec)

mysql> select * from Persons;
+----------+----------+-----------+---------------+-------------+
| PersonID | LastName | FirstName | Address       | City        |
+----------+----------+-----------+---------------+-------------+
|     NULL | Rissi    | Hans      | Lohstrasse 20 | Kreuzlingen |
+----------+----------+-----------+---------------+-------------+
1 row in set (0.00 sec)
```

Man sieht also, dass es richtig funktioniert. 

## Server Konfiguration

### Transaktions-Isolations LVL

Um dies einzusehen folgendes Statement verwenden, Output mitinbegriffen:
```sql
mysql> SELECT @@transaction_ISOLATION;
+-------------------------+
| @@transaction_ISOLATION |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+
1 row in set (0.00 sec)
```

Für uns bedeutet dies in diesem Fall das "Phantom Read" möglich ist. Das heisst also, dass Suchkriterien auf unterschiedliche Datensätze zutreffen aufgrund von laufenden Transkationen welche Datensätze entfernt, hinzugefügt oder verändert hat.

### System-Variablen

Um alle System-Variablen in einem schönen gefiltertem Output zu erhalten muss man folgenden Command verwenden:
```SHOW VARIABLES LIKE '%size%';```

Den Output habe ich in ein kleines File im Home von meinem Benutzer gespeichert. Es heisst "sql_variablen_output".

### Netzwerk-Konfiguration

Um dies zu ändern müssen wir auf dem Linux Host unter /etc/mysql/mysql.conf.d/mysqld.cnf die Datei Anpassen. Addressen welche momentan auf "127.0.0.1" sind müssen wir auf "0.0.0.0" setzen damit der Server auf alle Ports hört.

Damit die Änderungen wirksam werden am besten den Service neustarten mithilfe von Systemd:
```bash
sudo systemctl restart mysql
```

## Serverbetrieb

### Protokollierung

Wir möchten langsame Abfragen loggen da wir in einer Entwicklungsumgebung sind.
Dies können wir einstellen indem wir "slow_query_log" auf "1" einstellen. Um den Speicherort zu definieren können wir "slow_query_log_file" definieren mit einem Pfad.

Konkret müssen wir in unserer Konfiguration einfach beide Zeilen entkommentieren, dann wird dies in "/var/log/mysql/mysql-slow.log" geloggt.

### Data-Directory

Das Data-Direcotry ist standardmässig unter Linux "/var/lib/mysql". Der Inhalt ist folgender:
```bash
sudo ls /var/lib/mysql

# Output
 auto.cnf	 binlog.000005	 client-cert.pem  '#ib_16384_0.dblwr'   ib_logfile1     performance_schema   sys
 binlog.000001	 binlog.000006	 client-key.pem   '#ib_16384_1.dblwr'   ibtmp1	        private_key.pem      undo_001
 binlog.000002	 binlog.index	 datenbank.pid	   ib_buffer_pool      '#innodb_temp'   public_key.pem	     undo_002
 binlog.000003	 ca-key.pem	 debian-5.7.flag   ibdata1	        mysql	        server-cert.pem
 binlog.000004	 ca.pem		 demodb		   ib_logfile0	        mysql.ibd       server-key.pem
```

Hier sehen wir also verschiede Zertifikate, unsere Datenbanken, Configs und temporäre Dateien.

### Standard Datenbanken in MySQL

Standardmässig haben wir 4 Datenbanken welche benötigt werden:

- mysql - Speichert Tabellen welche Informationen für MySQL Server beinhalten
- information_schema - Bietet Zugriff auf Metadaten der Datenbank
- performance_schema - Bietet Funktionen für eine Überwachung des Server auf einem tiefen level 
- sys - Ein Set von Objekten welches Admins und Entwicklern hilft Daten zu interpretieren welche vom performance_schema gesammelt werden.

