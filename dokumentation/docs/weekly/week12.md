# Lernziele

Jede/r Lernende:

    ...kann eine bestehende Applikation auf eine vorhanden Infrastruktur migrieren (bezgl. MongoDB-Konfigurationen)

## Auftrag 1

Als erstes müssen wir eine Software von [folgendem](https://github.com/sbhaseen/express-dashboard-demo) Github Repository klonen. 

```bash
cd ~
git clone https://github.com/sbhaseen/express-dashboard-demo.git
```

Sie baut auf folgenden Technologien auf:

- Express.js - A Node.js web application framework
- Express-Validator - A set of validation middlewares for Express.js applications.
- Mongoose - MongoDB Object Document Mapper
- (Express) Handlebars - A minimal views templating engine.
- Helmet.js - Helmet helps you secure your Express apps by setting various HTTP headers.
- Bootstrap (front-end library) - Bootstrap is an open source toolkit for developing with HTML, CSS, and JS.
- Chart.js - A JavaScript charting library.

In der Applikation kann man verschiedene Prozesse, Kategorien und Instanzen hinzufügen. 

## Auftrag 2

Die Inbetriebnahme ist sehr einfach gehalten. Als erstes müssen wir in den Ordner wechseln wo wir es heruntergeladen haben:

```bash
cd ~/express-dashboard-demo
```

Als nächstes müssen wir die Abhängigkeiten mit npm installieren, in meinem Fall führe ich gleich auch einen Fix für die ganzen Vulnerabilities aus:

```bash
npm install
npm audit fix
```

Jetzt können wir die Applikation starten mittels folgendem Command:

```bash
npm run dev
```

Die Applikation ist über das Web mit dem Port 5000 nun erreichbar. In meinem Fall wäre dies ```http://192.168.100.100:5000```

![Screenshot von der Applikation](../img/express_screenshot.png)

## Auftrag 3

Über das GUI können wir nun einen neuen Datensatz, z.B. eine neue Maschine erfassen. Verifizieren können wir dies anschliessend auch in der GUI, ob sie unter "All Machines" auftaucht.

Die kommunikation funktioniert über den "machineController.js" und das Model welches für das Abspeichern verwendet wird ist "machine.js". Wir arbeiten hier also mit dem Model, View, Controller prinzip. 

Interessant ist auch die Speicherung in der DB, hierfür müssen wir uns erst mal manuell einloggen um die Daten einzusehen.

```bash
mongo --port 27017 --authenticationDatabase "mfgdashboard" -u "applicationuser" -p "AdminDB1$"
```

Nun können wir uns die Dokumente anschauen:

```js
> use mfgdashboard
switched to db mfgdashboard
> show collections
machines
> db.machines.find({}).pretty()
{
	"_id" : ObjectId("62736c7a838d22047b656e05"),
	"name" : "Testmaschine",
	"date_of_commissioning" : ISODate("2022-05-18T00:00:00Z"),
	"date_of_retirement" : ISODate("2022-06-03T00:00:00Z"),
	"__v" : 0
}
```

Hier sehen wir die einzigartige ID, den definierten Namen und die beiden Daten welche wir im Webinterface eingeben können.

Das "__v" kommt vom mongoose Command im Code (Model) und steht für Versionen. Man kann also vom Dokument verschiedene Versionen haben.

## Auftrag 4

Nun möchten wir einen System-Admin und einen Applikationsbenutzer erstellen. Hier gehen wir wie folgt vor in der Mongo Shell:

### Sysadmin

```js
> use admin
switched to db admin
> db.createUser(
... {
...     user: "sysadmin",
...     pwd: passwordPrompt(), // oder cleartext password. passwordPrompt() führt zu einem Prompt auf der Konsole
...     roles: [
...     { role: "userAdminAnyDatabase", db: "admin" },
...     { role: "readWriteAnyDatabase", db: "admin" }
...     ]
... }
... )
Enter password: 
Successfully added user: {
	"user" : "sysadmin",
	"roles" : [
		{
			"role" : "userAdminAnyDatabase",
			"db" : "admin"
		},
        {
			"role" : "readWriteAnyDatabase",
			"db" : "admin"
		}
	]
}

```

Für den Systemadministrator sagen wir, dass dieser in der admin DB erstellt werden soll da dieser eh global ist.

### Applikationsbenutzer

```js
> use mfgdashboard
switched to db mfgdashboard
> db.createUser(
... {
...     user: "applicationuser",
...     pwd: passwordPrompt(), // oder cleartext password. passwordPrompt() führt zu einem Prompt auf der Konsole
...     roles: [
...     { role: "readWrite", db: "mfgdashboard" }
...     ]
... }
... )
Enter password: 
Successfully added user: {
	"user" : "applicationuser",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "mfgdashboard"
		}
	]
}

```
Ich habe mich dazu entschieden, diesen Benutzer in der mfgdashboard Datenbank zu erstellen, während der Systemadministrator in der admin DB erstellt ist.

### Code-Anpassung

Die Code-Anpassung ist sehr einfach, hierzu Linie 5 von server.js editieren:

```bash
vim ~/express-dashboard-demo/server.js
```

... und wie folgt anpassen:

```js
// Alt
const db = process.env.MONGODB_URI || 'mongodb://localhost:27017/mfgdashboard';

// Neu
const db = process.env.MONGODB_URI || 'mongodb://applicationuser:AdminDB1$@localhost:27017/mfgdashboard';
```

Hier ergänzen wir den URI um den Benutzer und das Passwort, welches zwischen dem "mongodb://" und dem "localhost" angegeben wird.

Für eine optimale Sicherheit verwenden wir auch den Applikationsbenutzer, da dieser nur auf diese DB Read und Write Zugriff hat.

Die Informationen dazu habe ich in der offiziellen [Dokumentation](https://www.mongodb.com/docs/manual/reference/connection-string/#std-label-connections-connection-options) gefunden.



