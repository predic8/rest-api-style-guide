# REST API Styleguide

Version: 0.2

# Einführung

Der Guide soll keine API oder REST Einführung ersetzen sondern konkrete Hinweise für die wichtigsten Design-Entscheidungen liefern. Für das Verständnis sind grundlegende REST Kenntnisse notwendig.

## Ziele 
- Beschränkung auf die wichtigsten Punkte 
- Beachtung der REST Prinzipien soweit sinnvoll ader nicht dogmatisch
- Beispiele anstatt formeller Beschreibungen

## Reife-Modell

Im [Richardson Maturity Model](https://en.wikipedia.org/wiki/Richardson_Maturity_Model) sind die Level 0 bis 2 einzuhalten.

|Level|Beschreibung|Pflicht|
|---|---|---|
|0|JSON, XML, ... Format|ja|
|1|Ressourcen|ja|
|2|HTTP Verben |ja|
|3|Hypermedia Steuerung|optional|



# Requests - Anfragen

## Ressourcen und Pfade 

Das Anlegen, Ändern, Abfragen und Löschen (CRUD) von Daten soll über Ressourcen erfolgen. Für Prozesse und Berechnungen kann vom Konzept der Ressourcen abgewichen werden und statt dessen Funktionen (Verben) verwendet werden. 


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

    http://predic8.de/api/shop/v2/

### Ressourcen

Jedes Geschäftsobjekt, das über die Schnittstelle angesprochen werden kann muss über einen eindeutigen Pfad adressierbar sein. Das Geschäftsobjekt ist als Ressource zu behandeln. Der Pfad, die URI ist der Name der Ressource. Der Name muss einem Hauptwort ensprechen.

**Regeln:**
- Pfade sind Hauptwörter

Subressourcen können gebildet werden.

**Beispiele:**

    https://api.predic8.de/shop/products/42/photo
    https://api.predic8.de/shop/vendors/672/products/
    https://api.predic8.de/partner/112358/vertraege/7


### Einzel Ressourcen

### Listen Ressourcen


### Filtern und Sortieren

?sort-by=

### Paginating - Blättern


    ...?start=100&max=10


## Methoden

Das Design sollte sich möglichst auf wenige Methoden wie GET, POST, PUT und DELETE beschränken. Bei bestimmten Anwendungsfällen kann es Sinn machen zusätzlich PATCH anzubieten. 

|Methode|CRUD|Beschreibung|
|---|---|---|
|GET|Lesen||
|POST|Neuanlegen|Wurde erfolgreich eine neue Ressource angelegt, muss die Response einen *Location* Header enthalten, der auf die neue Ressource verweist.|
|PUT|Ändern|Die gesamte Ressource wird mit dem Inhalt des Requestbodies ersetzt.|
|DELETE|Löschen||
|PATCH|Teiländerung|Es wird nur ein Teil z.B. einzelne Felder geändert.|


### Verbtabelle - CRUD Pattern
Der Life-Cycle von Ressourcen kann wie in der Tabelle beschrieben verändert werden.

||Lesen|Erzeugen|Ändern|Löschen|
|---|---|---|---|---|
|Methode|GET|POST|PUT|DELETE|
|Einzel<br>/produkte/|Lesen der Liste|Erzeugen eines Produktes|Fehler: Status Code 405|Fehler: Status Code 405|
|Liste<br>/produkt/{pid}|Lesen eines Objektes|Fehler: Status 405|Ändern eines Produktes|Löschen eines Produktes|


# Responses - Antworten

## Status Codes

Für die Rückmeldung an den Client sind die HTTP Status Codes zu verwenden. Der Server-Code sollte sich auf die Verwendung der folgenden Status Codes beschränken. Von der Verwendung von exotischen und selten verwendeten Status Codes ist abzusehen.  

|Code|||
|---|---|---|
|200|OK||
|201|Created|Mit dem Aufruf wurde eine neue Ressource erzeugt.|
|202|Accepted
|400|Bad Request||
|404|Not found||
|500|Internal Server Error||

Die verwendeten Server, API Gateways, Bibliotheken und Frameworks können darüberhinaus weitere Status Codes wie die folgenden verwenden:

307, 401, 403, 405, 502, ...

## Fehlerbehandlung

Fehler bei der Verarbeitung auf dem Server müssen dem Client mit HTTP Status Codes mitgeteilt werden.

### Fehlerbeschreibung im Body (Optional)

Eine Fehlerbeschreibung im Body ist optional und kann weitere Informationen zum Fehler liefern. Eine Fehlerbeschreibung kann im Zielkonflikt mit Sicherheitsanforderungen stehen.

**Beispiel:**
    {
        "code": 400
        "message": "...."
    }


# Datenformate

Generell kann jedes Datenformat verwendet werden. Für strukturierte Daten wie Produktinformationen, Rechnungen oder Tickets sollte JSON verwendet werden. Es können aber auch andere Formate zum Einsatz kommen. 

Die Angabe des *Content-Type Headers* ist Pflicht.

**Beispiel:**

    Content-Type: application/json


## Nameskonventionen 

In JSON, XML oder anderen Formaten muss eine einheitliche Schreibweise für Elemente oder Feldnamen verwendet werden.


# Hypermedia und Links

Die Verwendung von Hypermedia ist optional. Hypermedia ist ein interessantes und zentrales Konzept von REST. Leider wurde Hypermedia von der 

## Link ohne Metadata

Für Links ohne Metadaten können einfache Felder verwendet werden. 

**Beispiel:**

    {
        "id": 31415
        "hersteller_uri": "https://api.predic8.de/mm/hersteller/45
        "hersteller_id: 45
    }

**Best Practice:**

- Neben dem Link kann zusätzlich eine Id zurückgegeben werden, damit Client Entwickler, die keine Hypermedia Links verfolgen die URIs nicht parsen müssen. 


## Link mit Metadata

Ein Link kann zusätzlich mit Metadata versehen werden. Dafür sollte möglichst auf ein vorhandenes Format wie *JSON API* oder *Hypertext Application Language(HAL)* zurückgegriffen werden.


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