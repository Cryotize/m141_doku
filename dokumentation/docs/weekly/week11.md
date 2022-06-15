# Lernziele

Jede/r Lernende:

    ...kann (anhand von Beispielen) komplizierte Abfragen für MongoDB erstellen
    ...Daten in MongoDB aus CSV und JSON importieren
    ...Backups und Restores für ein MongoDB-Instanz durchführen

## Auftrag 1

Als erstes müssen wir eine json Datei importieren. Hierbei MUSS der Dateityp beachtet werden, da wir verschiedene Dateitypen in MongoDB importieren können.

Auftrag ist: mongo_cities1000.json zu importieren, dies funktioniert wie folgt:

```bash
mongoimport mongo_cities1000.json -d world -c cities --drop
```

```-d``` steht für die Datenbank welche automatisch erstellt wird, ```-c``` für die Collection und das ```--drop``` steht da damit eine existente DB oder Collection gedropped wird falls eine mit diesem Namen bereits existiert. 

!> Der Import wird fehlschlagen wenn man die Datei einfach probiert zu importieren nachdem man sie von Moodle herunterlädt. Grund hierfür ist, dass die einzelnen JSON Daten nicht mit einem Komma getrennt sind, das ganze nicht in einem [] eingeklammert ist und die Keys müssen ebenfalls mit einem "" umrahmt sein. Dies habe ich rasch im VSCode ergänzt um es nacher importieren zu können. --legacy wäre ebenfalls eine option um veraltete JSON Dateien importieren zu können. 

## Auftrag 2

Im 2. Auftrag geht es darum alle Städte aus der Zeitzone Europa aufzulisten. Hier müssen wir mit Regex arbeiten, da die Zeitzonen nicht nur "Europa" sondern "Europa/irgendwas" heissen.

Der Command geht daher wie folgt:

```js
db.cities.find({timezone : {$regex : "Europe"}})
```

... möchten wir das ganze noch schön formatieren können wir den Command so ergänzen:

```js
db.cities.find({timezone : {$regex : "Europe"}}).pretty()
```

Der unschöne Output sieht dann so aus:

```json
{ "_id" : ObjectId("6272acd2c8cf404fc83cb56d"), "name" : "Omarska", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 6466, "location" : { "longitude" : 44.89, "latitude" : 16.89917 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb56e"), "name" : "Olovo", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 3305, "location" : { "longitude" : 44.13, "latitude" : 18.58278 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb56f"), "name" : "Odžak", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 11621, "location" : { "longitude" : 45.01111, "latitude" : 18.32667 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb570"), "name" : "Obudovac", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 2171, "location" : { "longitude" : 44.965, "latitude" : 18.61222 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb571"), "name" : "Novi Šeher", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 4416, "location" : { "longitude" : 44.51028, "latitude" : 18.02583 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb572"), "name" : "Nevesinje", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 7313, "location" : { "longitude" : 43.25861, "latitude" : 18.11333 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb573"), "name" : "Neum", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 4200, "location" : { "longitude" : 42.92333, "latitude" : 17.61556 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb574"), "name" : "Mrkonjić Grad", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 14737, "location" : { "longitude" : 44.41583, "latitude" : 17.08611 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb575"), "name" : "Mramor", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 3918, "location" : { "longitude" : 44.59167, "latitude" : 18.56444 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb576"), "name" : "Mostar", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 104518, "location" : { "longitude" : 43.34333, "latitude" : 17.80806 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb577"), "name" : "Modriča", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 5136, "location" : { "longitude" : 44.95444, "latitude" : 18.30361 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb578"), "name" : "Mionica", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 8701, "location" : { "longitude" : 44.86722, "latitude" : 18.46306 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb579"), "name" : "Milići", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 8210, "location" : { "longitude" : 44.17111, "latitude" : 19.09028 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb57a"), "name" : "Maslovare", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 4899, "location" : { "longitude" : 44.56556, "latitude" : 17.53361 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb57b"), "name" : "Marićka", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 3707, "location" : { "longitude" : 44.86861, "latitude" : 16.84056 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb57c"), "name" : "Mala Kladuša", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 6033, "location" : { "longitude" : 45.13417, "latitude" : 15.85861 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb57d"), "name" : "Mahala", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 5062, "location" : { "longitude" : 44.01194, "latitude" : 18.25528 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb57e"), "name" : "Maglajani", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 4221, "location" : { "longitude" : 44.94861, "latitude" : 17.34944 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb57f"), "name" : "Maglaj", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 7399, "location" : { "longitude" : 44.54917, "latitude" : 18.09667 } }
{ "_id" : ObjectId("6272acd2c8cf404fc83cb580"), "name" : "Lukavica", "country" : "BA", "timezone" : "Europe/Sarajevo", "population" : 4084, "location" : { "longitude" : 44.76278, "latitude" : 18.16889 } }
```

!> Der Output ist so immer auf 20 limitiert und man muss ```it``` eingeben um weitere anzuzeigen. Möchte man wirklich alle sehen so kann man den Command so ergänzen:

```js
db.cities.find({timezone : {$regex : "Europe"}}).toArray()
```

So ist alles schön formatiert und alle Städte mit Timezone "Europe" werden aufgelistet.

Eine andere Option wäre es die maximale Output Anzahl allgemein zu erhöhen, dies ist nicht persistent:

```js
DBQuery.shellBatchSize = 300
```

> Als info: Der import mit --legacy würde das Umschreiben vom json unnötig machen und der import würde funktionieren.

## Auftrag 3

Folgende Query müssen wir auf die importierten Daten ausführen:

```js
> db.cities.aggregate([
    {
    $match: {
        'timezone': {
        $eq: 'Europe/London'
        }
    }
    },
    {
    $group: {
        _id: 'averagePopulation',
        avgPop: {
        $avg: '$population'
        }
    }
    }
])
```

Der Output sieht wie folgt aus:

```js
{ "_id" : "averagePopulation", "avgPop" : 23226.22149712092 }
```

Was hier passiert ist, dass wir die Population aller Städten in der Zeitzone "Europe/London" durchgehen und mittels dem "$avg" den Durchschnitt der Bewohner errechnen. Das ganze wird anschliessend als "avgPop" angezeigt mit der id "averagePopulation"

## Auftrag 4

Folgende Query müssen wir auf die importierten Daten ausführen:

```js
> db.cities.aggregate([
    {
    $match: {
        'timezone': {
        $eq: 'Europe/London' /* $eq steht für equals, muss also dem Wort entsprechen */
        }
    }
    },
    {
    $sort: { /* Hier sortieren wir */
        population: -1 /* Der Population absteigend...*/
    }
    },
    {
    $project: { /* Was wollen wir anzeigen, was nicht? */
        _id: 0, /* 0 steht für ausblenden */
        name: 1, /* 1 steht für einblenden */
        population: 1
    }
    }
])
```

Dabei entsteht folgender Output: 

```js
{ "name" : "City of London", "population" : 7556900 }
{ "name" : "London", "population" : 7556900 }
{ "name" : "Birmingham", "population" : 984333 }
{ "name" : "Glasgow", "population" : 610268 }
{ "name" : "Liverpool", "population" : 468945 }
{ "name" : "Leeds", "population" : 455123 }
{ "name" : "Sheffield", "population" : 447047 }
{ "name" : "Edinburgh", "population" : 435791 }
{ "name" : "Bristol", "population" : 430713 }
{ "name" : "Manchester", "population" : 395515 }
{ "name" : "Teesside", "population" : 365323 }
{ "name" : "Leicester", "population" : 339239 }
{ "name" : "Islington", "population" : 319143 }
{ "name" : "Coventry", "population" : 308313 }
{ "name" : "Hull", "population" : 302296 }
{ "name" : "Cardiff", "population" : 302139 }
{ "name" : "Bradford", "population" : 299310 }
{ "name" : "Belfast", "population" : 274770 }
{ "name" : "Stoke-on-Trent", "population" : 260419 }
{ "name" : "Wolverhampton", "population" : 252791 }
```

>! Nur die ersten 20 werden wieder so angezeigt. Siehe Aufgabe 3 für mehr Informationen zu diesem Thema.

In dieser Query sortieren wir alle Städte in der Zeitzone Europe/London nach der Population absteigend. Dabei zeigen wir nur den Namen und die Population an, die _id wird ausgeblendet da der wert auf "0" steht und nicht auf "1".

## Auftrag 5

Im 5. Auftrag sollte ein Backup und Restore unserer Test DB geschehen. In meinem Fall heisst diese jetzt "auftrag1". Diese will ich als Archiv backuppen und anschliessend wieder importieren.

Das Backup funktioniert wie folgt auf dem DB Host:

```bash
mongodump --archive=/home/domi/backups/auftrag1-04-05-2022.archive --db=auftrag1

# Output
#2022-05-04T18:11:26.004+0000	writing auftrag1.cities to archive '/home/domi/backups/auftrag1-04-05-2022.archive'
#2022-05-04T18:11:26.178+0000	done dumping auftrag1.cities (99838 documents)
```

Der Vorteil mit dem Archiv ist, dass alles in dieser Datei gespeichert ist und nicht verschiedene Ordner oder Dateien rumfliegen.

Nun möchten wir das ganze wieder importieren:

```bash
mongorestore --archive=/home/domi/backups/auftrag1-04-05-2022.archive

# Output wäre zu lang, aber es hat funktioniert...
# Wichtig: Der Import funktioniert so nur, wenn die alten Daten nicht mehr gleich vorhanden sind :)
```