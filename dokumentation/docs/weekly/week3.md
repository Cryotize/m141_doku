# Woche 3

In der 3. Woche ist der Auftrag relativ einfach gehalten: Eine Installation von MySQL zu welcher man Recherche betreiben soll zusammen mit einer Dokumentation. 

## Generelles zu MySQL

- Hersteller: Oracle Corporation
- Typ: Relationale Datenbank
- Ranking: Platz 2. Weltweit
- Lizenzen: Proprietär / GPLv2
- Ersterscheinung: 1995
- Geschrieben in: C, C++
- Läuft unter: Linux, Solaris, macOS, Windows, FreeBSD
- Sourcecode: https://github.com/mysql/mysql-server
- Versionen: General availability (8.0.x), Minor Versions (8.x.x), Legacy (<5.7.x)

## Pre-Install

#### Informationen über den DB Host

```bash
domi@datenbank 
-------------- 
OS: Ubuntu 20.04.4 LTS x86_64 
Host: VirtualBox 1.2 
Kernel: 5.4.0-100-generic 
Uptime: 14 mins 
Packages: 671 (dpkg) 
Shell: bash 5.0.17 
Resolution: preferred 
Terminal: /dev/pts/0 
CPU: Intel i7-8550U (8) @ 1.991GHz 
GPU: 00:02.0 VMware SVGA II Adapter 
Memory: 514MiB / 5882MiB 
```

#### Software Quelle

Software wird mit dem Paketmanager "aptitude" oder auch "apt" installiert. Es werden offizielle Quellen von Canonical (Hersteller von Ubuntu) verwendet. Dies kann mit folgendem Command überprüft werden:

```bash
cat /etc/apt/sources.list

# Output:
deb http://ch.archive.ubuntu.com/ubuntu focal-security main restricted
deb http://ch.archive.ubuntu.com/ubuntu focal-security universe
deb http://ch.archive.ubuntu.com/ubuntu focal-security multiverse
```

!> Andere Quellen oder "Repositories" können hinzugefügt werden um die Software direkt vom Hersteller zu beziehen. Diese werden dann im Ordner /etc/apt/sources.list.d/xxx gespeichert.

#### Software-Infos

MySQL wird in Version 8.0 Installiert, die Abhängigkeiten sind nach korrespondierender Version installiert. Dies übernimmt der Paketmanager, man muss sich also nicht selbst darum kümmern. Version ist die neuste GA (General Availability) Version und ist somit für Produktion geeignet.

#### Dienste

Einmal ganz grob das wichtigste:

- MySQL Server
- SSH
- UFW
- CRON
- Systemd-*

... kann man aber interpretieren wie man möchte, hier einfach alle Dienste welche momentan auf dem Server laufen. (Minimaler Install, selbst aufgesetzt, ohne GUI)

```bash
systemctl --type=service

# Output
  UNIT                                                                                      LOAD   ACTIVE SUB     DESCRIPTION                                                                  
  accounts-daemon.service                                                                   loaded active running Accounts Service                                                                                                           
  apport.service                                                                            loaded active exited  LSB: automatic crash report generation                                       
  atd.service                                                                               loaded active running Deferred execution scheduler                                                 
  blk-availability.service                                                                  loaded active exited  Availability of block devices                                                
  cloud-config.service                                                                      loaded active exited  Apply the settings specified in cloud-config                                 
  cloud-final.service                                                                       loaded active exited  Execute cloud user/final scripts                                             
  cloud-init-local.service                                                                  loaded active exited  Initial cloud-init job (pre-networking)                                      
  cloud-init.service                                                                        loaded active exited  Initial cloud-init job (metadata service crawler)                            
  console-setup.service                                                                     loaded active exited  Set console font and keymap                                                  
  cron.service                                                                              loaded active running Regular background program processing daemon                                 
  dbus.service                                                                              loaded active running D-Bus System Message Bus                                                     
  finalrd.service                                                                           loaded active exited  Create final runtime dir for shutdown pivot root                             
  fwupd.service                                                                             loaded active running Firmware update daemon                                                       
  getty@tty1.service                                                                        loaded active running Getty on tty1                                                                
  irqbalance.service                                                                        loaded active running irqbalance daemon                                                            
  keyboard-setup.service                                                                    loaded active exited  Set the console keyboard layout                                              
  kmod-static-nodes.service                                                                 loaded active exited  Create list of static device nodes for the current kernel                    
  lvm2-monitor.service                                                                      loaded active exited  Monitoring of LVM2 mirrors, snapshots etc. using dmeventd or progress polling
  lvm2-pvscan@8:3.service                                                                   loaded active exited  LVM event activation on device 8:3                                           
  multipathd.service                                                                        loaded active running Device-Mapper Multipath Device Controller                                    
  mysql.service                                                                             loaded active running MySQL Community Server                                                       
  networkd-dispatcher.service                                                               loaded active running Dispatcher daemon for systemd-networkd                                       
  polkit.service                                                                            loaded active running Authorization Manager                                                        
  rsyslog.service                                                                           loaded active running System Logging Service                                                       
  setvtrgb.service                                                                          loaded active exited  Set console scheme                                                           
  ssh.service                                                                               loaded active running OpenBSD Secure Shell server                                                  
  systemd-fsck@dev-disk-by\x2duuid-c5f2842c\x2d8943\x2d4d9f\x2daede\x2d9668fb3e436b.service loaded active exited  File System Check on /dev/disk/by-uuid/c5f2842c-8943-4d9f-aede-9668fb3e436b  
  systemd-journal-flush.service                                                             loaded active exited  Flush Journal to Persistent Storage                                          
  systemd-journald.service                                                                  loaded active running Journal Service                                                              
  systemd-logind.service                                                                    loaded active running Login Service                                                                
  systemd-modules-load.service                                                              loaded active exited  Load Kernel Modules                                                          
  systemd-networkd-wait-online.service                                                      loaded active exited  Wait for Network to be Configured                                            
  systemd-networkd.service                                                                  loaded active running Network Service                                                              
  systemd-random-seed.service                                                               loaded active exited  Load/Save Random Seed                                                        
  systemd-remount-fs.service                                                                loaded active exited  Remount Root and Kernel File Systems                                         
  systemd-resolved.service                                                                  loaded active running Network Name Resolution                                                      
  systemd-sysctl.service                                                                    loaded active exited  Apply Kernel Variables                                                       
  systemd-sysusers.service                                                                  loaded active exited  Create System Users                                                          
  systemd-timesyncd.service                                                                 loaded active running Network Time Synchronization                                                 
  systemd-tmpfiles-setup-dev.service                                                        loaded active exited  Create Static Device Nodes in /dev                                           
  systemd-tmpfiles-setup.service                                                            loaded active exited  Create Volatile Files and Directories                                        
  systemd-udev-settle.service                                                               loaded active exited  udev Wait for Complete Device Initialization                                 
  systemd-udev-trigger.service                                                              loaded active exited  udev Coldplug all Devices                                                    
  systemd-udevd.service                                                                     loaded active running udev Kernel Device Manager                                                   
  systemd-update-utmp.service                                                               loaded active exited  Update UTMP about System Boot/Shutdown                                       
  systemd-user-sessions.service                                                             loaded active exited  Permit User Sessions                                                         
  udisks2.service                                                                           loaded active running Disk Manager                                                                 
  ufw.service                                                                               loaded active exited  Uncomplicated firewall                                                       
  unattended-upgrades.service                                                               loaded active running Unattended Upgrades Shutdown                                                 
  upower.service                                                                            loaded active running Daemon for power management
```


## Intallation

#### Software & deren Versionen

Folgende Pakete wurden alle Installiert bei der Aufforderung "mysql-server" zu installieren:

```bash
  libcgi-fast-perl libcgi-pm-perl libencode-locale-perl libevent-core-2.1-7 libevent-pthreads-2.1-7 libfcgi-perl
  libhtml-parser-perl libhtml-tagset-perl libhtml-template-perl libhttp-date-perl libhttp-message-perl
  libio-html-perl liblwp-mediatypes-perl libmecab2 libtimedate-perl liburi-perl mecab-ipadic mecab-ipadic-utf8
  mecab-utils mysql-client-8.0 mysql-client-core-8.0 mysql-common mysql-server-8.0 mysql-server-core-8.0
  ```

#### Ablauf der Installation

System auf neusten Stand bringen: 
```bash
sudo apt update && sudo apt upgrade -y
```

MySQL Server Installieren:
```bash
sudo apt install mysql-server -y
```

MySQL Server sicherer machen mithilfe vom Sicherheitsscript:
```bash
sudo mysql_secure_installation
```
Dies wird einen durch verschiedene Optionen führen, als erstes möchten wir die Passwort Komponente validieren, daher mit Y bestätigen.
Anschliessend mindestens die 2 wählen für "MEDIUM".

Jetzt muss man ein starkes Passwort setzen, alle restlichen Optionen können mit einem einfachen Y bestätigt werden, dies bewirkt das Anonyme Benutzer und Test-Datenbanken entfernt werden, Remote-Root Logins ausgeschaltet werden und alles neu geladen wird. Jetzt ist die Datenbank um einiges sicherer und fast bereit zur Konfiguration, fehlt nur noch das Testing.

## Testing

#### Status vom Service

Als erstes müssen wir wissen ob der Dienst überhaupt läuft:
```bash
[domi@datenbank ~]$ systemctl status mysql
● mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2022-02-24 06:41:53 UTC; 4min 32s ago
   Main PID: 33171 (mysqld)
     Status: "Server is operational"
      Tasks: 39 (limit: 6953)
     Memory: 359.6M
     CGroup: /system.slice/mysql.service
             └─33171 /usr/sbin/mysqld
```

#### Version von MySQL

Die Version können wir nochmals mit MySQL selbst validieren:
```bash
[domi@datenbank ~]$ sudo mysqladmin -p -u root version
Enter password: 
mysqladmin  Ver 8.0.28-0ubuntu0.20.04.3 for Linux on x86_64 ((Ubuntu))
Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version		8.0.28-0ubuntu0.20.04.3
Protocol version	10
Connection		Localhost via UNIX socket
UNIX socket		/var/run/mysqld/mysqld.sock
Uptime:			4 min 49 sec
```

#### Commands testen

Zuletzt möchten wir wissen ob die MySQL Commands richtig funktionieren:

Login auf Server:
```bash
[domi@datenbank ~]$ sudo mysql -p -u root
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 13
Server version: 8.0.28-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

Tabellen einsehen:
```sql
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)
```

User einsehen:
```sql
mysql> SELECT user,authentication_string,plugin,host FROM mysql.user;
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| user             | authentication_string                                                  | plugin                | host      |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| debian-sys-maint | $A$005$/jk4BbsqJYjVN*H+P0K9H8BYE/QNrkLBe/qoDooaa295CcNilBM.nnFVGY4     | caching_sha2_password | localhost |
| mysql.infoschema | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.session    | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.sys        | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| root             |                                                                        | auth_socket           | localhost |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
5 rows in set (0.00 sec)
```
Diese Commands sollten generell reichen um die Funktionalität von MySQL genügend getestet zu haben. 
