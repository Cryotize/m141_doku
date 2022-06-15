# Sakila

## Installation

Zuerst wollen wir die dumps herunterladen:

```bash
wget https://downloads.mysql.com/docs/sakila-db.zip
```

Nun können wir das ganze in MySQL importieren:

```bash
/usr/bin/mysql -u root < sakila-schema.sql
/usr/bin/mysql -u root < sakila-data.sql
```

## Testing

Um den import zu testen, einfach wieder auslesen:
(Sieht gleich wie im Script, ist es auch)
```sql
mysql> USE sakila;
Database changed

mysql> SHOW FULL TABLES;
+----------------------------+------------+
| Tables_in_sakila           | Table_type |
+----------------------------+------------+
| actor                      | BASE TABLE |
| actor_info                 | VIEW       |
| address                    | BASE TABLE |
| category                   | BASE TABLE |
| city                       | BASE TABLE |
| country                    | BASE TABLE |
| customer                   | BASE TABLE |
| customer_list              | VIEW       |
| film                       | BASE TABLE |
| film_actor                 | BASE TABLE |
| film_category              | BASE TABLE |
| film_list                  | VIEW       |
| film_text                  | BASE TABLE |
| inventory                  | BASE TABLE |
| language                   | BASE TABLE |
| nicer_but_slower_film_list | VIEW       |
| payment                    | BASE TABLE |
| rental                     | BASE TABLE |
| sales_by_film_category     | VIEW       |
| sales_by_store             | VIEW       |
| staff                      | BASE TABLE |
| staff_list                 | VIEW       |
| store                      | BASE TABLE |
+----------------------------+------------+
23 rows in set (0.01 sec)

mysql> SELECT COUNT(*) FROM film;
+----------+
| COUNT(*) |
+----------+
|     1000 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(*) FROM film_text;
+----------+
| COUNT(*) |
+----------+
|     1000 |
+----------+
1 row in set (0.00 sec)
```

## Views

### Erklärung

Bevor wir die Views anzeigen, möchte ich erklären was sie überhaupt sind:

Views kann man sich vorstellen wie virtuelle Tabellen. Sie beinhalten keine Daten, sondern lesen sie nur von einer anderen Tabelle aus. So kann man genau definieren welche Daten im View vorhanden sein sollten. Sie sind immer an eine Tabelle gebunden und aktualisieren sich daher auch mit der Tabelle.

Eine sehr gute Erklärung kann [hier](https://www.javatpoint.com/mysql-view) gefunden werden.

### Anzeigen

Mittels folgendem Command können wir alle Tabellen sehen welche als View definiert wurden:

```sql
mysql> show full tables where table_type = 'VIEW';
+----------------------------+------------+
| Tables_in_sakila           | Table_type |
+----------------------------+------------+
| actor_info                 | VIEW       |
| customer_list              | VIEW       |
| film_list                  | VIEW       |
| nicer_but_slower_film_list | VIEW       |
| sales_by_film_category     | VIEW       |
| sales_by_store             | VIEW       |
| staff_list                 | VIEW       |
+----------------------------+------------+
7 rows in set (0.00 sec)
```

### Erstellen

Einen View erstellen wir mittels dem "CREATE" Command. Hier ein einfaches Beispiel:

```sql
CREATE VIEW [Brazil Customers] AS
SELECT CustomerName, ContactName
FROM Customers
WHERE Country = 'Brazil'; 
```

In diesem Fall erstellen wir einen View welcher "Brazil Customers" heisst, in diesem werden alle Kunden mit deren Nachname und Kontaktname gespeichert welche aus Brasilien sind.

### Änderung vornehmen

Dies wird mittels ALTER VIEW erledigt:

```sql
ALTER
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    VIEW view_name [(column_list)]
    AS select_statement;
```

### Sakila actor_info

Auftrag ist es, herauszufinden wie der "actor_info" View erstellt wurde, bzw. welcher CREATE Befehl dahinter Steckt.

Herausfinden können wir mittels folgendem SQL Befehl:
```sql
SELECT VIEW_DEFINITION FROM INFORMATION_SCHEMA.VIEWS WHERE TABLE_NAME = 'actor_info'
```

Der CREATE Befehl welcher ausgegeben wurde sieht wie folgt aus:
```sql
CREATE OR REPLACE VIEW actor_info AS SELECT `a`.`actor_id` AS `actor_id`,`a`.`first_name` AS `first_name`,`a`.`last_name` AS `last_name`,group_concat(DISTINCT concat(`c`.`name`,': ',(SELECT group_concat(`f`.`title` ORDER BY `f`.`title` ASC separator ', ') FROM ((`film` `f` JOIN `film_category` `fc` ON((`f`.`film_id` = `fc`.`film_id`))) JOIN `film_actor` `fa` ON((`f`.`film_id` = `fa`.`film_id`))) WHERE ((`fc`.`category_id` = `c`.`category_id`) AND (`fa`.`actor_id` = `a`.`actor_id`)))) ORDER BY `c`.`name` ASC separator '; ') AS `film_info` FROM (((`actor` `a` LEFT JOIN `film_actor` `fa` ON((`a`.`actor_id` = `fa`.`actor_id`))) LEFT JOIN `film_category` `fc` ON((`fa`.`film_id` = `fc`.`film_id`))) LEFT JOIN `category` `c` ON((`fc`.`category_id` = `c`.`category_id`))) GROUP BY `a`.`actor_id`,`a`.`first_name`,`a`.`last_name`;
```

## Stored Procedures

### Erklärung

Stored Procedures sind in der theorie einfach erklärt. In einer Datenbank kann es oftmals sein, dass man viele Queries hat welch sich in gleicher Reihenfolge wiederholen. Damit wir nicht jede Query einzeln durchführen müssen können wir sie gruppieren und so die Performance steigern.

Mehr dazu inklusive Beispiel [hier](https://www.javatpoint.com/mysql-procedure)

Aufruf mittels ```CALL procedure_name(parameter)```

### Anzeigen

Möchten wir die Stored Procedures unserer DB einsehen, so können wir dies mit folgendem Command:

```sql
mysql> SHOW PROCEDURE STATUS WHERE db = 'sakila';
+--------+-------------------+-----------+----------------+---------------------+---------------------+---------------+--------------------------------------------------+----------------------+----------------------+--------------------+
| Db     | Name              | Type      | Definer        | Modified            | Created             | Security_type | Comment                                          | character_set_client | collation_connection | Database Collation |
+--------+-------------------+-----------+----------------+---------------------+---------------------+---------------+--------------------------------------------------+----------------------+----------------------+--------------------+
| sakila | film_in_stock     | PROCEDURE | root@localhost | 2022-03-24 06:42:31 | 2022-03-24 06:42:31 | DEFINER       |                                                  | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| sakila | film_not_in_stock | PROCEDURE | root@localhost | 2022-03-24 06:42:31 | 2022-03-24 06:42:31 | DEFINER       |                                                  | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| sakila | rewards_report    | PROCEDURE | root@localhost | 2022-03-24 06:42:31 | 2022-03-24 06:42:31 | DEFINER       | Provides a customizable report on best customers | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
+--------+-------------------+-----------+----------------+---------------------+---------------------+---------------+--------------------------------------------------+----------------------+----------------------+--------------------+
3 rows in set (0.04 sec)
```

### Erstellen

Hier ganz kurz gefasst, weiter unten wird es noch genauer erklärt.
```sql
DELIMITER //

CREATE PROCEDURE GetAllProducts()
BEGIN
	SELECT *  FROM products;
END //

DELIMITER ;
```
!> DELIMITER nicht vergessen zu ändern und anschliessend zurückzusatzen damit das Kommando nicht direkt ausgeführt wird!

### Änderung vornehmen

Eine direkte Änderung ist nicht möglich, ein DROP und CREATE ist notwendig. 

## Triggers

### Erklärung

Ein Trigger ist ein kleiner Task welcher automatisch als Antwort auf einen INSERT, UPDATE oder DELETE ausgeführt wird. Man kann sie vor oder nach dem Event durchführen und so Prozesse automatisieren. 

### Anzeigen

Möchten wir alle Triggers der Datenbank einsehen so können wir dies mit folgendem Command:

```sql
show triggers in sakila;
```

Der Output ist zu gross, um ihn hier richtig anzueigen. Daher am besten selsbt ausprobieren.

### Erstellen

Hier ein praktisches Beispiel von einem Trigger, welcher eine Art "Backup" macht.

```sql
CREATE TRIGGER before_employee_update 
    BEFORE UPDATE ON employees
    FOR EACH ROW 
 INSERT INTO employees_audit
 SET action = 'update',
     employeeNumber = OLD.employeeNumber,
     lastname = OLD.lastname,
     changedat = NOW();
```

... und die Syntax:

```sql
CREATE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE| DELETE }
ON table_name FOR EACH ROW
trigger_body;
```

## Aufträge

### Create Befehl erklärt

```sql
// Hier erstellen wir die Prozedur film_in_stock mit dem Input p_film_id als INT, dem INT p_store_id als Input und als Output nutzen wird p_film_count als INT.
CREATE PROCEDURE film_in_stock(IN p_film_id INT, IN p_store_id INT, OUT p_film_count INT)
READS SQL DATA
BEGIN
// Hier ist das SQL Statement um die Daten auszulesen mit dem jeweiligen WHERE Statement
     SELECT inventory_id
     FROM inventory
     WHERE film_id = p_film_id
     AND store_id = p_store_id
     AND inventory_in_stock(inventory_id);
// Zuletzt zählen wir die Vorkommen welche übereinstimmen zu dem WHERE statement und speichern es in p_film_count.
     SELECT COUNT(*)
     FROM inventory
     WHERE film_id = p_film_id
     AND store_id = p_store_id
     AND inventory_in_stock(inventory_id)
     INTO p_film_count;
END $$
```

# Chat-Applikation

Die Chat-Applikation sollte so angepasst werden, dass nun Stored Procedures und Views verwendet werden. Dazu muss man in der DB diese erstellen und anschliessend den Code anpassen damit dies auch funktioniert. 

## View

Fangen wir mit dem einfacheren an, dem View. 

Im moment sieht das ganze wie folgt aus im Code:

```javascript
var sql = "SELECT * FROM login WHERE username='" + username+"'";
```

Diesen SELECT wollen wir in einem View machen, da dies aber den eingegebenen Username vom HTML abgleicht müssen wir den WHERE weiterhin im Code belassen und können dies nicht direkt im View speichern.

Den Code passen wir also dementsprechend wie folgt an: 

```javascript
var sql = "SELECT * FROM selectview WHERE username='" + username+"'";
```

Hier sehen wir nun, dass der View "selectview" verwendet wird. Um diesen zu erstellen muss man sich am DBMS anmelden und die "chat" DB auswählen. Der View wird mit folgendem Command erstellt: 

```sql
mysql> CREATE VIEW selectview AS SELECT * FROM login;
```

So ist die Anpassung für den View bereits fertig.

## Stored Procedure

Das Stored Procedure ist ein wenig komplizierter. Schauen wir uns den Code an:

```javascript
var sql = "INSERT INTO message (message , user) VALUES ('" + data+ "' , '"+user+"')";
```

Für Stored Procedures brauchen wir die Datentypen der Spalten "message" und "user". Nur so können wir diesen erstellen. Hierfür können wir DESCRIBE verwenden wenn wir im DBMS eingeloggt sind und die DB ausgewählt haben:

```sql
mysql> DESCRIBE message;
+---------+---------------+------+-----+---------+----------------+
| Field   | Type          | Null | Key | Default | Extra          |
+---------+---------------+------+-----+---------+----------------+
| id      | int unsigned  | NO   | PRI | NULL    | auto_increment |
| message | varchar(2550) | YES  |     | NULL    |                |
| user    | varchar(250)  | YES  |     | NULL    |                |
+---------+---------------+------+-----+---------+----------------+
```

Jetzt wo wir dies wissen, können wir das Stored Procedure erstellen.

!> Bevor wir ein Stored Procedure erstellen müssen wir den DELIMITER Temporär umstellen. Dies macht man mittels ```DELIMITER <zeichen>``` in SQL. In meinem Beispiel verwende ich temporär // als delimiter. 

```sql
CREATE PROCEDURE insertprocedure (IN message varchar(2550), IN user varchar(250)) BEGIN INSERT INTO message (message , user) VALUES (message , user); END//
```

Hier sagen wir das in die Spalten message und user die Werte der Variablen message und user eingefügt werden. Dies müssen wir nun noch dementsprechend im Code anpassen:

```javascript
var sql = "CALL insertprocedure('"+data+"', '"+user+"')";
```

Das "+data+" und "+user+" kommt vom HTML übergeben und wird hierbei in die Variablen message und user gespeichert. So ist die Anpassung bereits fertiggestellt.

!> Je nach Berechtigungsstufe vom User welche die DB verbindet, kann es sein das er keinen Zugriff auf die Views / Stored Procedures hat. Hier bitte achtsam sein und kontrollieren falls es nicht funktioniert. 
