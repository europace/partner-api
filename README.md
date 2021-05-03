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

- [Stammdaten eines Partners auslesen](https://github.com/europace/partner-api/blob/master/docs/Partner_auslesen.md)
- [neuen Benutzer anlegen](https://github.com/europace/partner-api/blob/master/docs/Partner_anlegen.md)
- [Zugang anlegen](https://github.com/europace/partner-api/blob/master/docs/Zugang.md)
- [Partnerkennzeichen auslesen](https://github.com/europace/partner-api/blob/master/docs/Partnerkennzeichen_auslesen.md)
- [Rechte eines Benutzer auslesen](https://github.com/europace/partner-api/blob/master/docs/Rechte_auslesen.md)
- [Partner aktualisieren](https://github.com/europace/partner-api/blob/master/docs/Partner_aktualisieren.md)
