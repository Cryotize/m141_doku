```bash
[domi@elitebook ~]$ ssh 192.168.100.100
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-107-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu 21 Apr 2022 06:36:11 AM UTC

  System load:  0.0               Processes:               157
  Usage of /:   39.4% of 9.78GB   Users logged in:         0
  Memory usage: 14%               IPv4 address for enp1s0: 192.168.100.100
  Swap usage:   0%

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Wed Apr 20 19:04:30 2022 from 192.168.100.1
[domi@datenbank ~]$ wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
[sudo] password for domi: 
OK
[domi@datenbank ~]$ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse
[domi@datenbank ~]$ sudo apt-get update
Hit:1 http://ch.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://ch.archive.ubuntu.com/ubuntu focal-updates InRelease                                  
Hit:3 http://ch.archive.ubuntu.com/ubuntu focal-backports InRelease                                
Hit:4 http://ch.archive.ubuntu.com/ubuntu focal-security InRelease                                                   
Ign:5 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 InRelease                                            
Hit:6 https://deb.nodesource.com/node_14.x focal InRelease                                         
Get:7 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 Release [4,412 B]
Get:8 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 Release.gpg [801 B]
Get:9 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse arm64 Packages [12.5 kB]
Get:10 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 Packages [14.3 kB]
Fetched 32.0 kB in 1s (36.4 kB/s)                         
Reading package lists... Done
[domi@datenbank ~]$ sudo apt-get install -y mongodb-org
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  mongodb-database-tools mongodb-mongosh mongodb-org-database mongodb-org-database-tools-extra mongodb-org-mongos
  mongodb-org-server mongodb-org-shell mongodb-org-tools
The following NEW packages will be installed:
  mongodb-database-tools mongodb-mongosh mongodb-org mongodb-org-database mongodb-org-database-tools-extra
  mongodb-org-mongos mongodb-org-server mongodb-org-shell mongodb-org-tools
0 upgraded, 9 newly installed, 0 to remove and 36 not upgraded.
Need to get 147 MB of archives.
After this operation, 464 MB of additional disk space will be used.
Get:1 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-database-tools amd64 100.5.2 [46.5 MB]
Get:2 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-mongosh amd64 1.3.1 [40.8 MB]
Get:3 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-org-shell amd64 5.0.7 [14.4 MB]
Get:4 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-org-server amd64 5.0.7 [26.3 MB]
Get:5 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-org-mongos amd64 5.0.7 [18.5 MB]
Get:6 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-org-database-tools-extra amd64 5.0.7 [7,752 B]
Get:7 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-org-database amd64 5.0.7 [3,540 B]
Get:8 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-org-tools amd64 5.0.7 [2,892 B]
Get:9 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0/multiverse amd64 mongodb-org amd64 5.0.7 [2,932 B]   
Fetched 147 MB in 19s (7,915 kB/s)                                                                                   
Selecting previously unselected package mongodb-database-tools.
(Reading database ... 116000 files and directories currently installed.)
Preparing to unpack .../0-mongodb-database-tools_100.5.2_amd64.deb ...
Unpacking mongodb-database-tools (100.5.2) ...
Selecting previously unselected package mongodb-mongosh.
Preparing to unpack .../1-mongodb-mongosh_1.3.1_amd64.deb ...
Unpacking mongodb-mongosh (1.3.1) ...
Selecting previously unselected package mongodb-org-shell.
Preparing to unpack .../2-mongodb-org-shell_5.0.7_amd64.deb ...
Unpacking mongodb-org-shell (5.0.7) ...
Selecting previously unselected package mongodb-org-server.
Preparing to unpack .../3-mongodb-org-server_5.0.7_amd64.deb ...
Unpacking mongodb-org-server (5.0.7) ...
Selecting previously unselected package mongodb-org-mongos.
Preparing to unpack .../4-mongodb-org-mongos_5.0.7_amd64.deb ...
Unpacking mongodb-org-mongos (5.0.7) ...
Selecting previously unselected package mongodb-org-database-tools-extra.
Preparing to unpack .../5-mongodb-org-database-tools-extra_5.0.7_amd64.deb ...
Unpacking mongodb-org-database-tools-extra (5.0.7) ...
Selecting previously unselected package mongodb-org-database.
Preparing to unpack .../6-mongodb-org-database_5.0.7_amd64.deb ...
Unpacking mongodb-org-database (5.0.7) ...
Selecting previously unselected package mongodb-org-tools.
Preparing to unpack .../7-mongodb-org-tools_5.0.7_amd64.deb ...
Unpacking mongodb-org-tools (5.0.7) ...
Selecting previously unselected package mongodb-org.
Preparing to unpack .../8-mongodb-org_5.0.7_amd64.deb ...
Unpacking mongodb-org (5.0.7) ...
Setting up mongodb-mongosh (1.3.1) ...
Setting up mongodb-org-server (5.0.7) ...
Adding system user `mongodb' (UID 114) ...
Adding new user `mongodb' (UID 114) with group `nogroup' ...
Not creating home directory `/home/mongodb'.
Adding group `mongodb' (GID 118) ...
Done.
Adding user `mongodb' to group `mongodb' ...
Adding user mongodb to group mongodb
Done.
Setting up mongodb-org-shell (5.0.7) ...
Setting up mongodb-database-tools (100.5.2) ...
Setting up mongodb-org-mongos (5.0.7) ...
Setting up mongodb-org-database-tools-extra (5.0.7) ...
Setting up mongodb-org-database (5.0.7) ...
Setting up mongodb-org-tools (5.0.7) ...
Setting up mongodb-org (5.0.7) ...
Processing triggers for man-db (2.9.1-1) ...
[domi@datenbank ~]$ systemctl status mongod
● mongod.service - MongoDB Database Server
     Loaded: loaded (/lib/systemd/system/mongod.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
       Docs: https://docs.mongodb.org/manual
[domi@datenbank ~]$ sudoedit lib/systemd/system/mongod.service
sudoedit: lib/systemd/system/mongod.service: No such file or directory
[domi@datenbank ~]$ sudoedit /lib/systemd/system/mongod.service
sudoedit: /lib/systemd/system/mongod.service unchanged
[domi@datenbank ~]$ sudo systemctl start mongod
[domi@datenbank ~]$ systemctl status mongod
● mongod.service - MongoDB Database Server
     Loaded: loaded (/lib/systemd/system/mongod.service; disabled; vendor preset: enabled)
     Active: active (running) since Thu 2022-04-21 06:46:20 UTC; 3s ago
       Docs: https://docs.mongodb.org/manual
   Main PID: 2677 (mongod)
     Memory: 65.4M
     CGroup: /system.slice/mongod.service
             └─2677 /usr/bin/mongod --config /etc/mongod.conf

Apr 21 06:46:20 datenbank systemd[1]: Started MongoDB Database Server.
[domi@datenbank ~]$ sudo systemctl enable mongod
Created symlink /etc/systemd/system/multi-user.target.wants/mongod.service → /lib/systemd/system/mongod.service.
```