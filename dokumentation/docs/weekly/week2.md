# Woche 2

In der 2. Woche geht es viel um Transaktionen und deren Prinzipien welche hier genauer beschrieben werden. Ich werde auch ein wenig PostgreSQL erklären und die Unterschiede / Vorteile gegenüber MySQL aufzählen. 

### Transaktion(en)

Eine Datenbanktransaktion besteht aus einer Gruppe von Teilaktionen. Hierbei wird z.B. bei Relationalen datenbanken das ACID Prinzip verfolgt, dies ist weiter unten genau beschrieben. Die Gruppe von Aktionen besteht aus Atomarität, Konsistenz, Isolation und Dauerhaftigkeit. All diese Eigenschaften sollten bei einer Transaktion eingehalten werden, auch wenn mehrere Transaktionen parallel auf einer Datenbank durchgeführt werden.

### ACID / AKID

ACID ist eine Abkürzung und beschreibt häufig erwünschte Eigenschaften bei Transaktionen von Datenbankmanagementsystemen.

Hierfür stehen die einzelnen Buchstaben:

#### Atomarität

Es steht im grunde genommen für "Alles oder nichts". In einer Transaktion gibt es verschiedene Datenbank-Operationen welche durchgeführt werden. Eine Transaktion wird erst als "gültig" oder "abgeschlossen" gezählt wenn alle Operationen erfolgreich abgeschlossen wurden, ansonsten ist die Transaktion fehlgeschlagen. 

#### Konsistenzerhaltung

Es bedeutet, dass nach Ende der Transaktion die Datenbank in einem konsistenten Zustand hinterlassen wird. Beinhaltet sind da z.B. die definierten Integritätsbedingungen vom Datenbankschema. Falls dies nicht möglich ist und die konsistenz nicht gewährleistet werden kann, so wird die Transaktion abgebrochen / rückgängig gemacht.  

#### Isolation (Abgrenzung)

Durch die Isolation wird verhindert, das nebenläufige Transaktionen sich gegenseitig beinflussen können. Das ganze funktioniert durch ein Sperrverfahren, so werden z.B. ganze Tabellen während einer Transaktion gesperrt und können durch andere Transaktionen nicht gebraucht werden. Dies kann aber zu Performance reduktion führen, es besteht daher die Option verschiedene Isolationsgrade zu definieren. So kann man z.B. für Diagramme von Logging "Non-Repeatable Read" gebrauchen da es logisch ist, das nach jedem Read andere Daten ausgegeben werden. Es gibt insgesamt vier Isolationsgrade: Dirty Read, Lost Updates, Non-Repeatable Read und Phantom. 

Für weitere Informationen zu diesem Thema gerne einmal [hier](https://de.wikipedia.org/wiki/Isolation_(Datenbank)) vorbeischauen. 

#### Dauerhaftigkeit

Die Dauerhaftigkeit garantiert, dass nach einem Systemausfall Transaktionen und Daten nicht verloren gehen. Dies funktioniert mithilfe von einem Transaktionslog, nachdem wieder das System Online kommt kann überprüft werden, welche Transaktionen bereits auf den Speicher geschrieben wurden und welche nicht. Dies ist vergleichbar zum Journaling Filesystem EXT4 oder NTFS. 

### BASE

Die Abkürzung BASE steht für Basically Available, Soft State, Eventually Consistent und ist bei NoSQL-Systemen sehr verbreitet. Es ist ein neuer Konsistenzbegriff von welchem das Ziel ist, verteile Datenbanksysteme zu verarbeiten ohne jegliche Sperren. Damit dies möglich ist wird das MVCC-Verfahren [(Multiversion Concurrency Control)](https://wikis.gm.fh-koeln.de/Datenbanken/MVCC) verwendet. Bei diesem Verfahren wird in kauf genommen, dass die Daten bis zum Erreichen der Konsistenz inkonsistent gespeichert sind. Der Bereich "Konsistenzerhaltung" aus dem ACID Prinzip ist daher schwer übertragbar. 

### CAP-Theorem

Die Abkürzung CAP steht für Consistency, Availability und Partition tolerance. Es geht dabei um verteile Datenbanksysteme welche nur 2 dieser 3 punkte gleichzeitig erfüllen können. Consistency beschreibt dass alle Clients zur gleichen Zeit die gleichen Daten sehen. Availability steht für die Verfügbarkeit, selbst wenn eine Node Offline ist müssen die Daten immernoch verfügbar sein. Partition tolerance beschreibt das eine Verzögerung oder gar ein Ausfall der Kommunikation unterhalb von Nodes das Cluster nicht zum "Stop" bringen. Das Cluster muss weiterhin funktionieren. Als Beispiel Instagram: Es ist egal ob eine Person in der USA den gleichen Post zur genau gleichen Zeit sieht wie eine Person in Europa. Ein paar Sekunden oder Minuten unterschied spielen hier keine Rolle. Die beiden Anderen Punkte sind dabei viel wichtiger, es soll immer verfügbar sein und auch Unterbrüche aushalten können. Es ähnelt dem Prinzip "Dienstleistungen billig, schnell und von guter Qualität". Man kann sich nur für zwei davon entscheiden, alle drei sind niemals möglich. 

### Praxisauftrag PostgreSQL

Als Beispiel habe ich PostgreSQL gewählt - Grund dafür ist die MVCC fähigkeit. DBMS wie MySQL unterstützen dies z.B. nicht, somit kann seitens MySQL nur ACID gewährleistet werden. PostgreSQL hingegen unterstützt ACID wie auch BASE. Es besteht die Wahl beider Optionen. Die Performance von PostgreSQL kann also um einiges höher sein da Tabellen nicht gesperrt werden müssen. Skalierbarkeit ist ebenfalls ein grosses plus, man hat die Möglichkeit durch MVCC global zu skalieren. PostgreSQL ist auch Objekt-Relational gegenüber nur Relational von MySQL. Es gibt noch viele verschiedene Unterschiede und Vorteile beider DBMS, generell wird aber gesagt das PostgreSQL die fortgeschrittenere Lösung ist. Wichtig ist, das PostgreSQL also als NoSQL DBMS angesehen wird. Einen genaueren Vergleich kann man [hier](https://www.ibm.com/cloud/blog/postgresql-vs-mysql-whats-the-difference) finden.