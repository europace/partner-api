# Beispiel: neuen Partner anlegen

Das Anlegen eines neuen Partners erfolgt immer unterhalb eines bestehenden Partners:
```
https://api.europace.de/v2/partner/{PartnerId}/untergeordnete
```

Voraussetzungen:
* OAuth Token hat den Scope `partner:plakette:anlegen `
* Aufrufer ist ein Partner vom Typ Person
* Aufrufer hat das Recht "Darf Organisationseinheiten anlegen" 
* Aufrufer hat Einstellungsrechte auf den Partner, unter dem der neue Partner angelegt werden soll

Beispiel-Request:
``` curl 
POST /v2/partner/ABC12/untergeordnete HTTP/1.1
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

Bei der serverseitigen Auswertung gelten folgende Regeln:

- unbekannte Attribute werden ignoriert.
- Für Organisationen werden personenspezifische Attribute ignoriert.
- Für Personen werden organisationenspezifische Attribute ignoriert.
- Leere Attribute bei Strings ("") werden ignoriert.
- "partnerId" ist nicht setzbar und wird deshalb ignoriert.
- Rechte werden für Personen -sofern nicht angegeben- mit "false" belegt.

Der Body der Response enthält die aktuellen Stammdaten im JSON Format.
Dies kann zur Erfolgskontrolle genutzt werden. Attribute, die serverseitig gesetzt werden bzw. für die es Defaultwerte gibt, sind dabei immer enthalten.

Im HTTP Header "Location" befindet sich die Url des neu angelegten Partners.

Beispiel-Response:
``` json
201 CREATED
Location: https://api.europace2.de/v2/partner/ABC12
Content-Type: application/json;charset=utf-8

{
   "partnerId": "ABC12",
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

> **Hinweis**
> 
> Für eine erfolgreiche Angebotsannahme in BaufiSmart ist es für die nachfolgenden Prozesse erforderlich, daß für den Kundenbetreuer des Vorgangs die folgenden Attribute im Partnermanagement gepflegt sind:
> - anrede
> - vorname
> - nachname
> - anschrift
> - bankverbindung
> - email
> 
> Eine Angebotsannahme ist andernfalls nicht möglich.
