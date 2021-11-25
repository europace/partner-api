# Partner API

Die Partner API ermöglicht eine Automatisierung der Benutzerverwaltung von Europace. Das Partnermanagment (Einstellungen) dient Europpace-Partnern zur Abbildung der eigenen Benutzer- und Rechte-Struktur. 

**Partner** können Benutzer mit ihren Stammdaten und Rechten sein als auch Organisationen wie Firmen, Abteilungen oder Teams. Jeder Partner wird im Hierarchiebaum als **Plakette** vom Typ **Person** oder **Organisation** angelegt.

---- 
![Vertrieb](https://img.shields.io/badge/-Vertrieb-lightblue)
![Produktanbieter](https://img.shields.io/badge/-Produktanbieter-lightblue)
![Baufinanzierung](https://img.shields.io/badge/-Baufinanzierung-lightblue)
![Privatkredit](https://img.shields.io/badge/-Privatkredit-lightblue)

[![Authentication](https://img.shields.io/badge/Auth-OAuth2-green)](https://docs.api.europace.de/baufinanzierung/authentifizierung/)
[![GitHub release](https://img.shields.io/github/v/release/europace/partner-api)](https://github.com/europace/partner-api/releases)

[![Pattern](https://img.shields.io/badge/Pattern-Tolerant%20Reader-yellowgreen)](https://martinfowler.com/bliki/TolerantReader.html)

## Dokumentation
[![YAML](https://img.shields.io/badge/OAS-HTML_Doc-lightblue)](https://europace.github.io/partner-api)
[![YAML](https://img.shields.io/badge/OAS-YAML-lightgrey)](https://github.com/europace/partner-api/blob/master/partner-openapi.yaml)

Feedback und Fragen sind als [GitHub Issue](https://github.com/europace/partner-api/issues/new) willkommen.

## Schnellstart
Damit du unsere APIs und deinen Anwendungsfall schnellstmöglich testen kannst, haben wir eine [Postman-Collection](https://docs.api.europace.de/baufinanzierung/schnellstart/) für dich zusammengestellt. 

### Authentifizierung
Bitte benutze [![Authentication](https://img.shields.io/badge/Auth-OAuth2-green)](https://docs.api.europace.de/baufinanzierung/authentifizierung/), um Zugang zur API bekommen. Um die API verwenden zu können, benötigt der OAuth2-Client folgende Scopes:

| Scope                                  | API-Usecase                                                      |
| -------------------------------------- | ---------------------------------------------------------------- |
| ` partner:plakette:anlegen `           |   Darf neue Plaketten anlegen.                                   |
| ` partner:plakette:lesen `             |   Darf Partner-Daten lesen                                       |
| ` partner:plakette:schreiben `         |   Darf Partner-Daten schreiben                                   |
| ` partner:beziehungen:lesen `          |   Darf Beziehungen zwischen Partnern lesen Erlaubt es unter anderem UebernahmeRecht, Administrierbare und Uebernehmbare abzurufen  |
| ` partner:rechte:lesen `               |   Darf Rechte eines Partners lesen                               |
| ` partner:rechte:schreiben `           |   Darf Rechte eines Partners schreiben                           |

## Anwendungsfälle der API

- [Kontaktdaten eines Partners auslesen](#Kontaktdaten-eines-Partners-auslesen)
- [Partners-Stammdaten auslesen](#Stammdaten-eines-Partners-auslesen)
- [Partner-Kennzeichen auslesen](#Partner-Kennzeichen-auslesen)
- [Partner-Zugang auslesen](#Zugang-auslesen)
- [Partner-Rechte auslesen](#Partner-Rechte-auslesen)
- [Partner anlegen](#Partner-anlegen)
- [Partner aktualisieren](#Partner-aktualisieren)
- [Partner-Zugang anlegen und ändern](#Zugang-anlegen-und-ändern)
- [Partner-Rechte ändern](#Partner-Rechte-ändern)

# Kontaktdaten eines Partners auslesen 

Die Kontaktdaten lassen sich für alle Handelspartner abrufen, um die Zusammenarbeit zu unterstützen.

Voraussetzungen:
* der Aufrufer hat eine Handelsbeziehung zu dem Partner
und/oder
* der Aufrufer hat ein Zugriffsrecht auf den Partner
und/oder
* der Aufrufer hat ein Einstellungsrecht auf den Partner
und/oder
* der Aufrufer liegt in der Hierachie über dem Partner

Beispiel-Request: 
``` curl
curl --location --request GET 'https://api.europace.de/v2/partner/ABC12/kontaktdaten' \
--header 'Content-Type: application/json' \
--header 'X-TraceId: {{meineTraceId}}' \
--header 'Authorization: Bearer {{access_token}}'
```

Beispiel-Response Person: 
```json
{
    "person": {
        "anrede": "FRAU",
        "vorname": "Maxi",
        "nachname": "Musterfrau",
        "titelFunktion": "Baufi-Beraterin",
        "geburtsdatum": "1995-05-05"
    },
    "firmenName": "Immofin AG",
    "telefon": "030-12345-0",
    "mobiltelefon": "0151-12345678",
    "email": "maxi.musterfrau@immofin.de",
    "anschrift": {
        "strasse": "Teststr.",
        "hausnummer": "69",
        "plz": "10557",
        "ort": "Berlin"
    },
    "Paragraph34c": {
        "registrierungsNummer": "D-W-7943497238942",
        "aufsichtsBehörde": "IHK Berlin, Fasanenstr. 85, 10623 Berlin"
    }
}
```

Beispiel-Response Organisation: 
```json
{
    "organisation": {
        "organisationsName": "Immofin"
    },
    "firmenName": "Immofin AG",
    "telefon": "030-12345-0",
    "fax": "030-12345-678",
    "email": "maxi.musterfrau@immofin.de",
    "website": "http://www.immofin.de",
    "anschrift": {
        "strasse": "Teststr.",
        "hausnummer": "69",
        "plz": "10557",
        "ort": "Berlin"
    },
    "Paragraph34c": {
        "registrierungsNummer": "D-W-7943497238942",
        "aufsichtsBehörde": "IHK Berlin, Fasanenstr. 85, 10623 Berlin"
    }
}
```

## Stammdaten eines Partners auslesen

Voraussetzungen:
* OAuth Token hat den Scope `partner:plakette:lesen `
* Für den Zugriff auf einen Partner benötigt der Aufrufer grundsätzlich die Berechtigung, diesen zu sehen. Dieses Recht besteht, wenn der abgerufenene Partner in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

Beispiel-Request: 
``` curl
curl --location --request GET 'https://api.europace.de/v2/partner/ABC12/' \
--header 'Content-Type: application/json' \
--header 'X-TraceId: {{meineTraceId}}' \
--header 'Authorization: Bearer {{access_token}}'
```

Die aus den Einstellungen bekannte Vererbung von Werten bestimmter Attribute entlang der Hierarchie wird in der API nicht reflektiert. **Geerbte Werte werden also nicht ausgeliefert.**

Beispiel-Response: 
```json
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

## Partner-Kennzeichen auslesen

Die Partnerkennzeichen identifizieren einen Vertrieb bei einem Produktanbieter. 

Vorausetzungen:
* OAuth Token hat den Scope `partner:plakette:lesen `
* für den Zugriff auf einen Partner und dessen Partnerkennzeichen benötigt der Aufrufer grundsätzlich die Berechtigung, diesen zu sehen. Dieses Recht besteht, wenn der abgerufenene Partner in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

Beispiel-Request:
```
GET /v2/partner/ABC12/partnerkennzeichen HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJ...
X-TraceId: request-2020-08-28-07-59
Accept: application/json
```

Die aus den Einstellungen bekannte Vererbung von Werten bestimmter Attribute entlang der Hierarchie wird in der API nicht reflektiert. **Geerbte Werte werden also nicht ausgeliefert.**

Beispiel-Response:
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

## Zugang auslesen
Um den aktuellen Zugang zu ermitteln, läßt sich dieser auslesen.

Voraussetzungen:
* OAuth Token hat den Scope `partner:plakette:lesen`
* für den Zugriff auf einen Partner und dessen Partnerkennzeichen benötigt der Aufrufer grundsätzlich die Berechtigung, diesen zu sehen. Dieses Recht besteht, wenn der abgerufenene Partner in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

Beispiel-Request:
```
GET /v2/partner/ABC12/zugang HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json
```

Beispiel-Response, für einen Partner, der sich am Europace-Identity-Provider authentifizert (Europace-Passwort):
```
200 OK
Content-Type: application/json;charset=utf-8

{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "benutzername": "maxi.musterman@example.org",
}
```

Beispiel-Response, für einen Partner, der sich mit einem eigenen Identity-Provider authentifizert (z.B. Actice Directory):
```
200 OK
Content-Type: application/json;charset=utf-8

{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "identityProviderBenutzername": "maxi.muster",
    "identityProviderConfigURL": "https://auth.deineOrganisation.de/adfs/.well-known/openid-configuration"
}
```

## Partner-Rechte auslesen
Voraussetzungen für alle Anwendungsfälle und Beispiele:
* OAuth Token hat den Scope `partner:plakette:lesen`
* für den Zugriff auf einen Partner und dessen Partnerkennzeichen benötigt der Aufrufer grundsätzlich die Berechtigung, diesen zu sehen. Dieses Recht besteht, wenn der abgerufenene Partner in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

### Personen-Rechte auslesen

Beispiel-Request:
``` http
GET /v2/partner/ABC12/rechte HTTP/1.1
Host: api.europace.de
Authorization: Bearer eyJraWQ...
```

Beispiel-Response:
```json
{
    "partnermanagement": {
        "apiClientEinstellungenVornehmen": true,
        "einstellungenOeffnen": true,
        "baufiSmartEinstellungenVornehmen": true,
        "partnerAnlegen": true
    },
    "baufismart": {
        "baufiSmartNutzen": true,
        "echtgeschaeft": true,
        "vorgaengeUeberOberflaecheAnlegen": true,
        "ergebnisListeNutzen": true,
        "loeschen": false
    },
    "kreditsmart": {
        "echtgeschaeft": true,
        "kreditSmartSichtbar": true,
        "versicherungAnbieten": true,
        "vorgaengeUeberOberflaecheAnlegen": true
    }
}
```

### Übernahmerecht

Das Übernahmerecht berechtigt Partner auf alle Vorgänge eines anderen Partners lesend und schreiben zugreifen zu dürfen.


#### Auf welche Partner habe ich Zugriff?

Beispiel-Request:
```
GET /v2/partner/ABC12/uebernehmbare HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJ
Content-Type: application/json
X-TraceId: ff-request-2020-08-28-07-59
```

Beispiel-Response:
```
200 OK
Content-Type: application/json;charset=utf-8

{ 
  "content": [ 
    { 
      "partnerId":"XYZ15" 
    } 
  ]
}
```

#### Habe ich Zugriff auf Partner XYZ15?

Beispiel-Request:
```
GET /v2/partner/ABC12/uebernahmeRechtFuer/XYZ15 HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOi...
Content-Type: application/json
X-TraceId: ff-request-2020-08-28-07-59
```

Beispiel-Response:
```
200 OK
Content-Type: application/json;charset=utf-8

{
  "partner": {
    "partnerId": "XYZ15",
    "gesperrt": false
  },
  "uebernehmbar": true
}
```



### Administrationsrecht

Jeder Partner darf Änderungen an sich selbst oder anderen Partnern ausführen, wenn er das Administrationsrecht besitzt. Damit können Stammdaten oder Berechtigungen angepasst werden. Berechtigungen können nur vergeben werden, wenn der ausführende Partner diese selbst besitzt.

#### Welche Partner kann ich verwalten?

Beispiel-Request:
```
GET /v2/partner/ABC12/administrierbare HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json
```

Beispiel-Response:
```
200 OK
Content-Type: application/json;charset=utf-8

{ 
  "content": [ 
    { 
      "partnerId":"XYZ15" 
    } 
  ]
}
```

## Partner anlegen

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
``` json 
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

## Partner aktualisieren

Attribute eines Partners können mittels HTTP PATCH modifiziert werden.
Dabei werden **ausschließlich** diejenigen Attribute überschrieben, die im PATCH Request enthalten sind. Alle anderen Attribute werden nicht geändert.

Für Änderungen an einem Partner benötigt der Aufrufer Einstellungsrechte auf diesen. 

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

### Beispiel
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
- wenn ein Feld übergeben wird, dass ein ENUM erwartet, muss ein Wert angeben werden (Anrede)

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

## Zugang anlegen und ändern

Eine neu angelegte Partner:in braucht einen "Zugang" um sich an Europace mit ihrem Benutzernamen anmelden zu können. Der Zugang kann entweder über die Einstellungen (Partnermanagement) oder durch die Partner-API erstellt werden.

### Zugang anlegen

Voraussetzung für alle Anwendungsfälle:
* OAuth Token hat den Scope `partner:plakette:schreiben`
* für den Zugriff auf die Partner:in und ihre Partnerkennzeichen benötigt die Aufrufer:in grundsätzlich die Berechtigung, diese zu sehen. Dieses Recht besteht, wenn die abgerufenene Partner:in in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

#### Anwendungsfall 1: Europace-Benutzer anlegen
Der Benutzer:in mit der PartnerId:ABC12 wird ein Zugang mit dem Benutzernamen "max.musterman@exmaple.org" eingerichtet und eine Aktivierungs-E-Mail (`sendEmail=true`) an ihren Benutzernamen gesendet. In der Aktivierungs-E-Mail wird die Benutzer:in zur Festlegung ihres Passwortes auffordert.

>Hinweis: \
>Als Antwort-Adresse in der Aktivierungs-E-Mail wird die E-Mail oder der Benutzername des Subject im OAuth-Token verwendet, die den Zugang angelegt hat.
>Ist keine E-Mail-Adresse oder Benutzername vorhanden geht die Antwort an noreply@europace2.de.

Voraussetzung für Anwendungsfall 1:
* der Benutzername ist Europace-weit eindeutig
* der Benutzername ist eine E-Mail-Adresse

Beispiel-Request: 
```
POST /v2/partner/ABC12/zugang?sendEmail=true HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json

{
    "benutzername" : "maxi.musterman@example.org"
}
```

Beispiel-Response:
```
HTTP-Code: 201 created
{
    "partnerId": "ABC12",
    "status": "ZUGANG_UNBESTAETIGT",
    "benutzername": "maxi.musterman@example.org",
}
```

#### Anwendungsfall 2: Benutzer für eigenen Identity Provider anlegen
Dieser Anwendungsfall ist vor allem bei Bankpartnern oder Direktvertrieben üblich, bei denen alle Mitarbeiter eine firmeneigene E-Mail mit eigener Domäne haben und die Mitarbeiter ausschließlich innerhalb dieser Organisation arbeiten.

In diesem Anwendungsfall wird der Identity Provider des Partners konfiguriert und der Benutzername als Benutzers-Identifikation bei Europace und dem Identity Provider des Partners verwendet.

Da es sich um einen bestehende Benutzer:in im Identity Provider des Partners handelt, darf keine Aktivierungs-E-Mail versendet werden (`sendEmail=false`).

Voraussetzung für Anwendungsfall 2:
* der Benutzername ist Europace-weit eindeutig
* der Benutzername ist eine E-Mail-Adresse

Beispiel-Request: 
```
POST /v2/partner/ABC12/zugang?sendEmail=false HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json

{
    "benutzername" : "maxi.musterman@deineOrganisation.de"
}
```

Beispiel-Response:
```
HTTP-Code: 201 created
{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "benutzername": "maxi.musterman@deineOrganisation.de",
    "identityProviderConfigURL": "https://idp.deineOrganisation.de/auth/realms/.well-known/openid-configuration"
}
```

#### Anwendungsfall 3: Benutzername ist Europace-weit nicht eindeutig oder keine E-Mail-Adresse 
Es soll eine Benutzer:in für den eigenen Identity Provider angelegt werden, aber der Benutzername ist Europace-weit nicht eindeutig oder keine E-Mail-Adresse. In diesem Fall wird das Feld `benutzername` gar nicht verwendet, sondern ausschließlich das Feld `identityProviderBenutzername`. Die Identifikation der Benutzer:in auf der Anmelde-Maske kann nur noch über die Partner-Id erfolgen. Die Partner-Id kann in manchen Anwendungsfällen mit dem Parameter `login_hint` an die Anmelde-Maske übergeben werden, um die Benutzererfahrung zu verbessern.

Da es sich um eine bestehende Benutzer:in im Identity Provider des Partners handelt und der identityProviderBenutzername nur dort verwendet wird, wird keine Aktivierungs-E-Mail versendet, unabhängig vom Parameter `sendEmail`.

Voraussetzung für Anwendungsfall 3:
* keine weiteren

Beispiel-Request: 
```
POST /v2/partner/ABC12/zugang HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json

{
    "identityProviderBenutzername" : "maxi.mustermann01"
}
```

Beispiel-Response:
```
HTTP-Code: 201 created
{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "identityProviderBenutzername": "maxi.mustermann01",
    "identityProviderConfigURL": "https://idp.deineOrganisation.de/auth/realms/.well-known/openid-configuration"
}
```

### Benutzernamen für Identity Provider ändern
Die Benutzernamen von externen Identity Providern können über die Partner-API geändert werden.

Einschränkung:
* das Feld `benutzername` kann nicht angepasst werden

Beispiel-Request: 
```
PATCH /v2/partner/ABC12/zugang HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json

{
    "identityProviderBenutzername" : "maxi.muster"
}
```

Beispiel-Response:
```
HTTP-Code: 200 okay
{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "identityProviderBenutzername": "maxi.muster",
    "identityProviderConfigURL": "https://idp.deineOrganisation.de/auth/realms/.well-known/openid-configuration"
}
```

## Partner-Rechte ändern

### Personen-Rechte ändern

Voraussetzungen:
* OAuth Token hat den Scope `partner:rechte:schreiben`
* Aufrufer hat ein Administrationsrecht auf den Partner

Beispiel-Request:
``` http
POST /v2/partner/ABC12/rechte HTTP/1.1
Host: api.europace.de
Authorization: Bearer eyJraWQ...
```

Beispiel-Response:
```json
{
    "partnermanagement": {
        "apiClientEinstellungenVornehmen": true,
        "einstellungenOeffnen": true,
        "baufiSmartEinstellungenVornehmen": true,
        "partnerAnlegen": true
    },
    "baufismart": {
        "baufiSmartNutzen": true,
        "echtgeschaeft": true,
        "vorgaengeUeberOberflaecheAnlegen": true,
        "ergebnisListeNutzen": true,
        "loeschen": false
    },
    "kreditsmart": {
        "echtgeschaeft": true,
        "kreditSmartSichtbar": true,
        "versicherungAnbieten": true,
        "vorgaengeUeberOberflaecheAnlegen": true
    }
}
```

### Übernahmerecht vergeben

In dem Beispiel wird dem Partner ABC12 das Übernahmerecht auf XYZ56 gegeben. ABC12 kann danach auf die Vorgänge von XYZ56 zugreifen.

Voraussetzungen:
* OAuth Token hat den Scope `partner:beziehungen:schreiben `
* Aufrufer hat Einstellungsrechte auf den Partner, dem das Übernahmerecht hinzugefügt wird 
* Aufrufer hat Einstellungsrechte auf den Partner, auf den der Zugriff gewährt wird

Beispiel-Request:
```
POST /v2/partner/ABC12/uebernahmeRechtFuer/XYZ56 HTTP/1.1
Host: api.europace.de
X-Trace-Id: My-COLLECTION-8301
Authorization: Bearer eyJraWQiOiJFT05...
```

Beispiel-Response:
```
201 Created
```