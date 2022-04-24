# Import & Export

## Import von SQL-Daten

Ein Import ist ziemlich einfach mit den eingebauten Commands von mysql.

Folgende Syntax ist anzuwenden:

```bash
mysql -u username -p new_database < data-dump.sql
```

Das .sql File oder auch "Dump" genannt beinhaltet alle Statements um die Daten wiederherzustellen. Dies kann dazu führen, dass die Datei eher gross sein kann. 

## Export in SQL-Daten

Der Export ist sehr ähnlich zum Import der SQL Daten:

```bash
mysqldump -u username -p existing_database > data-dump.sql
```

Während diesem Export kann es zu veränderungen innerhalb der Datenbank kommen da nicht alle Tabellen gesperrt werden. Möchte man dies verhindern, so kann man alle Tabellen während der Transaktion sperren mittels ```--single-transaction```. Hier wird ein Snapshot der DB gemacht welcher anschliessend exportiert wird, so werden die Daten der Tabellen nicht mehr verändert, im Hintergrund kann aber der jetzige Stand immernoch aktualisiert werden.

Arbeitet man mit der MyISAM Engine so sollte man ```--lock-tables``` verwenden, hier kann im Hintergrund aber nichts mehr verändert werden.

## Export in CSV

Möchte man die Daten im CSV Format exportieren für einen anschliessenden Import in einem Programm welches das .sql Format nicht unterstützt, so kann man folgende Commands verwenden:

> [!warning]
> Beim export in CSV könnte folgender Fehler auftreten:
>
> ```mysqldump: Got error: 1290: The MySQL server is running with the --secure-file-priv option so it cannot execute this statement when executing 'SELECT INTO OUTFILE')```
>
> Dies kann man am besten beheben indem man den Output in /var/lib/mysql-files/ macht.
> Falls dies nicht erwünscht ist, so muss man den Server ohne die ``` --secure-file-priv``` Option laufen lassen.

Hier einmal mittels SELECT
```sql
SELECT 
    orderNumber, status, orderDate, requiredDate, comments
FROM
    orders
WHERE
    status = 'Cancelled' 
INTO OUTFILE '/tmp/cancelled_orders.csv' -- Arbeiten Sie hier mit dem tmp-Verzeichnis, arbeiten Sie mit absoluten Pfaden
FIELDS ENCLOSED BY '"'  -- Jedes Feld wird mit "" eingefasst
TERMINATED BY ';' -- Feld wird mit einem Semikolon beendet
LINES TERMINATED BY '\n'; -- Zeilenendings nach Linux-Style (\r\n für Windows)
```

Dies ist aber eher kompliziert und die verwendung liegt bei einzelnen Spalten und nicht ganzen Tabellen. Möchte man ganze Tabellen exportieren so kann man dies mittels mysqldump:

```bash
mysqldump DBNAME TABLENAME --fields-terminated-by ',' \
--fields-enclosed-by '"' --fields-escaped-by '\' \
--no-create-info --tab /var/lib/mysql-files/
```

Möchte man die ganze DB und nicht nur eine Tabelle exportieren, so kann man den ```TABLENAME``` einfach weglassen.