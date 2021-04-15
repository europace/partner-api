# Beispiel: Zugang anlegen und auslesen

Ein neu angelegter Partner braucht einen "Zugang" um sich an der Plattform mit seinem Benutzernamen anmelden zu können. Der Zugang kann entweder über die grafische Oberfläche werden oder durch eines POST Requests hergestellt werden.

HINWEIS: das Anlegen eines Zugangs per POST Request macht in der aktuellen Ausbaustufe nur Sinn, wenn der Partner einen externen Identity Provider (z.B. Active Directory) nutzt. Denn ein Partner, dessen Zugang per POST Request angelegt wird, hat bei Europace kein eigenes Passwort (sein Passwort ist im externen Identity Provider hinterlegt).

Das Url-Template für das Anlegen eines Zugangs für den Partner mit {PartnerId} lautet:

https://api.europace.de/v2/partner/{PartnerId}/zugang

## Zugang anlegen

Dem Benutzer mit der PartnerId:ABC12 wird ein Zugang mit dem Benutzernamen "max.musterman@exmaple.org" eingerichtet und eine Aktivierungs-E-Mail (sendEmail=true) an seinen Benutzernamen gesendet, die ihn zur Festlegung seines Passwortes auffordert.

Voraussetzung:
* Scope `partner:plakette:schreiben`
* für den Zugriff auf einen Partner und dessen Partnerkennzeichen benötigt der Aufrufer grundsätzlich die Berechtigung, diesen zu sehen. Dieses Recht besteht, wenn der abgerufenene Partner in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

Beispiel-Request: 
```
POST /v2/partner/ABC12/zugang?sendEmail=true HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJWRDZZTk...
X-TraceId: ff-request-2020-08-28-07-59
Content-Type: application/json

{
    "benutzername" : "max.musterman@exmaple.org"
}
```

Beispiel-Response:
```
HTTP-Code: 201 created
```
Der Body des Responses bleibt leer.


## Zugang auslesen
Voraussetzungen:
* Scope `partner:plakette:lesen`
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
    "benutzername": "max.mustermann@email.de"
}
```

Beispiel-Response, für einen Partner, der sich mit einem eigenen Identity-Provider authentifizert (z.B. Actice Directory):
```
200 OK
Content-Type: application/json;charset=utf-8

{
    "partnerId": "SCR06",
    "benutzername": "max.mustermann@email.de",
    "status": "ZUGANG_REGISTRIERT",
    "identityProviderConfigURL": "https://auth.meineDomain.de/adfs/.well-known/openid-configuration"
}
```
