# ACLens — Manuelle SharePoint Einrichtung

Diese Anleitung erklärt Schritt für Schritt wie du eine Azure AD App-Registrierung für ACLens manuell erstellst. Nutze diese Anleitung wenn die automatische Einrichtung per Gerätecode nicht funktioniert.

---

## Voraussetzungen

- Du benötigst die Rolle **Anwendungsadministrator** oder **Globaler Administrator** in deinem Microsoft 365 Mandanten
- Zugang zum [Azure-Portal](https://portal.azure.com)

---

## Schritt 1 — App-Registrierung erstellen

1. Öffne [portal.azure.com](https://portal.azure.com)
2. Suche oben nach **„App-Registrierungen"**
3. Klicke auf **+ Neue Registrierung**
4. Fülle folgende Felder aus:
   - **Name:** `ACLens`
   - **Unterstützte Kontotypen:** `Nur Konten in diesem Organisationsverzeichnis`
   - **Umleitungs-URI:** leer lassen
5. Klicke auf **Registrieren**

> Nach der Registrierung siehst du die **Anwendungs-ID (Client-ID)** und die **Verzeichnis-ID (Mandanten-ID)** — beide notieren, du brauchst sie in ACLens.

---

## Schritt 2 — API-Berechtigungen hinzufügen

1. Klicke in der App-Registrierung auf **API-Berechtigungen**
2. Klicke auf **+ Berechtigung hinzufügen**
3. Wähle **Microsoft Graph**
4. Wähle **Anwendungsberechtigungen** (nicht delegiert)
5. Suche und füge folgende Berechtigungen hinzu:

| Berechtigung | Beschreibung |
|---|---|
| `Sites.Read.All` | Elemente in allen Websitesammlungen lesen |
| `Sites.FullControl.All` | Berechtigungszuweisungen auf Websiteebene lesen |
| `User.Read.All` | Alle Benutzerprofile lesen (für Namensauflösung) |
| `GroupMember.Read.All` | Gruppenmitgliedschaften lesen |

6. Klicke auf **Berechtigungen hinzufügen**
7. Klicke auf **Administratorzustimmung erteilen für [Dein Mandant]** — mit **Ja** bestätigen

> Die Berechtigungen erhalten ein grünes Häkchen sobald die Zustimmung erteilt wurde.

---

## Schritt 3 — Geheimen Clientschlüssel erstellen

1. Klicke auf **Zertifikate & Geheimnisse**
2. Klicke auf **+ Neuer geheimer Clientschlüssel**
3. Gib eine Beschreibung ein (z.B. `ACLens Schlüssel`)
4. Ablauf festlegen — **24 Monate** empfohlen
5. Klicke auf **Hinzufügen**
6. **Kopiere den Wert sofort** — er wird nur einmal angezeigt

---

## Schritt 4 — Zugangsdaten in ACLens eingeben

1. ACLens öffnen
2. Auf den Tab **SharePoint** klicken
3. Auf **Einrichten / Verbinden** klicken
4. Den Tab **Manuelle Einrichtung** wählen
5. Folgende Daten eingeben:
   - **Mandanten-ID** — aus Schritt 1 (Verzeichnis-ID)
   - **Client-ID** — aus Schritt 1 (Anwendungs-ID)
   - **Geheimer Clientschlüssel** — aus Schritt 3
6. Auf **Speichern & verbinden** klicken

ACLens speichert die Zugangsdaten verschlüsselt auf deinem lokalen Computer in `sp_credentials.xml` neben dem Skript.

---

## Verbindung prüfen

Nach dem Speichern sollte der SharePoint-Tab folgendes anzeigen:

```
✔  Verbunden
Mandant: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  |  Client: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

---

## Benötigte Berechtigungen

| API | Berechtigung | Typ | Zweck |
|---|---|---|---|
| Microsoft Graph | `Sites.Read.All` | Anwendung | SharePoint-Websiteinhalte lesen |
| Microsoft Graph | `Sites.FullControl.All` | Anwendung | Berechtigungszuweisungen lesen |
| Microsoft Graph | `User.Read.All` | Anwendung | Anzeigenamen auflösen |
| Microsoft Graph | `GroupMember.Read.All` | Anwendung | Gruppenmitglieder lesen |

---

## Fehlerbehebung

| Problem | Lösung |
|---|---|
| „Unzureichende Rechte" | Sicherstellen dass Administratorzustimmung für alle Berechtigungen erteilt wurde (grüne Häkchen) |
| „Anwendung nicht gefunden" | Client-ID prüfen — muss exakt mit der App-Registrierung übereinstimmen |
| „Ungültiger geheimer Clientschlüssel" | Schlüssel ist möglicherweise abgelaufen — neuen erstellen |
| Website nicht gefunden | Die Website-URL exakt aus der Browser-Adressleiste von SharePoint kopieren |
| 403 Forbidden bei Ordnern | `Sites.FullControl.All` wurde möglicherweise nicht zugestimmt — Administratorzustimmung erneut erteilen |

---

## Sicherheitshinweise

- Der geheime Clientschlüssel wird **verschlüsselt** mit der Windows-Datenschutz-API (DPAPI) gespeichert — er ist an dein Windows-Benutzerkonto gebunden und kann auf einem anderen Gerät nicht entschlüsselt werden
- ACLens fordert nur **Leseberechtigungen** an — es können keine Berechtigungen oder Inhalte verändert werden
- Die Datei `sp_credentials.xml` darf nicht weitergegeben oder in die Versionskontrolle eingecheckt werden — sie ist in `.gitignore` eingetragen

---

*Zurück zur [README](../README.md)*
