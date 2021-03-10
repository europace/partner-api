# Beispiel: Partnerkennzeichen auslesen

Die Partnerkennzeichen identifizieren einen Vertrieb bei einem Produktanbieter. 

Beispiel-Request:
```
GET /v2/partner/ABC12/partnerkennzeichen HTTP/1.1
Host: api.europace.de
Accept: application/json
Authorization: Bearer eyJraWQiOiJ...
X-TraceId: request-2020-08-28-07-59
Accept: application/json
```

Für den Zugriff auf einen Partner und dessen Partnerkennzeichen benötigt der Aufrufer grundsätzlich die Berechtigung, diesen zu sehen. Dieses Recht besteht, wenn der abgerufenene Partner in der Hierachie unter der authentifizierten Plakette liegt oder das Administrationsrecht an die authentifizierte Plakette vergeben ist.

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