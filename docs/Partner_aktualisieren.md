# Partner aktualisieren

Attribute eines Partners können mittels HTTP PATCH modifiziert werden.
Dabei werden **ausschließlich** diejenigen Attribute überschrieben, die im PATCH Request enthalten sind. Alle anderen Attribute werden nicht geändert.

Für Änderungen an einem Partner benötigt der Aufrufer Einstellungsrechte auf diesen. 

## Attribute die per PATCH geändert werden können

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

## Beispiel
Voraussetzungen:
* OAuth Token hat den Scope `partner:plakette:schreiben`
* Aufrufer hat Einstellungsrechte auf den Partner

Beispiel-Request:
```
PATCH /v2/partner/ABC12 HTTP/1.1
Host: api.europace.de
Authorization: Bearer eyJraWQiOiJWRDZZ...
Accept: application/json
X-TraceId: ff-request-2020-08-28
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

- leere Attribute bei Strings ("") löschen den bestehenden Wert.
- unbekannte Attribute werden ignoriert.
- "partnerId" ist nicht änderbar und wird deshalb ignoriert.
- "typ" ist nicht änderbar und wird deshalb ignoriert.

Der Body der Response enthält die aktuellen Stammdaten im JSON Format.
Dies kann zur Erfolgskontrolle genutzt werden. Attribute, die bereits gesetzt waren bzw. für die es Default Werte gibt, sind dabei immer enthalten.

Beispiel-Response:
```
200 OK
Content-Type: application/json;charset=utf-8

{
   "partnerId":"ABC12",
   "vorname":"Max",
   "nachname":"Mustermann",
   "typ":"PERSON",
   "email":"max.musterman@exmaple.org",
   "avatar":"https://ep2.mtp.rz-hypoport.local/partnermanagement/d71c58aeaed4022384b169f83e0d8842.avatar?anonymousAvatar=BENUTZER",
   "gesperrt":false,
   "kreditsachbearbeiter":true,
   "parent":{
      "partnerId":"ABC12"
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
