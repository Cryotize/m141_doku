# Backup & Migration

## Übung 1

Ziel ist es ein Backupscript zu erstellen und nutzen anhand vom Beispiel auf Moodle.

Als erstes müssen wir die Datei erstellen und befüllen: `vim backup_db.sh`

Hier der Inhalt vom Script, ziemlich einfach gehalten:

```bash
#!/bin/bash
# Add the backup dir location, MySQL root password, MySQL and mysqldump location
DATE=$(date +%d-%m-%Y)
BACKUP_DIR="/home/domi/db_backups"
MYSQL_USER='root'
MYSQL_PASSWORD='dein_PW'
MYSQL=/usr/bin/mysql
MYSQLDUMP=/usr/bin/mysqldump

# To create a new directory in the backup directory location based on the date
mkdir -p $BACKUP_DIR/$DATE

# To get a list of databases
databases=`$MYSQL -u$MYSQL_USER -p$MYSQL_PASSWORD -e "SHOW DATABASES;" | grep -Ev "(Database|information_schema)"`

# To dump each database in a separate file
# Kommentar meinerseits: --single-transaction ist hier wichtig damit keine andere Tabelle während des Backups verändert wird.
for db in $databases; do
echo $db
$MYSQLDUMP --single-transaction --force --opt --skip-lock-tables --user=$MYSQL_USER -p$MYSQL_PASSWORD --databases $db | gzip > "$BACKUP_DIR/$DATE/$db.sql.gz"
done

# Delete the files older than 10 days
find $BACKUP_DIR/* -mtime +10 -exec rm {} \;
```

Zuletzt noch das Script ausführbar machen: `sudo chmod +x backup_db.sh`

Um das Script auszuprobieren: `./backup_db.sh`

Zuletzt noch das Testing ob der Import auch wieder funktioniert: `gzip -dk db_backups/15-03-2022/demodb.sql.gz && sudo mysql -u root demodb < db_backups/15-03-2022/demodb.sql`

Wenn keine Fehler ausgegeben wurden hat es erfolgreich funktioniert!

## Übung 2

Als erstes müssen wir mydumper installieren:

```bash
sudo apt install libatomic1
wget https://github.com/mydumper/mydumper/releases/download/v0.12.1/mydumper_0.12.1-1-zstd.focal_amd64.deb
sudo dpkg -i mydumper_0.12.1-1-zstd.focal_amd64.deb
```

Als erstes möchten wir ein Backup der Pokemon DB machen, wichtig ist das 6 parallele Threads und die Komprimierung verwendet wird: `sudo mydumper -u root -B pokemon -t 6 -c -o mydumper_pokemon_export`

Bevor wir nun die Pokemon Datenbank löschen sollten wir einen Snapshot der VM nehmen, anschliessend können wir mittels myloader wieder einen import probieren.

So funktioniert der Import: `sudo myloader -u root -B pokemon -d mydumper_pokemon_export/`
## Übung 3

In der dritten Übung geht es darum eine Migration mittels FIFO und SSH durchzuführen.

Als erstes möchten wir auf dem export Server eine FIFO Pipe erstellen: `mkfifo mysql_migration`

Anschliessend können wir 2 Terminals auf dem export Server öffnen und beide Commands paralell laufen lassen:
```bash
sudo mysqldump --single-transaction -u root pokemon | gzip -9 > mysql_migration; # Export
ssh 172.16.16.6 'gunzip | mysql -u root pokemon' < mysql_migration; # Import auf remote Server
```

In meinem Fall funktioniert die SSH Auth. über Zertifikate, PW ist wird also nicht abgefragt. 

Wichtig ist ebenfalls, dass der User Root beim auth_socket in MySQL die richtige Einstellung hat. Dies funktioniert wie folgt:

```
mysql> USE mysql;
mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
mysql> FLUSH PRIVILEGES;
mysql> exit;
```