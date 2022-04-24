# Backup Vorgehen

## Datenverfügbarkeit

Während einem Backup hat man verschiedene Varianten von Datenverfügbarkeiten. Diese sind in Cold, Warm und Hot aufgeteilt. 

### Cold

Bei Cold Backups stehen die Daten dem Benutzer nicht zur verfügung. Dies kann man z.B. über die Nacht laufen lassen oder ausserhalb der Betriebszeiten.

### Warm

Während einem Warm Backups stehen die Daten teilweise oder reduziert zur verfügung. Dies kann z.B. bedeutet, dass sie nur gelesen werden können.

### Hot

bei einem Hot Backup stehen die Daten dem User voll zur verfügung. Wenn möglich, ist dies die beste Variante, es könnte aber mit zusätzlichen Kosten und Komplexität verbunden sein.

## Datenformat

### Logisch

Beim Logischen Vorgehen wird das Backup in menschlesbarer Form gemacht. Darunter versteht sich z.B. ein mysqldump in welchem alle Commands stehen. Dies braucht relativ viel Speicherplatz.

### Physisch

Bei einem Physischen Backup spricht man vom Backup in binärer Form. So kann man z.B. auf Filesystem Level einen Snapshot machen, Nachteil ist, dass man nicht jede Änderung nachverfolgen kann in der DB. Beim Logischen ist dies möglich.

## Backup-Art

### Voll

Beim Vollbackup werden immer alle Daten gesichert.

### Inkrementell

Beim Inkrementellen Backup werden nur die Änderungen seit dem letzten Backup gesichert. So kann man Zeit und Platz sparen. Der Restore dauert dafür länger da man mehrere Inkrementelle Backups einspielen muss falls man mehrere Tage zurück muss. 

## Übungen

Siehe: Siehe: [Backup & Migration, Übung 1](../other/backup.md?id=Übung-1)

## Percona XtraBackup

Percona XtraBackup ist eine Enterprise Lösung für Backups von MySQL DBs. Es ermöglicht einem hot Backups durchzuführen, welches von grossem Vorteil ist. Die Software ist opensource und wird von Facebook verwendet um ihre Daten zu sichern. Es ermöglicht eine einfache Automatisation von MySQL Server DB Backups welche zugelich wenig Zeit und Arbeit beanspruchen und sicher sind. 

Weitere Informationen auf ihrer [Website](https://www.percona.com/software/mysql-database/percona-xtrabackup)

# Migrationen

Siehe: [Backup & Migration, Übung 3](../other/backup.md?id=Übung-3)

# Etherpad

Etherpad ist eine Software welche es ermöglicht, zusammen in real-time Dokumente im Web zu editieren.

## Installation

Die folgenden Commands installiert es relativ rasch auf Ubuntu.

```bash
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
sudo apt install -y nodejs
git clone --branch master https://github.com/ether/etherpad-lite.git && cd etherpad-lite
```

Möchte man alle Features nutzen wie auf ihrer Website:

```bash
npm install --no-save --legacy-peer-deps ep_headings2 ep_markdown ep_comments_page ep_align ep_font_color ep_webrtc ep_embedded_hyperlinks2
```

Die Einbindung zur MySQL DB ist realtiv einfach. 

Als erstes wollen wir unsere eigenen Einstellungen nutzen und kopieren daher das Template:

```bash
cp settings.json.template settings.json
```

Danach wollen wir die richtigen DB Einstellungen einfügen in der Config:

```json
/* Als erstes müssen wir die folgende DB auskommentieren*/
   /*
  "dbType": "dirty",
  "dbSettings": {
    "filename": "var/dirty.db"
  },
  */
  /*
   * An Example of MySQL Configuration (commented out).
   *
   * See: https://github.com/ether/etherpad-lite/wiki/How-to-use-Etherpad-Lite-with-MySQL
   */

/* Als zweites müssen wir die folgende DB einkommentieren und die richtigen Werte angeben*/
  
  "dbType" : "mysql",
  "dbSettings" : {
    "user":     "etherpaduser",
    "host":     "localhost",
    "port":     3306,
    "password": "dein_pw",
    "database": "etherpad_lite_db",
    "charset":  "utf8mb4"
  },
```

Damit sich Etherpad an der DB anmelden kann müssen wir nun auch noch den User mit PW, der DB und den richten Berechtigungen erstellen.

Auf DBMS einloggen:

```bash
mysql -u root
```

User erstellen in mysql:

```sql
CREATE USER 'etherpaduser'@'localhost' IDENTIFIED WITH mysql_native_password BY 'dein_pw';
```

Anschliessend möchten wir die DB erstellen:

```sql
CREATE DATABASE etherpad_lite_db;
```

Nun möchten wir noch die berechtigungen richtig setzen:

```sql
GRANT ALL PRIVILEGES ON etherpad_lite_db.* TO 'etherpaduser'@'localhost';
```

Zuletzt können wir das Programm starten und unter http://127.0.0.1:5001 im Browser erreichen:

```bash
bash src/bin/run.sh 
```

## Testing

Hierzu einfach die Website unter http://127.0.0.1:5001 aufrufen und etwas schreiben. Anschliessend möchten wir dies in der DB einsehen.

Als erstes wieder auf dem DBMS einloggen:

```bash
mysql -u root
```

Nun die DB auswählen und den Inhalt der Tabelle "store" auslesen:

```sql
mysql> use etherpad_lite_db;

mysql> show tables;
+----------------------------+
| Tables_in_etherpad_lite_db |
+----------------------------+
| store                      |
+----------------------------+
1 row in set (0.01 sec)

mysql> SELECT * FROM store;
```
Nun sollte man den Beispiel Text oder seinen eigenen Text im SELECT sehen. Falls nicht, kann es sein das man noch die dirty-db nutzt oder sonst einen Konfigurationsfehler hat.

Das sieht dann etwa so aus:

```sql
| readonly2pad:r.098cd5c7b27f526facb990d5fc670c07 | "Test"
```

Hier sehen wir den Text "Test" welchen ich hier im Pad "Test" verändert habe:

```sql
| pad:Test:revs:1 | {"changeset":"Z:6d>1|4=4x=1e*0|1+1$\n","meta":{"author":"a.BKAVPDnwtHS7753Z","timestamp":1648066949602}}
```

## Security-Angriffsvektoren

### Mögliche Angriffsvektoren & Lösungen

Hier ein paar Gefahren wie man an Daten der DB kommt mit der jeweiligen Lösung dazu (Stichwortartig):

|Gefahr                          |Lösung                              |
|--------------------------------|------------------------------------|
|Zu viel Berechtigungen auf User |Berechtigungen setzen               |
|Traffic capture                 |Verschlüsselung nutzen              |
|Direkt Zugriff auf DB Server    |Hinter Applikation verstecken       |
|Benutzer Konto ausspähen        |Konto nicht in Applikation speichern|
|SQL Injections                  |Prepared Statements verwenden       |
|Hacking                         |Firewall, Passwörter, Berechtigungen|

### Etherpad Sicherheit

Hier möchte ich ein paar Überlegungen geben zu der jetzigen Sicherheit der Etherpad Applikation:

- Die Berechtigungen sind sehr knapp gehalten. So kann der Etherpad User nur auf seine eigene DB zugreifen und nicht mehr, man könnte dies noch mehr auf Tabellen beschränken.

- Traffic Capture ist leider noch voll und ganz möglich, da wir ohne eine Verschlüsselung arbeiten, dies ist also ein Punkt welcher beachtet werden sollte. In unserem Fall ist dies kaum ein Problem da die Applikation auf dem gleichen Server ist wie die DB, falls man aber die DB auf einem anderen Server hat sollte dies definitv aktiviert werden.

- Der direkte Zugriff auf den DB Server ist in diesem Fall ebenfalls möglich, grund dafür sind Testing zwecke. Dies kann aber einfach gelöst werden, indem man mittels UFW unter Ubuntu den Port schliesst. 

- Das Benutzerkonto kann nicht ausgespäht werden, es ist nur vom Server lesbar. In der Webapplikation sieht man die Verbindung also nicht.

- SQL Injections kann ich schlecht bewerten, da ich nicht die ganze Programmlogik kenne. Ich gehe aber stark davon aus, dass dies verhindert wird. Falls erwünscht kann man dies auch testen.

- Hacking ist relativ einfach, da die Passwörter nicht start gewählt sind und die Firewall relativ offen gehalten wurde. Grund dafür ist, die Entwicklungsumbgebung. In Produktion würde man stärkere Passwörter und striktere Firewall regeln nutzen. 

