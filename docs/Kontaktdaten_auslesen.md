# Beispiel: Kontaktdaten eines Partners auslesen

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
