# Performance und Indexing

## Performance Tips

Ziel ist es die Last zu reduzieren und die Geschwindigkeit der DB zu erhöhen.

### Generelles

Mittels dem EXPLAIN command kann man sehen, was ein anderer command macht. "Key" ist hier auf NULL, bedeutet dass es ein Full-Table-Scan ist. "rows" liegt bei 500, es durchsucht also 500 Einträge.
```sql
mysql> explain select customer_id, customer_name from customers where customer_id='140385';
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  500 |    10.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

### Tipp 1

Indexierung von Attributen bei welchen "WHERE, ORDER BY, GROUP BY" zum Einsatz kommen. Meistens sind dies z.B. irgend welche IDs, diese sollten indexiert sein. Ist dies nicht der Fall, so wird ein Full Table Scan durchgeführt, bedeutet, die DB geht durch alle Rows durch und sucht nach der bestimmten ID. 

Erstellt wird dies mittels dem "Create index" Command:
```sql
mysql> Create index customer_id ON customers (customer_Id);
```

### Tipp 2

UNIONS verwenden, wenn in WHERE Statements "or" verwendet werden. Es führt dazu, dass Resultate von mehreren Selects kombiniert werden, anstatt das es evtl. zu einem Full-Table-Scan führt obwohl die Werte indexiert sind.

Schlechte variante:
```sql
mysql> select * from students where first_name like  'Ade%'  or last_name like 'Ade%' ;
```

Variante mit UNIION (gut):
```sql
mysql> select  from students where first_name like  'Ade%'  union all select  from students where last_name like  'Ade%' ;
```

### Tipp 3

Führende Wildcards bei Selects vermeiden und Full-Text-Searches verwenden. Dafür ein FULLTEXT erstellen und anch gegen das Wort matchen lassen.

### Tipp 4

Das Datenbank-Design spielt eine grosse Rolle und man sollte hierbei auf folgende Dinge achten:

1. Keine Redundante Daten, ausser man weiss was man macht
1. Sinnvolle Datentypen verwenden
1. NULL Werte vermeiden
1. Nicht zu viele Spalten / Attribute verwenden
1. SQL Joins ohne "SELECT *" verwenden und unnötige Tabellen nicht miteinbeziehen

### Tipp 5

MySQL unterstützt Caching von Abfragen, davon sollten wir profitieren. Einsehen können wir diese mit folgendem Befehl:

```sql
mysql> show variables like 'query_cache_%' ;
+------------------------------+----------+
| Variable_name                | Value    |
+------------------------------+----------+
| query_cache_limit            | 1048576  |
| query_cache_min_res_unit     | 4096     |
| query_cache_size             | 16777216 |
| query_cache_type             | OFF      |
| query_cache_wlock_invalidate | OFF      |
+------------------------------+----------+
5 rows in <b>set</b> (0.00 sec)
```

Anpassen können wir diese Einstellungen im my.cnf von MySQL:

```bash
query_cache_type=1          # 1 ist eingestellt / 0 ist aus
query_cache_size = 10M      # default ist 1MB / erhöhen auf 10MB macht Sinn
query_cache_limit=1M        # Individuelle Query-Resultate die gecacht werden, 1MB macht Sinn      

```

## Übung

Als erstes müssen wir die DB importieren. Hierzu den Inhalt in die Datei "classicModelsOhneIndexv2.0.sql" schreiben mittels nano oder vim:

```bash
vim classicModelsOhneIndexv2.0.sql

# Jetzt die SQL Statements reinkopieren und anschliessend speichern
```

Als nächstes müssen wir dies importieren:

```bash
sudo mysql -u root < classicModelsOhneIndexv2.0.sql
```

Die Datenbank heisst "explaintesting, wir wechseln also zu dieser. 

Mittels dem "EXPLAIN" können wir überprüfen, wie die Performance ist von einem bestimmen SELECT:

```sql
EXPLAIN SELECT * FROM orderdetails d     
    INNER JOIN orders o ON d.orderNumber = o.orderNumber    
    INNER JOIN products p ON p.productCode = d.productCode     
    INNER JOIN productlines l ON p.productLine = l.productLine     
    INNER JOIN customers c on c.customerNumber = o.customerNumber      
WHERE o.orderNumber = "10101";

+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                                              |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
|  1 | SIMPLE      | l     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    7 |   100.00 | NULL                                               |
|  1 | SIMPLE      | p     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  110 |    10.00 | Using where; Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | o     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  326 |    10.00 | Using where; Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | c     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  122 |    10.00 | Using where; Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | d     | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 2996 |     1.00 | Using where; Using join buffer (Block Nested Loop) |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
5 rows in set, 1 warning (0.00 sec)
```

Wir sehen hier das Type auf "ALL" steht, possible_keys und keys sind auf "NULL". Dies führt dazu, dass wir einen Full-Table-Scan durchführen (7 x 110 x 326 x 122 x 2996 = 91'750'822'240) also rund 92 Milliarden Elemente. Sehr ineffizient.

Aus den gelerten Tipps können wir das ganze nun optimieren indem wir Primary Keys setzen: 

```sql
ALTER TABLE customers ADD PRIMARY KEY (customerNumber);
ALTER TABLE employees ADD PRIMARY KEY (employeeNumber);
ALTER TABLE offices ADD PRIMARY KEY (officeCode);
ALTER TABLE orderdetails ADD PRIMARY KEY (orderNumber, productCode);
ALTER TABLE orders ADD PRIMARY KEY (orderNumber), ADD KEY (customerNumber);
ALTER TABLE payments ADD PRIMARY KEY (customerNumber, checkNumber);
ALTER TABLE productlines ADD PRIMARY KEY (productLine);
ALTER TABLE products ADD PRIMARY KEY (productCode), ADD KEY (buyPrice), ADD KEY (productLine);
ALTER TABLE productvariants ADD PRIMARY KEY (variantId),ADD KEY (buyPrice),ADD KEY (productCode);
```

Das Resultat sieht nun um ein vielfaches erfreulicher aus:

```sql
+----+-------------+-------+------------+--------+------------------------+---------+---------+------------------------------+------+----------+-----------------------+
| id | select_type | table | partitions | type   | possible_keys          | key     | key_len | ref                          | rows | filtered | Extra                 |
+----+-------------+-------+------------+--------+------------------------+---------+---------+------------------------------+------+----------+-----------------------+
|  1 | SIMPLE      | o     | NULL       | const  | PRIMARY,customerNumber | PRIMARY | 4       | const                        |    1 |   100.00 | NULL                  |
|  1 | SIMPLE      | c     | NULL       | const  | PRIMARY                | PRIMARY | 4       | const                        |    1 |   100.00 | NULL                  |
|  1 | SIMPLE      | d     | NULL       | ref    | PRIMARY                | PRIMARY | 4       | const                        |    4 |   100.00 | Using index condition |
|  1 | SIMPLE      | p     | NULL       | eq_ref | PRIMARY,productLine    | PRIMARY | 17      | explaintesting.d.productCode |    1 |   100.00 | NULL                  |
|  1 | SIMPLE      | l     | NULL       | eq_ref | PRIMARY                | PRIMARY | 52      | explaintesting.p.productLine |    1 |   100.00 | NULL                  |
+----+-------------+-------+------------+--------+------------------------+---------+---------+------------------------------+------+----------+-----------------------+
5 rows in set, 1 warning (0.00 sec)
```

Hier sehen wir nun, dass keys und possible_keys einen Wert beinhalten (Den Wert welchen wir gesetzt haben) wie auch das Type nicht mehr auf "ALL" steht. Das ganze führt zu einer drastischen Reduktion der benötigten Zeit und Leistung:

Man rechne: 1 x 1 x 4 x 1 x 1 = 4 ... also 4 Elemente anstatt knapp 92 Milliarden. 

