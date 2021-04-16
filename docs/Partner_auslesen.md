# Beispiel: Stammdaten eines Benutzers auslesen

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
