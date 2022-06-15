# Replikation bei MySQL

## MySQL-Router

### Auftrag 1

#### Diagramm

Zuerst möchten wir überhaupt wissen, was das docker-compose anstellt und analysieren es hierfür. Ich habe ein Netzwerk-Diagramm gezeichnes, welches aufzeigt wie die Kommunikation zwischen den Services funktioniert:

![mysql_repl_compose](../img/mysql_repl_diagram.png)

#### Master Config

Ich werde die Konfigurationen jeweils direkt mittels Kommentaren beschreiben.

```bash
[mysqld]
# Generelle MySQL Einstellungen
server-id = 1 # Einzigartige ID, so können Sie sich identifizieren
port = 3306 # Server-Port

binlog_format = ROW # Das Format für das Binlog, müssen zwischen Replica und Master gleich gehalten werden

gtid_mode=ON
enforce-gtid-consistency=true

log-slave-updates
log_bin = mysql-bin

default_storage_engine = InnoDB # Storage Engine

# Replikationsrelevante Configs

report-host = mysql_node01 # Name vom Master
slave_net_timeout = 60 # 60 Sekunden soll als Timeout gesetzt werden für das verbinden der Replicas

skip-slave-start

transaction_isolation = 'READ-COMMITTED' # Transaktions Level

binlog_checksum = NONE # Wir wollten nicht mittels Checksummen das binlog überprüfen, kostet zusätzliche Ressourcen
relay_log_info_repository = TABLE
transaction_write_set_extraction = XXHASH64

auto_increment_increment = 1
auto_increment_offset = 2

binlog_transaction_dependency_tracking = WRITESET 
slave_parallel_type = LOGICAL_CLOCK
slave_preserve_commit_order = ON
```

#### Replica Config

```bash
[mysqld]
# Generelle MySQL Einstellungen
server-id = 2 # Hier wieder die einzigartige ID
port = 3306 # Server Port

binlog_format = ROW # Gleiches Format für den Binlog wie auf Master

gtid_mode=ON
enforce-gtid-consistency=true

log-slave-updates
log_bin = mysql-bin

default_storage_engine = InnoDB

# Replikationsrelevante Configs

report-host = mysql_node02 # Host als welche sich die Node ausgibt
slave_net_timeout = 60 # hier wieder der Timeout von 60sek

skip-slave-start
read_only

transaction_isolation = 'READ-COMMITTED' # Transaktions Level, gleich wie Master

binlog_checksum = NONE # Wieder keine Checksumme
relay_log_info_repository = TABLE
transaction_write_set_extraction = XXHASH64

auto_increment_increment = 1
auto_increment_offset = 2

binlog_transaction_dependency_tracking = WRITESET
slave_parallel_type = LOGICAL_CLOCK
slave_preserve_commit_order = ON
```

### Auftrag 2

Wir starten die Umgebung mittels ```docker-compose up -d```

Kontrollieren können wir dies mittels ```docker-compose ps && docker-compose logs -f```. So sehen wir ob die Container laufen und in den Logs können wir mögliche Fehler entdecken. 

So sollte das ps Kommando aussehen: 

```bash
[domi@datenbank mysql-innodb-cluster]$ docker-compose ps
               Name                              Command               State                               Ports                            
--------------------------------------------------------------------------------------------------------------------------------------------
mysql-innodb-cluster_mysql_node01_1   docker-entrypoint.sh --def ...   Up      3306/tcp, 33060/tcp, 33061/tcp                               
mysql-innodb-cluster_mysql_node02_1   docker-entrypoint.sh --def ...   Up      3306/tcp, 33060/tcp, 33061/tcp                               
mysql-innodb-cluster_mysql_node03_1   docker-entrypoint.sh --def ...   Up      3306/tcp, 33060/tcp, 33061/tcp                               
mysql-innodb-cluster_router_1         docker-entrypoint.sh mysql ...   Up      0.0.0.0:3306->3306/tcp,:::3306->3306/tcp,                    
0.0.0.0:6446->6446/tcp,:::6446->6446/tcp,                    
0.0.0.0:6447->6447/tcp,:::6447->6447/tcp,                    
0.0.0.0:6606->6606/tcp,:::6606->6606/tcp  
```

### Auftrag 3

Um die Funktionalität zu testen, starten wir eine Bash Shell im node01 Container:

```bash
docker-compose exec mysql_node01 bash
```

Anschliessend loggen wir uns auf MySQL an sich ein mit dem Benutzer root und Passwort root:

```bash
mysqlsh --js root@mysql_node01
```

Zuletzt möchten wir Informationen zum Cluster auslesen:

```js
dba.getCluster().status()
```

Dies generiert folgenden Output, Kommentare beschreiben die einzelnen Dinge:

```js
{
    "clusterName": "testcluster", // Name vom Cluster
    "defaultReplicaSet": {
        "name": "default", // Der Name vom Replikationsset
        "primary": "mysql_node01:3306", // Node 01 ist als master gesetzt
        "ssl": "DISABLED", // SSL macht alles kompliziert, wir machen es aus...
        "status": "OK", // Ist Okay soweit
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.", // Statusmeldung
        "topology": { // Hier darunter sieht man die Topologie von 3 Members (minimum)
            "mysql_node01:3306": { // Name:Port
                "address": "mysql_node01:3306", // Adresse:Port
                "memberRole": "PRIMARY", // Primary = Master
                "mode": "R/W", // Read / Write 
                "readReplicas": {}, // Falls man Read Replicas hat...
                "replicationLag": null, // Kein Log der Replikation
                "role": "HA", // Hoch Verfügbarkeits Modus
                "status": "ONLINE", // Node Online?
                "version": "8.0.29" // MySQL Version
            }, 
            "mysql_node02:3306": {
                "address": "mysql_node02:3306", 
                "memberRole": "SECONDARY", // <- Hier darauf achten, nun Secondary, Primary einzigartig
                "mode": "R/O", // Nur Read Only, nur primary kann schreiben
                "readReplicas": {}, 
                "replicationLag": null, 
                "role": "HA", 
                "status": "ONLINE", 
                "version": "8.0.29"
            }, 
            "mysql_node03:3306": {
                "address": "mysql_node03:3306", 
                "memberRole": "SECONDARY", 
                "mode": "R/O", 
                "readReplicas": {}, 
                "replicationLag": null, 
                "role": "HA", 
                "status": "ONLINE", 
                "version": "8.0.29"
            }
        }, 
        "topologyMode": "Single-Primary" // Einzelner Primary
    }, 
    "groupInformationSourceMember": "mysql_node01:3306" // Von hier kommt die Info
}
```

## ProxySQL und Springboot

Auf Host erledigen