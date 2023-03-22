# Partner API v2.0

**Attention: You are not viewing the latest version. You can find the latest version [here](https://docs.api.europace.de/common/partner-settings/partner-api/).**

The Partner API enables automation of Europace's user management. The partner management (settings) is used by Europace partners to map their own user and rights structure. 

Partners can be users as well as organizations like companies, departments or teams. Each partner is created in the hierarchy tree as [Plakette](https://docs.api.europace.de/common/glossary/) of type [Person](https://docs.api.europace.de/common/glossary/) or [Organisation](https://docs.api.europace.de/common/glossary/).

---- 
![advisor](https://img.shields.io/badge/-advisor-lightblue)
![loanProvider](https://img.shields.io/badge/-loanProvider-lightblue)
![mortgageLoan](https://img.shields.io/badge/-mortgageLoan-lightblue)
![consumerLoan](https://img.shields.io/badge/-consumerLoan-lightblue)

[![Authentication](https://img.shields.io/badge/Auth-OAuth2-green)](https://docs.api.europace.de/baufinanzierung/authentifizierung/)
[![GitHub release](https://img.shields.io/github/v/release/europace/partner-api)](https://github.com/europace/partner-api/releases/tag/v2.0)

[![Pattern](https://img.shields.io/badge/Pattern-Tolerant%20Reader-yellowgreen)](https://martinfowler.com/bliki/TolerantReader.html)

## Dokumentation
[![YAML](https://img.shields.io/badge/OAS-HTML_Doc-lightblue)](https://europace.github.io/partner-api)
[![YAML](https://img.shields.io/badge/OAS-YAML-lightgrey)](https://github.com/europace/partner-api/blob/master/partner-openapi_2_0.yaml)

For translation of our german domain-specific-language the [glossary](https://docs.api.europace.de/common/glossary/) will support you.

Feedback and questions are welcome as [GitHub Issue](https://github.com/europace/partner-api/issues/new).

## Quick Start
To help you test our APIs and your use case as quickly as possible, we've put together a [Postman Collection](https://docs.api.europace.de/baufinanzierung/quickstart/) for you. 

### Authentication
Please use [![Authentication](https://img.shields.io/badge/Auth-OAuth2-green)](https://docs.api.europace.de/baufinanzierung/authentifizierung/) to get access to the API. The OAuth2 client requires the following scopes:

| Scope                                  | API-Usecase                                                      |
| -------------------------------------- | ---------------------------------------------------------------- |
| ` partner:plakette:anlegen `           |   Allows to create new [Plaketten](https://docs.api.europace.de/common/glossary/)|
| ` partner:plakette:lesen `             |   Allows to read partner data                                    |
| ` partner:plakette:schreiben `         |   Allows to write partner data                                   |
| ` partner:beziehungen:lesen `          |   Allows to read relations between partners. Allows to retrieve UebernahmeRecht, Administrierbare and Uebernehmbare  |
| ` partner:rechte:lesen `               |   Allows to read partner rights                                  |
| ` partner:rechte:schreiben `           |   Allows to write partner rights                                 |

## Use cases of the API

### get information
- [Get contact details of a partner](#get-contact-details-of-a-partner)
- [Get partner data](#get-partner-data)
- [Get partner-code](#get-partner-code)
- [Get user-access](#get-user-access)
- [Get partner-permissions](#get-partner-permissions)
### manage partner
- [Create partner](#create-partner)
- [Update partner data](#update-partner-data)
- [Create or update user-access](#create-or-update-user-access)
- [Update partner-permissions](#update-partner-permissions)


## Get contact details of a partner 

Contact details can be retrieved for all trading partners to support collaboration.

Requirements:
* the caller has a trading relationship with the partner
and/or
* the caller has an [Zugriffsrecht](https://docs.api.europace.de/common/glossary/) to the partner
and/or
* the caller has a [Einstellungsrecht](https://docs.api.europace.de/common/glossary/) to the partner
and/or
* the caller is above the partner in the hierarchy

Example request: 
``` curl
curl --location --request GET 'https://api.europace.de/v2/partner/ABC12/kontaktdaten' \
--header 'Content-Type: application/json' \
--header 'X-TraceId: {{meineTraceId}}' \
--header 'Authorization: Bearer {{access_token}}'
```

Example response Person: 
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

Example response Organisation: 
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

## Get partner data

Requirements:
* OAuth token has scope `partner:plakette:lesen`.
* To access a partner, the caller basically needs permission to see it. This right exists if the retrieved partner is below the authenticated partner in the hierarchy or the Einstellungsrecht is assigned to the authenticated partner.

Example request: 
``` curl
curl --location --request GET 'https://api.europace.de/v2/partner/ABC12/' \
--header 'Content-Type: application/json' \
--header 'X-TraceId: {{meineTraceId}}' \
--header 'Authorization: Bearer {{access_token}}'
```

The inheritance of values of certain attributes along the hierarchy, which is known from the settings, is not reflected in the API. **Inherited values are therefore not delivered.**

Example response: 
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

## Get partner-code

[Partnerkennzeichen](https://docs.api.europace.de/common/glossary/) identify a [Vertriebsorganisation](https://docs.api.europace.de/common/glossary/) on the [Produktanbieter](https://docs.api.europace.de/common/glossary/)-side. 

Requirements:
* OAuth token has scope `partner:plakette:lesen `
* To access a partner, the caller basically needs permission to see it. This right exists if the retrieved partner is below the authenticated partner in the hierarchy or the Einstellungsrecht is assigned to the authenticated partner.

Example request:
```http
GET /v2/partner/ABC12/partnerkennzeichen HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJ...
X-TraceId: request-2020-08-28-07-59
Accept: application/json
```

**Inherited values are therefore not delivered.** The inheritance of values of certain attributes along the hierarchy, which is known from the settings, is not reflected in the API. 

Example response:
```json
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
  "rundvBlzVertriebsbank": "RV_BLZ",
  "vertriebsOrganisation": {
    "name": "Musterbank",
    "firma": "Musterbank AG",
    "vertriebsOrganisationsId": "MUSTERBANK_AG",
    "partnerId": "TYL29"
  }
}
```

## Get user-access
To determine the current Zugang, this can be read out.

Requirements:
* OAuth token has scope `partner:plakette:lesen `
* To access a partner, the caller basically needs permission to see it. This right exists if the retrieved partner is below the authenticated partner in the hierarchy or the Einstellungsrecht is assigned to the authenticated partner.

Example request:
```http
GET /v2/partner/ABC12/zugang HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json
```

Example response, for a partner authenticating to the Europace identity provider (Europace password):
```json
200 OK
Content-Type: application/json;charset=utf-8

{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "benutzername": "maxi.musterman@example.org",
}
```

Example response, for a partner authenticating with its own identity provider (e.g. Actice Directory):
```json
200 OK
Content-Type: application/json;charset=utf-8

{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "identityProviderBenutzername": "maxi.muster",
    "identityProviderConfigURL": "https://auth.deineOrganisation.de/adfs/.well-known/openid-configuration"
}
```

## Get partner-permissions
 
Requirements for all use cases and examples:
* OAuth token has scope `partner:rechte:lesen `
* To access a partner, the caller basically needs permission to see it. This right exists if the retrieved partner is below the authenticated partner in the hierarchy or the Einstellungsrecht is assigned to the authenticated partner.

### Get user-permissions

Example request:
``` http
GET /v2/partner/ABC12/rechte HTTP/1.1
Host: api.europace.de
Authorization: Bearer eyJraWQ...
```

Example response:
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

### Get access right

The [Zugriffrecht](https://docs.api.europace.de/common/glossary/) entitles partners to read and write access to all [Vorgänge](https://docs.api.europace.de/common/glossary/) of another partner.

#### Which partners do I have access to?

Example request:
```http
GET /v2/partner/ABC12/uebernehmbare HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJ
Content-Type: application/json
X-TraceId: ff-request-2020-08-28-07-59
```

Example response:
```json
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

#### Do I have access to Partner XYZ15?

Example request:
```http
GET /v2/partner/ABC12/uebernahmeRechtFuer/XYZ15 HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOi...
Content-Type: application/json
X-TraceId: ff-request-2020-08-28-07-59
```

Example response:
```json
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

### Get setting right

Returns all partners for which this partner is allowed to change the data and authorizations or retrieve the reporting.

At least the partner itself is returned, since everyone may at least set itself or retrieve its own reporting.

In order to achieve a better performance, the implicitly administrable partners have been omitted in the partner-api. To determine these, it is necessary to iterate over the subordinates of the result list.

Requirements:
* OAuth token has scope `partner:plakette:lesen`.
* Each partner may make changes to himself or other partners if he has the [Einstellungsrecht](https://docs.api.europace.de/common/glossary/). This means that partner data or authorizations can be adjusted. Authorizations can only be assigned if the executing partner has them himself.

#### Which partners can I manage?

Example request:
```http
GET /v2/partner/ABC12/administrierbare HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json
```

Example response:
```json
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

## Create partner

Creating a new partner is always done below an existing partner:
`https://api.europace.de/v2/partner/{PartnerId}/untergeordnete`

Requirements:
* OAuth token has scope `partner:plakette:anlegen`.
* Caller is a partner of type Person
* Caller has the `may create organizational units` permission. 
* Caller has [Einstellungsrechte](https://docs.api.europace.de/common/glossary/) on the partner under which the new partner is to be created

Example request:
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

The following rules apply to server-side evaluation:

- unknown attributes are ignored.
- For organizations, person-specific attributes are ignored.
- For persons, organization-specific attributes are ignored.
- Empty attributes for strings ("") are ignored.
- "partnerId" cannot be set and will be ignored.
- Rights are set to false for persons if not specified.

The body of the response contains the current master data in JSON format.
This can be used for success control. Attributes that are set on the server side or for which there are default values are always included.

The HTTP header "Location" contains the url of the newly created partner.

Example response:
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

> **Note**
> 
> For a successful offer acceptance in BaufiSmart it is necessary for the following processes that the following attributes are maintained in the partner management for the account manager of the transaction:
> - anrede
> - vorname
> - nachname
> - anschrift
> - bankverbindung
> - email

## Update partner data

Attributes of a partner can be modified using HTTP PATCH.
This overwrites **only** those attributes that are included in the PATCH request. All other attributes are not changed.

To make changes to a partner, the caller needs Einstellungsrechte. 

### Attributes that can be changed via PATCH

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

### Example
Requirements:
* OAuth token has the scope `partner:plakette:schreiben`.
* Caller has Einstellungsrechte on the partner

Example request:
``` json
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

The following rules apply to server-side evaluation:

- empty attributes for strings ("") delete the existing value.
- unknown attributes are ignored.
- `partnerId` cannot be changed and will be ignored.
- `type` is not changeable and will be ignored.
- if a field expects an ENUM, a value must be specified (Anrede)

The body of the response contains the current partner data in JSON format.
This can be used for success control. Attributes that were already set or for which there are default values are always included.

Example response:
```json
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

## Create or update user-access

A newly created partner needs a [Zugang](https://docs.api.europace.de/common/glossary/) to be able to log in to Europace with username. The Zugang can be created by the settings-frontend (partner management) or the partner-API.

### Create user-access

Requirements for all use cases and examples:
* OAuth token has scope `partner:plakette:schreiben`
* To access a partner, the caller basically needs permission to see it. This right exists if the retrieved partner is below the authenticated partner in the hierarchy or the Einstellungsrecht is assigned to the authenticated partner.

#### Use case 1: Create Europace user.
The user with the PartnerId:ABC12 is set up an Zugang with the username "max.musterman@example.org" and an activation email (`sendEmail=true`) is sent to the username. In the activation email, the user is prompted to set a password.

>Note: \
>The reply address in the activation email is the email or username of the subject in the access-token that created the Zugang.
>If no email address or username is available, the reply goes to noreply@europace2.de.

Requirement for use case 1:
* the username is Europace-wide unique
* the username is an e-mail address

Example request: 
```http
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

Example response:
```json
HTTP-Code: 201 created
{
    "partnerId": "ABC12",
    "status": "ZUGANG_UNBESTAETIGT",
    "benutzername": "maxi.musterman@example.org",
}
```

#### Use case 2: Create user for own identity provider.
This use case is most common with banking partners or direct sales organizations where all employees have a company email with their own domain and the employees work exclusively within this organization.

In this use case, the partner's identity provider is configured and the username is used as the user identifier at Europace and the partner's identity provider.

Don't send an activation email (`sendEmail=false`), because the user already exists in the partner's Identity Provider.

Requirement for use case 2:
* the username is Europace-wide unique
* the username is an e-mail address

Example request: 
```http
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

Example response:
```json
HTTP-Code: 201 created
{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "benutzername": "maxi.musterman@deineOrganisation.de",
    "identityProviderConfigURL": "https://idp.deineOrganisation.de/auth/realms/.well-known/openid-configuration"
}
```

#### Use case 3: Username is not unique Europace-wide or no email address 
A user:in is to be created for the own Identity Provider, but the username is Europace-wide not unique or no e-mail address. In this case the field 'benutzername' is not used at all, but only the field 'identityProviderBenutzername'. The identification of the user on the login mask can only be done via a `partnerId`. The `partnerId` can be passed to the login mask with the `username` parameter in some use cases to improve the user experience.

Since it is an existing user in the identity provider of the partner and the identityProvider username is only used here, no activation email is sent, regardless of the `sendEmail` parameter.

Requirements for use case 3:
* No further

Example request: 
```http
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

Example response:
```json
HTTP-Code: 201 created
{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "identityProviderBenutzername": "maxi.mustermann01",
    "identityProviderConfigURL": "https://idp.deineOrganisation.de/auth/realms/.well-known/openid-configuration"
}
```

### Update user-access for identity providers
The [Benutzernamen](https://docs.api.europace.de/common/glossary/) of external identity providers can be changed with Partner API.

Restriction:
* the field `benutzername` cannot be changed

Example request: 
```http
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

Example response:
```json
HTTP-Code: 200 okay
{
    "partnerId": "ABC12",
    "status": "ZUGANG_REGISTRIERT",
    "identityProviderBenutzername": "maxi.muster",
    "identityProviderConfigURL": "https://idp.deineOrganisation.de/auth/realms/.well-known/openid-configuration"
}
```

## Update partner-permissions

### Set user-permissions

Requirements:
* OAuth token has the scope `partner:rechte:schreiben`.
* Caller has a [Einstellungsrecht](https://docs.api.europace.de/common/glossary/) on the partner

Example request:
``` http
POST /v2/partner/ABC12/rechte HTTP/1.1
Host: api.europace.de
Authorization: Bearer eyJraWQ...

{
    "baufismart": {
        "baufiSmartNutzen": true,
        "echtgeschaeft": true
    }
}
```

Example response:
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

### Add access right

In the example, partner ABC12 is getting the [Zugriffsrecht](https://docs.api.europace.de/common/glossary/) from XYZ56. ABC12 can then access the [Vorgänge](https://docs.api.europace.de/common/glossary/) of XYZ56.

Requirements:
* OAuth token has scope `partner:beziehung:schreiben`.
* Caller has Einstellungsrecht on the partner to which the Zugriffsrecht is added 
* Caller has Einstellungsrecht on the partner to which the access is granted

Example request:
```http
POST /v2/partner/ABC12/uebernahmeRechtFuer/XYZ56 HTTP/1.1
Host: api.europace.de
X-Trace-Id: My-COLLECTION-8301
Authorization: Bearer eyJraWQiOiJFT05...
```

Example response:
```http
201 Created
```
