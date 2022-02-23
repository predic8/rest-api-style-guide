# API Styleguide für REST-artige Schnittstellen

Version: 0.2

## Ziele 
- Kompaktes Dokument mit den wichtigsten Punkten 
- Beachtung der REST Prinzipien soweit sinnvoll ader nicht dogmatisch
- Beispiele anstatt formeller Beschreibungen
- Soll keine API oder REST Einführung sein. Für das Verständnis sind grundlegende REST Kenntnisse notwendig.

# 
Im [Richardson Maturity Model](https://en.wikipedia.org/wiki/Richardson_Maturity_Model) sind die Level 0 bis 2 einzuhalten.

|Level|Beschreibung|Pflicht|
|---|---|---|
|0|JSON, XML, ... Format|ja|
|1|Ressourcen|ja|
|2|HTTP Verben |ja|
|3|Hypermedia Steuerung|optional|



# Requests - Anfragen

## Ressourcen und Pfade 

Das Anlegen, Ändern, Abfragen und Löschen (CRUD) von Daten soll über Ressourcen erfolgen. Für Prozesse und Berechnungen kann vom Konzept der Ressourcen abgewichen werden und statt dessen Funktionen verwendet werden. 


Für Pfade dürfen die folgenden Zeichen verwendet werden:

|Zeichen|Beschreibung|
|---|---|
|Kleinbuchstaden|a,b,c ... z||
|Ziffern|0..9|
|Sonderzeichen| - / ? = , . _



### Basispfad

Der Basispfad wird allen Aufrufen vorangestellt.




**Beispiele:**

    https://api.predic8.de/fruitshop/v1/
    https://predic8.de/api/
    http://predic8.de/fruit-shop/



### Versionierung

Die Angabe einer Versionsnummer im Pfad ist optional. Versionsnummern sind Ganzzahlen. 


**Beispiel:**

    http:predic8.de/api/v2/

**Regeln:**
- Plural
- Subressourcen
    - Level
- Version
    - Optional für Version 1



### Einzel Ressourcen

### Listen Ressourcen


### Filtern und Sortieren


### Paginating - Blättern


    ...?start=100&max=10


## Methoden


### Verbtabelle

CRUD




||Lesen|Erzeugen|Ändern|Löschen|
|---|---|---|---|---|
|Methode|GET|POST|PUT|DELETE|
|Einzel<br>/produkt/|Lesen der Liste|Erzeugen eines Produktes|Fehler: Status Code 405|Fehler: Status Code 405|
|Liste<br>/produkt/{pid}|Lesen eines Objektes|Fehler: Status 405|Ändern eines Produktes|Löschen eines Produktes|

	
/produkte/	Liste der Produkte	Anlegen eines Produktes		
/produkte/{pid}	Einzelnes Produkt mit der id pid	405		


Kein Patch

- 201 / Location Header


# Responses

Status Codes

|Code|||
|---|---|---|
|200|OK||
|201|Created|Mit dem Aufruf wurde eine neue Ressource erzeugt.|
|202|
|400|||
|401|Unauthorized||
|404|Not found||
|405|||
|500|||






# Datenformate

Für strukturierte Daten sollte JSON verwendet werden. Es können aber auch andere Formate verwendet werden. 

Die Angabe des *Content-Type Headers* ist Pflicht.

**Beispiel:**

    Content-Type: application/json


## Nameskonventionen 

In JSON, XML oder anderen Formaten muss eine einheitliche Schreibweise für Elemente oder Feldnamen verwendet werden.



## Fehlerbehandlung

Fehler bei der Verarbeitung auf dem Server müssen dem Client mit HTTP Status Codes mitgeteilt werden.

### Fehlerbeschreibung im Body (Optional)

Eine Fehlerbeschreibung im Body ist optional und kann weitere Informationen zum Fehler liefern. Eine Fehlerbeschreibung kann im Zielkonflickt mit Sicherheitsanforderungen stehen.

**Beispiel:**
    {
        "code": 400
        "message": "...."
    }


# Hypermedia und Links

Die Verwendung von Hypermedia ist optional. 

## Link ohne Metadata



**Beispiel:**

    {
        "id": 
        "hersteller_uri": /45
        "hersteller_id: 45
    }

## Link mit Metadata



**Beispiel:**

    {
        "_link": {
            "uri": "https://api.predic8.de/shop/order/2242/actions/cancel",
            "method": "POST",
            "rel": "cancel",
        }
    }

# Dokumentation

Eine API sollte möglichst selbsterklärend sein. Entweder erschließt sich die Verwendung der API über Links(Hypermedia) oder die API sollte mit einer Dokumentation versehen werden.

Zum dokumentieren ist möglichst der OpenAPI Standard zu verwenden. Es sind aber auch andere Formen der Dokumentation möglich.

Beispiele mit curl-Aufrufen können die Verwendung einer API erleichtern. 


# OpenAPI

OpenAPI sollte in der Version 3 oder neuer verwendet werden.

**Best Practice:**
- Die OpenAPI Beschreibung sollte über eine *well known URL* abrufbar sein.

**Beispiel:**

    https://predic8.de/api/tickets/v2/api-doc/



# Anweichungen vom Styleguide

Diese Styleguide ist für allgemeine Anwendungsfälle gedacht. Bei speziellen Problem kann eine Abweichungen vom Guide sinnvoll sein.

Eine Erweiterung der Meta- oder Error-Strukturen um zusätzliche Felder stellt keine Abweichung vom Guide dar.