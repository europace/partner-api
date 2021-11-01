# Beispiel: Zugang anlegen und auslesen

Eine neu angelegte Partner:in braucht einen "Zugang" um sich an Europace mit ihrem Benutzernamen anmelden zu können. Der Zugang kann entweder über die Einstellungen (Partnermanagement) oder durch die Partner-API erstellt werden.

## Zugang anlegen

Voraussetzung für alle Anwendungsfälle:
* OAuth Token hat den Scope `partner:plakette:schreiben`
* für den Zugriff auf die Partner:in und ihre Partnerkennzeichen benötigt die Aufrufer:in grundsätzlich die Berechtigung, diese zu sehen. Dieses Recht besteht, wenn die abgerufenene Partner:in in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

### Anwendungsfall 1: Europace-Benutzer anlegen
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

### Anwendungsfall 2: Benutzer für eigenen Identity Provider anlegen
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

### Anwendungsfall 3: Benutzername ist Europace-weit nicht eindeutig oder keine E-Mail-Adresse 
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

## Benutzernamen für Identity Provider ändern
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

## Zugang auslesen
Um den aktuellen Zustand zu ermitteln, läßt sich dieser auslesen.

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
