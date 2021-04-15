# Beispiel: Rechte eines Partners
Voraussetzungen für alle Anwendungsfälle und Beispiele:
* Scope `partner:plakette:lesen`
* für den Zugriff auf einen Partner und dessen Partnerkennzeichen benötigt der Aufrufer grundsätzlich die Berechtigung, diesen zu sehen. Dieses Recht besteht, wenn der abgerufenene Partner in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

## Personen-Rechte auslesen

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

## Übernahmerecht

Das Übernahmerecht berechtigt Partner auf alle Vorgänge eines anderen Partners lesend und schreiben zugreifen zu dürfen.


### Auf welche Partner habe ich Zugriff?

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

### Habe ich Zugriff auf Partner XYZ15?

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

## Administrationsrecht

Jeder Partner darf Änderungen an sich selbst oder anderen Partnern ausführen, wenn er das Administrationsrecht besitzt. Damit können Stammdaten oder Berechtigungen angepasst werden. Berechtigungen können nur vergeben werden, wenn der ausführende Partner diese selbst besitzt.

### Welche Partner kann ich verwalten?

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


