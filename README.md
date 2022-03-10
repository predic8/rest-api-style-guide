# REST API Styleguide

Version: 0.3

# Einführung

Der Guide soll keine API oder REST Einführung ersetzen sondern konkrete Hinweise für die wichtigsten Design-Entscheidungen liefern. Für das Verständnis sind grundlegende REST Kenntnisse notwendig.

## Ziele 
- Beschränkung auf die wichtigsten Punkte 
- Beachtung der REST Prinzipien soweit sinnvoll
- Der Guide soll ein nichtdogmatischer Leitfaden darstellen
- Anschauliche Beispiele anstatt formeller Beschreibungen bereitstellen

Es wird vorausgesetzt, dass die Regeln der HTTP Spezifikation eingehalten werden.

## Abweichung vom Style-Guide

Dieser Guide ist als Richtschnur gedacht. Die einzelnen Punkte sind vielmehr Empfehlungen als verbindliche Regelen. Die Einhaltung soll zu einem guten Design und zur Einheitlichkeit von APIs beitragen.

Es ist möglich und gewollt, dass von diesem Guide Forks erzeugt werden, die individuell angepaßt werden.

## Reife-Modell

Im [Richardson Maturity Model](https://en.wikipedia.org/wiki/Richardson_Maturity_Model) sind die Level 0 bis 2 einzuhalten.

|Level|Beschreibung|Pflicht|
|---|---|---|
|0|JSON, XML oder anderes selbstgeschreibendes Format|ja|
|1|Ressourcen|ja|
|2|HTTP Verben |ja|
|3|Hypermedia Steuerung|optional|



# Requests - Anfragen

## Ressourcen und Pfade 

Das Anlegen, Ändern, Abfragen und Löschen (CRUD) von Daten soll über Ressourcen erfolgen. Für Prozesse und Berechnungen kann vom Konzept der Ressourcen abgewichen werden und statt dessen Funktionen (Verben) verwendet werden. 


Für Pfade dürfen die folgenden Zeichen verwendet werden:

|Zeichen|Beschreibung|
|---|---|
|Buchstaben|a,b,c ... z, A,B,C ... Z||
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

Jedes Geschäftsobjekt, das über die Schnittstelle angesprochen werden kann, muss über einen eindeutigen Pfad adressierbar sein. Das Geschäftsobjekt ist als Ressource zu behandeln. Der Pfad ist der Name der Ressource und muss einem Hauptwort ensprechen.

Für den Pfad sollten möglichst keine Großbuchstaben verwendet werden. Im Querystring, dem Anteil hinter dem Fragezeichen können Großbuchstaben dagegen verwendet werden.

**Regeln:**
- Pfade sind Hauptwörter
- Subressourcen sollten nicht tiefer als drei Ebenen gehen
- Verwendung von Kleinbuchstaben

**Beispiele:**

    https://api.predic8.de/shop/products/42/photo
    https://api.predic8.de/shop/vendors/672/products/
    https://api.predic8.de/partner/112358/vertraege/7


### Einzel Ressourcen

Mit einer Einzel-Ressource wird ein einzelnes Geschäftsobjekt abgebildet.

**Beispiele:**

    /products/{id}


### Listen Ressourcen

Eine Listen-Ressource bildet eine Liste von gleichartigen Geschäftsobjekten ab. 

**Regeln:**

- Am Ende einer Listen-Ressource steht der Schrägstrich **/**.

**Beispiele:**

    /products/
    /customers/


### Filtern und Sortieren 

Mit Parametern im Querystring kann Einfluss auf die Repäsentation einer Listen-Ressource genommen werden.

**Beispiele:**

    ?sort-by=preis&sort-order=asc
    ?stadt=Bonn

### Paginating - Blättern

Paginating kann über den Querystring realisiert werden.

**Beispiele:**

    ?max=10&start=30


### Formate

Als Alternative zu einer Formatangabe über den **Accept** Header kann ein gewünschtes Format auch über einen Query-Parameter angegeben werden. 

**Beispiele:**

    ?format=JSON
    ?format=PDF


## Übersicht Parameter

Die Tabelle zeigt wie verschiedene Parameter übertragen werden können.

|Parameter|Zweck|Beispiele|
|---|---|---|
|Pfad|Teil einer Einzel-Ressource|/produkte/7|
|Query String|Filter, Sortierung|?stadt=bonn|
|Header|Technische Metadaten wie Security-Tokens, Rate Limiting, ...|X-RateLimit|
|Payload|Eigentliche Nutzdaten, Geschäftsobjekte|{ "name": "Herbert" }|



## Methoden

Das Design sollte sich möglichst auf wenige Methoden wie *GET*, *POST*, *PUT* und *DELETE* beschränken. Bei bestimmten Anwendungsfällen kann es Sinn machen, zusätzlich *PATCH* anzubieten. 

|Methode|CRUD|Beschreibung|
|---|---|---|
|GET|Lesen|Daten auf dem Server werden nicht verändert|
|POST|Neuanlege|Wurde erfolgreich eine neue Ressource angelegt, muss das die Response mit dem Status Code *202* kenntlich machen und einen *Location*-Header enthalten, der auf die neue Ressource verweist.|
|PUT|Ändern|Die gesamte Ressource wird mit dem Inhalt des Requestbodies ersetzt.|
|DELETE|Löschen|Die Ressource wird gelöscht|
|PATCH|Teiländerung|Es wird nur ein Teil z.B. einzelne Felder geändert.|



### Verbtabelle - *CRUD* Pattern

Daten auf dem Server können wie in der Tabelle beschrieben gelesen und verändert werden.

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
|200|OK|Der Aufruf war erfolgreich|
|201|Created|Mit dem Aufruf wurde eine neue Ressource erzeugt.|
|202|Accepted|Der Aufruf wurde erfolgreich entgegengenommen. Die eigentliche Verarbeitung findet aber erst später statt|
|400|Bad Request|Die Anfrage ist fehlerhaft|
|404|Not found|Die Ressource konnte nicht gefunden werden bzw. das Geschäftsobjekt existiert nicht.|
|500|Internal Server Error|Bei der Verarbeitung auf dem Server ist ein Problem aufbetreten.|

Die verwendeten Server, API Gateways, Bibliotheken und Frameworks können darüberhinaus weitere Status Codes wie die folgenden verwenden:

307, 401, 403, 405, 422, 502, ...

## Fehlerbehandlung

Fehler bei der Verarbeitung auf dem Server müssen dem Client HTTP Status Codes mitgeteilt werden.

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