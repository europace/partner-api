# Partner API v2.0

Das Partnermanagment von EUROPACE 2 dient Organisationen zur Abbildung der eigenen Struktur. 
Hierzu bietet die Weboberfläche des Partnermanagements umfassende Möglichkeiten. 
Die PEX Partner API erlaubt Fremdsystemen einen automatisierten Zugriff auf die Partner 
des Partnermanagement via HTTP.

Bei Fragen und Anregungen entweder ein Issue in GitHub anlegen oder an devsupport@europace2.de schreiben.

# Dokumentation

Über die PEX Partner API ist es möglich
- neue Partner anzulegen,
- Daten zu Partnern abzurufen,
- Partner zu modifizieren.

## Inhalt
* [Die HTTP Schnittstelle](#die-http-schnittstelle)
* [Authentifizierung](#authentifizierung)
* [Datenformat der Partner Stammdaten](#datenformat-der-partner-stammdaten)
* [Anlegen eines neuen Partners](#anlegen-eines-neuen-partners)
* [Abruf eines Partners](#abruf-eines-partners)
* [Modifizieren eines Partners](#modifizieren-eines-partners)
* [Berechtigungen](#berechtigungen)
* [Validierungen](#validierungen)
* [Hinweise](#hinweise)
* [Vorherige Releases](#vorherige-releases)
* [Nutzungsbedingungen](#nutzungsbedingungen)

## Die HTTP Schnittstelle

Die HTTP Schnittstelle der PEX-API ist unter der Basis-Url
```
https://api.europace.de/v2/partner/
```
erreichbar.

Die PEX 2 API unterstützt das Datenformat OpenAPI 3. Unter 
[openapi-generator](https://openapi-generator.tech/) und [swagger.io](https://swagger.io/tools/swagger-codegen/)
kann man sich einen Client in seiner bevorzugten Programmiersprache generieren lassen.

### Authentifizierung

Für jeden Request ist eine Authentifizierung erforderlich. 
Die Authentifizierung erfolgt über den OAuth 2.0 Client-Credentials Flow. 

| Request Header Name | Beschreibung           |
|---------------------|------------------------|
| Authorization       | OAuth 2.0 Bearer Token |

Das Bearer Token kann über die [Authorization-API](https://github.com/europace/authorization-api) angefordert werden. 
Dazu wird ein Client benötigt der vorher von einer berechtigten Person über das Partnermanagement angelegt wurde, 
eine Anleitung dafür befindet sich im [Help Center](https://europace2.zendesk.com/hc/de/articles/360012514780).

Damit der Client für diese API genutzt werden kann, muss im Partnermanagement je nach Operation die Berechtigung
 
- Darf neue Plaketten anlegen
- Darf Partnerdaten lesen
- Darf Partnerdaten schreiben
- Darf Beziehungen zwischen Partnern lesen
- Darf Beziehungen zwischen Partnern schreiben
- Darf Rechte eines Partners lesen 
- Darf Rechte eines Partners schreiben
 
aktiviert sein.   
 
Schlägt die Authentifizierung fehl, erhält der Aufrufer eine HTTP Response mit Statuscode **401 UNAUTHORIZED**.

Hat der Client nicht die benötigte Berechtigung um die Resource abzurufen, erhält der Aufrufer eine HTTP Response mit Statuscode **403 FORBIDDEN**.

## Datenformat der Partner Stammdaten

Die PEX API stellt die Partner Stammdaten als JSON Dokument bereit.

Die aus der Weboberfläche des Partnermanagments bekannte Vererbung von Werten bestimmter Attribute entlang der Hierarchie wird in der API nicht reflektiert. **Geerbte Werte werden also nicht ausgeliefert.**

Als Partner können sowohl Personen als auch Organisationen angelegt werden. Dies wird über das Attribut "typ" beim Anlegen festgelegt. Gültige Werte für das Attribut "typ" sind "PERSON" und "ORGANISATION". Der Default ist "PERSON". **Der Typ eines Partners kann im nachhinein nicht mehr geändert werden.**

Die Datenhaushalte für Personen und Organisationen sind unterschiedlich.

### Datenhaushalt einer Person

```
{
    "partnerId": "...",
    "vorname": "...",
    "nachname": "...",
    "typ": "PERSON",      
    "email": "...",
    "avatar": "...",
    "gesperrt": false,
    "kreditsachbearbeiter": false,
    "parent": {
        "partnerId": "..."
    },
    "anrede": "HERR",     // alternativ: FRAU
    "telefonnummer": "...",
    "firmenname": "...",
    "anschrift": {
        "strasse": "...",
        "hausnummer": "...",
        "plz": "...",
        "ort": "..."
    },
    "bankverbindung": {
        "kontoinhaber": "...",
        "bic": "...",
        "iban": "..."
    }
}
```

Dabei gilt:
- Die Reihenfolge der Attribute spielt keine Rolle.
- Die Attributbezeichner sind case-sensitive.
- Zeilenumbrüche können durch '\n' erreicht werden.
- Gültige Werte für das Attribut "anrede" sind "HERR" und "FRAU".

### Datenhaushalt einer Organisation

```
{
    "partnerId": "...",
    "name": "...",
    "typ": "ORGANISATION",      
    "email": "...",
    "avatar": "...",
    "gesperrt": false,
    "parent": {
        "partnerId": "..."
    },
    "telefonnummer": "...",
    "firmenname": "...",
    "webseite": "...",
    "anschrift": {
        "strasse": "...",
        "hausnummer": "...",
        "plz": "...",
        "ort": "..."
    },
    "bankverbindung": {
        "kontoinhaber": "...",
        "bic": "...",
        "iban": "..."
    }
}
```
Dabei gilt:
- Die Reihenfolge der Attribute spielt keine Rolle.
- Die Attributbezeichner sind case-sensitive.
- Zeilenumbrüche können durch '\n' erreicht werden.

### TraceId zur Nachverfolgbarkeit von Requests

Für jeden Request sollte eine eindeutige id (TraceId) generiert werden, die den Request im EUROPACE 2 System nachverfolgbar macht und so bei etwaigen Problemen oder Fehlern die systemübergreifende Analyse erleichtert.
Die Übermittlung der TraceId erfolgt über einen HTTP Header und wird als 
solcher auch in der Response zurückgeliefert. 
Wird keine TraceId übermittelt, enthält die Response eine in EUROPACE 2 
automatisch erzeugte TraceId. 

Request / Response Header Name | Beschreibung
-----------------|-------------
X-TraceId        | eindeutige Id für jeden Request

### Content-Type

Die Schnittstelle liefert Daten auschließlich mit Content-Type "application/json". Entsprechend muß im Request der Accept-Header gesetzt werden:

Request Header Name | Header Value
------------|-------------
Accept      | application/json

## Anlegen eines neuen Partners

Partner können per HTTP POST angelegt werden.
Das Anlegen eines neuen Partners erfolgt immer unterhalb eines bestehenden Partners. 
Das Url-Template für das Anlegen eines neuen Partners unterhalb von {PartnerId} lautet:

```
https://api.europace.de/v2/partner/{PartnerId}/untergeordnetePartner
```

Die Daten werden als JSON Dokument im Body des POST Requests übermittelt.

Bei der serverseitigen Auswertung gelten folgende Regeln:

- unbekannte Attribute werden ignoriert.
- Für Organisationen werden personenspezifische Attribute ignoriert.
- Für Personen werden organisationenspezifische Attribute ignoriert.
- Leere Attribute bei Strings ("") werden ignoriert.
- "id" ist nicht setzbar und wird deshalb ignoriert.
- Rechte werden für Personen -sofern nicht angegeben- mit "false" belegt.

Ein erfolgreicher Aufruf resultiert in einer Response mit dem HTTP Statuscode **201 CREATED**.

Der Body der Response enthält die aktuellen Stammdaten im JSON Format. 
Dies kann zur Erfolgskontrolle genutzt werden. Attribute, die serverseitig gesetzt werden bzw. für die es Defaultwerte gibt, sind dabei immer enthalten. 

Im HTTP Header "Location" befindet sich die Url des neu angelegten Partners.

### POST Request Beispiel
```
POST /v2/partner/SZY92/untergeordnete HTTP/1.1
Host: api.europace.de
Accept: application/json
X-Trace-Id: ff-request-2020-08-28-07-55
Authorization: Bearer eyJraWQiO...
Content-Type: application/json

{
   "anrede":"HERR",
   "vorname":"Max",
   "nachname":"Mustermann",
   "email":"max.musterman@exmaple.org",
   "gesperrt":false,
   "kreditsachbearbeiter":true,
   "externePartnerId":"123456",
   "titelFunktion":"Softwareentwickler",
   "geburtsdatum":"1970-01-01",
   "telefonnummer":"030 123456",
   "mobilnummer":"030 123456",
   "faxnummer":"030 123456",
   "firmenname":"Europace AG",
   "firmennameZusatz":"Aktiengesellschaft",
   "webseite":"https://github.com/europace/partner-api",
   "anschrift":{
      "strasse":"Musterstraße",
      "hausnummer":"5",
      "plz":"12345",
      "ort":"Musterstadt"
   },
   "bankverbindung":{
      "kontoinhaber":"Max Musterman",
      "bic":"BYLADEM1001",
      "iban":"DE02120300000000202051",
      "referenzFeld":"Test Ref"
   },
   "aufsichtsbehoerde":"Musterbehoerde",
   "registrierungsnummer":"987654"
}
```

### POST Response Beispiele
```
201 CREATED
Location: https://api.europace2.de/v2/partner/LUI92
Content-Type: application/json;charset=utf-8

{
   "id": "LUI92",
   "typ": "PERSON",
   "avatarUrl": "https://www.europace2.de/partnermanagement/d71c58aeaed4022384b169f83e0d8842.avatar?anonymousAvatar=BENUTZER",
   "anrede":"HERR",
   "vorname":"Max",
   "nachname":"Mustermann",
   "email":"max.musterman@exmaple.org",
   "gesperrt":false,
   "kreditsachbearbeiter":true,
   "externePartnerId":"123456",
   "titelFunktion":"Softwareentwickler",
   "geburtsdatum":"1970-01-01",
   "telefonnummer":"030 123456",
   "mobilnummer":"030 123456",
   "faxnummer":"030 123456",
   "firmenname":"Europace AG",
   "firmennameZusatz":"Aktiengesellschaft",
   "webseite":"https://github.com/europace/partner-api",
   "anschrift":{
      "strasse":"Musterstraße",
      "hausnummer":"5",
      "plz":"12345",
      "ort":"Musterstadt"
   },
   "bankverbindung":{
      "kontoinhaber":"Max Musterman",
      "bic":"BYLADEM1001",
      "iban":"DE02120300000000202051",
      "referenzFeld":"Test Ref"
   },
   "aufsichtsbehoerde":"Musterbehoerde",
   "registrierungsnummer":"987654"
}
```

## Abruf eines Partners

Die Stammdaten eines Partners können mittels HTTP-GET Methode abgerufen werden. Sie werden als JSON Dokument zurückgeliefert.
Das Url-Template für den Abfruf lautet:

```
https://api.europace.de/v2/partner/{PartnerId}
```

Ein erfolgreicher Aufruf resultiert in einer Response mit dem HTTP Statuscode **200 OK**. 

Der Body der Response enthält die aktuellen Stammdaten im JSON Format.
Attribute, die nicht gesetzt sind, sind in der Response nicht enthalten.

### GET Request Beispiel:
```
GET /v2/partner/LUI92 HTTP/1.1
Host: api.europace.de
Accept: application/json
X-Trace-Id: ff-request-2020-08-28-07-55
Authorization: Bearer eyJraWQiO...
```

### GET Response Beispiel:
```
200 OK
Content-Type: application/json;charset=utf-8

{
   "id": "LUI92",
   "typ": "PERSON",
   "avatarUrl": "https://www.europace2.de/partnermanagement/d71c58aeaed4022384b169f83e0d8842.avatar?anonymousAvatar=BENUTZER",
   "anrede":"HERR",
   "vorname":"Max",
   "nachname":"Mustermann",
   "email":"max.musterman@exmaple.org",
   "gesperrt":false,
   "kreditsachbearbeiter":true,
   "externePartnerId":"123456",
   "titelFunktion":"Softwareentwickler",
   "geburtsdatum":"1970-01-01",
   "telefonnummer":"030 123456",
   "mobilnummer":"030 123456",
   "faxnummer":"030 123456",
   "firmenname":"Europace AG",
   "firmennameZusatz":"Aktiengesellschaft",
   "webseite":"https://github.com/europace/partner-api",
   "anschrift":{
      "strasse":"Musterstraße",
      "hausnummer":"5",
      "plz":"12345",
      "ort":"Musterstadt"
   },
   "bankverbindung":{
      "kontoinhaber":"Max Musterman",
      "bic":"BYLADEM1001",
      "iban":"DE02120300000000202051",
      "referenzFeld":"Test Ref"
   },
   "aufsichtsbehoerde":"Musterbehoerde",
   "registrierungsnummer":"987654"
}
```

## Details zu einem Partner

### Partnerkennzeichen

Die Partnerkennzeichen identifizieren einen Vertrieb bei einem Produktanbieter.
Die erfassten Werte können über diesen Endpunkt ausgelesen werden.

#### GET Request Beispiele

```
GET /v2/partner/LUI92/partnerkennzeichen HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJ...
X-TraceId: request-2020-08-28-07-59
Accept: application/json
```

#### GET Response Beispiel:

```
200 OK
Content-Type: application/json;charset=utf-8

{
  "dslSapGeschaeftspartnerNummerFuerRatenkredit": "cdts-dsl-sap-vo",
  "dslVertriebsWegSchluesselFuerRatenkredit": "1899026629",
  "dkbVertriebsIdFuerRatenkredit": "cdts-dkb-vertrieb",
  "kennzeichenIngDiba": {
    "vermittlerNummer": "9800002186",
    "vermittlerVorname": "wer",
    "vermittlerNachname": "wer",
    "vermittlerNummerFuerRatenkredit": "cdts-ing-diba-vermittler"
  },
  "kennzeichenErgo": {},
  "kennzeichenBhw": {
    "vgeNummer": "6248/205585.0",
    "orgaNummer": "ORGA123",
    "zurIdentifikationsPruefungLegitimiert": "false"
  },
  "lbsOstVermittlerNummer": "lbsost-werwer-vermittler",
  "lbsShhVermittlerNummer": "lbs-ssh-ldapvermittler",
  "lbsSuedwestVermittlerNummer": "lbs-südwest-ldapvermittler",
  "lbsWestVermittlerNummer": "lbs-west-ldapvermittler",
  "allianzVertreterNummer": "01/012/0123",
  "axaGeschaeftsPartnerNummer": "MA 00352",
  "dslVermittlerGruppenSchluessel": "1200",
  "bshVoNummer": "abcdefghijklmnopqrstuvwxyz",
  "bshAkquisitionsWeg": "AW1",
  "bshBlzVertriebsbank": "1234567890",
  "dzHypVermittlerNummer": "1234",
  "alteLeipzigerVerbundVermittlerNummer": "42",
  "rundvBankAgenturNummer": "RV_BAN",
  "rundvBlzVertriebsbank": "RV_BLZ"
}
```

## Rechte eines Partners

Jeder Partner darf unterschiedliche Aktionen zu anderen Partnern ausführen. Das betrifft die Modifikation eines Partners, sowie
die Interaktion mit Vorgängen eines Partners.

### Administrationsrecht

Dieses Recht besagt, dass man die Daten zum anderen Partner ändern darf. Darüber hinaus, dürfen die Berechtigungen angepasst werden.
Dies jedoch nur zu Rechten, die der ausführende Partner selbst besitzt.

#### GET Request Beispiel:

```
GET /v2/partner/SZY92/administrierbare HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json
```

#### GET Response Beispiel:

```
200 OK
Content-Type: application/json;charset=utf-8

{ "content": [{ 
    "partnerId":"VGD43"
  }]
}
```

### Übernahmerecht

Das Übernahmerecht besagt, dass der Partner auf alle Vorgänge des anderen Partners lesend und schreiben zugreifen darf.

#### GET Request Beispiel (viele Partner):

```
GET /v2/partner/LUI92/uebernehmbare HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJ
Content-Type: application/json
X-TraceId: ff-request-2020-08-28-07-59
```

#### GET Response Beispiel (viele Partner):

```
200 OK
Content-Type: application/json;charset=utf-8

{ "content": [{ 
    "partnerId":"VGD43"
  }]
}
```

#### GET Request Beispiel (ein Partner):

```
GET /v2/partner/LUI92/uebernahmeRechtFuer/SZY92 HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOi...
Content-Type: application/json
X-TraceId: ff-request-2020-08-28-07-59
```

#### GET Response Beispiel (ein Partner):

```
200 OK
Content-Type: application/json;charset=utf-8

{
  "partner": {
    "partnerId": "SZY92",
    "gesperrt": false
  },
  "uebernehmbar": true
}
```


## Modifizieren eines Partners

Attribute eines Partners können mittels HTTP PATCH modifiziert werden.
Dabei werden **ausschließlich** diejenigen Attribute überschrieben, die im PATCH Request enthalten sind. Alle anderen Attribute werden nicht geändert.

Hintergrund: Der Datenhaushalt der API ist kleiner als der eines Partners im EUROPACE 2 Partnermanagement. Zudem ist der Datenhaushalt externer Systeme i.d.R. kleiner als der der API. Damit Werte, die über die Weboberfläche "per Hand" eingetragene wurden, nicht durch (fehlende) Attribute eines API Aufrufs verloren gehen, wird die PATCH Semantik angewandt.

Das Url-Template ist dasselbe wie für den Abruf eines Partners:

```
https://api.europace.de/v2/partner/{PartnerId}
```

Die Daten werden als JSON Dokument im Body des PATCH Requests übermittelt.

Bei der serverseitigen Auswertung gelten folgende Regeln:

- **leere Attribute bei Strings ("") löschen den bestehenden Wert.**
- unbekannte Attribute werden ignoriert.
- "id" ist nicht änderbar und wird deshalb ignoriert.
- "typ" ist nicht änderbar und wird deshalb ignoriert.

Ein erfolgreicher Aufruf resultiert in einer Response mit dem HTTP Statuscode **200 OK**.

Der Body der Response enthält die aktuellen Stammdaten im JSON Format.
Dies kann zur Erfolgskontrolle genutzt werden. Attribute, die bereits gesetzt waren bzw. für die es Default Werte gibt, sind dabei immer enthalten.

### Attribute die per PATCH geändert werden können

- anrede
- anschrift
- aufsichtsbehoerde
- bankverbindung
- email
- externePartnerId
- faxnummer
- firmenname
- firmennameZusatz
- geburtsdatum
- kreditsachbearbeiter
- mobilnummer
- name
- vorname 
- nachname
- registrierungsnummer
- telefonnummer
- titelFunktion
- webseite

### Beispiel: HTTP PATCH Request und Response

```
PATCH /v2/partner/BVF52 HTTP/1.1
Host: api.europace.de
Authorization: Bearer eyJraWQiOiJWRDZZ...
Accept: application/json
X-TraceId: ff-request-2020-08-28
Content-Type: application/json
Content-Length: 779

{
   "anrede":"HERR",
   "vorname":"Max",
   "nachname":"Mustermann",
   "email":"max.musterman@exmaple.org",
   "gesperrt":false,
   "kreditsachbearbeiter":true,
   "externePartnerId":"123456",
   "titelFunktion":"Softwareentwickler",
   "geburtsdatum":"1970-01-01",
   "telefonnummer":"030 123456",
   "mobilnummer":"030 123456",
   "faxnummer":"030 123456",
   "firmenname":"Europace AG",
   "firmennameZusatz":"Aktiengesellschaft",
   "webseite":"https://github.com/europace/partner-api",
   "anschrift":{
      "strasse":"Musterstraße",
      "hausnummer":"5",
      "plz":"12345",
      "ort":"Musterstadt"
   },
   "bankverbindung":{
      "kontoinhaber":"Max Musterman",
      "bic":"BYLADEM1001",
      "iban":"DE02120300000000202051",
      "referenzFeld":"Test Ref"
   },
   "aufsichtsbehoerde":"Musterbehoerde",
   "registrierungsnummer":"987654"
}
```

```
200 OK
Content-Type: application/json;charset=utf-8

{
   "partnerId":"BVF52",
   "vorname":"Max",
   "nachname":"Mustermann",
   "typ":"PERSON",
   "email":"max.musterman@exmaple.org",
   "avatar":"https://ep2.mtp.rz-hypoport.local/partnermanagement/d71c58aeaed4022384b169f83e0d8842.avatar?anonymousAvatar=BENUTZER",
   "gesperrt":false,
   "kreditsachbearbeiter":true,
   "parent":{
      "partnerId":"SZY92"
   },
   "externePartnerId":"123456",
   "titelFunktion":"Softwareentwickler",
   "anrede":"HERR",
   "geburtsdatum":"1970-01-01",
   "telefonnummer":"030 123456",
   "mobilnummer":"030 123456",
   "faxnummer":"030 123456",
   "firmenname":"Europace AG",
   "firmennameZusatz":"Aktiengesellschaft",
   "webseite":"https://github.com/europace/partner-api",
   "anschrift":{
      "strasse":"Musterstraße",
      "hausnummer":"5",
      "plz":"12345",
      "ort":"Musterstadt"
   },
   "bankverbindung":{
      "referenzFeld":"Test Ref",
      "kontoinhaber":"Max Musterman",
      "bic":"BYLADEM1001",
      "iban":"DE02120300000000202051"
   },
   "aufsichtsbehoerde":"Musterbehoerde",
   "registrierungsnummer":"987654"
}
```

## Berechtigungen

- für den Zugriff auf einen Partner benötigt der Aufrufer grundsätzlich die Berechtigung, diesen zu sehen. Ist sie nicht vorhanden, erhält der Aufrufer eine HTTP Response mit Statuscode **404 NOT FOUND**.
- für Änderungen an einem Partner benötigt der Aufrufer Einstellungsrechte auf diesen. Sind sie nicht vorhanden, erhält der Aufrufer eine HTTP Response mit Statuscode **403 FORBIDDEN**.
- für das Anlegen neuer Partner benötigt der Aufrufer das Recht "Darf Organisationseinheiten anlegen" sowie Einstellungsrechte auf denjenigen Partner, unterhalb dessen der neue Partner angelegt werden soll. Sind diese nicht vorhanden, erhält der Aufrufer eine HTTP Response mit Statuscode **403 FORBIDDEN**.
- Rechte können nur vergeben bzw. geändert werden, wenn der Aufrufer sie selbst besitzt. Ist dies nicht gegeben, erhält der Aufrufer eine HTTP Response mit Statuscode **403 FORBIDDEN**. 

## Validierungen

Folgende Attribute werden validiert:

- "typ" gültige Werte: "PERSON", "ORGANISATION"
- "anrede" gültige Werte: "HERR", "FRAU"
- "geburtsdatum" gültiges Format ISO8601 extended: "YYYY-MM-DD" 

Schlägt eine dieser Validierungen fehl, erhält der Aufrufer eine HTTP Response mit Statuscode **400 BAD REQUEST**. Im Response Body befinden sich Details zur fehlgeschlagenen Validierung.

## Hinweise

### Anforderungen an die Vollständigkeit für BaufiSmart

Für eine erfolgreiche Angebotsannahme in BaufiSmart ist es für die nachfolgenden Prozesse erforderlich, daß für den Kundenbetreuer des Vorgangs die folgenden Attribute im Partnermanagement gepflegt sind:

- anrede
- vorname
- nachname
- anschrift
- bankverbindung
- email

Eine Angebotsannahme ist andernfalls nicht möglich.

## Vorherige Releases 

- [PEX v1.2](https://github.com/europace/partner-api/blob/1.0.2/README.md) 

## Nutzungsbedingungen

Die APIs werden unter folgenden [Nutzungsbedingungen](https://developer.europace.de/terms/) zur Verfügung gestellt.
