# Modul 2.3 – Manage Certificate Templates and Certificates

Um das zu machen sollten Modul 2.2 funktionieren. 

[Designing and Implementing a PKI: Part IV Configuring SSL for Web Enrollment and Enabling Key Archival](https://learn.microsoft.com/en-us/archive/blogs/askds/designing-and-implementing-a-pki-part-iv-configuring-ssl-for-web-enrollment-and-enabling-key-archival)

[Designing and Implementing a PKI: Part III Certificate Templates](https://learn.microsoft.com/en-us/archive/blogs/askds/designing-and-implementing-a-pki-part-iii-certificate-templates)
***

## 1. Vorbereitung – MMC für Certificate Templates

1. Auf der CA (CA01) **mmc** starten.  
2. **File → Add/Remove Snap-in…**.  
3. **Certificate Templates** hinzufügen → OK.  
4. **Certification Authority** Snap-In für die Root CA hinzufügen.

Damit kannst du Templates editieren und später auf der CA veröffentlichen.

***

## 2. WebServer-Template für IIS-Webserver anpassen

0. Auf dem DC die Gruppe `Webserver` erstellen um diese später zu verwenden
1. Im **Certificate Templates** Snap-In das vorhandene Template **Web Server** duplizieren:  
   - Rechtsklick auf **Web Server** → **Duplicate Template**.  
2. Neues Template konfigurieren (Name: `Corp WebServer`):
   - **General**:
     - Template display name: `Corp WebServer`.
     - Publish certificate in Active Directory.
   - **Compatibility**:
     - CA: mindestens deine CA-Version. (Windows Server 2016)
     - Client: passender OS-Level (Windows 10/ Windows Server 2016).  
   - **Subject Name**:
     - „Build from this Active Directory information“ → **DNS name** aktivieren, damit der FQDN des Servers automatisch gesetzt wird.
     - Alle anderen optionen abwählen
   - **Extensions**:
     - Key usage: Digital Signature, Key Encipherment.  
     - Application Policies: Server Authentication (und ggf. Client Authentication, falls nötig).  
   - **Security**:
     - Gruppe für Webserver (`WebServer`) erhält **Enroll**.  

3. Template speichern.

***

## 3. User-Template anpassen

1. Im **Certificate Templates** Snap-In das vorhandene Template **User** duplizieren:  
   - Rechtsklick auf **Web Server** → **Duplicate Template**.  
2. Neues Template konfigurieren (Name: `Corp User`):
   - **General**:
     - Template display name: `Corp User`.
     - Publish certificate in Active Directory.
   - **Compatibility**:
     - CA: mindestens deine CA-Version. (Windows Server 2016)
     - Client: passender OS-Level (Windows 10/ Windows Server 2016).  
   - **Request Handling**:
     - Aktiviere **Archive subject's encryption private key**
   - **Security**:
     - Falls die Gruppe (`Domain user`) noch nicht in Security ist, dann diese hinzufügen und **Enroll** vergeben.  

***

## 4. Key Recovery Agent-Template anpassen

1. Am DC den User `Corp KRA` erstellen um in später zu verwenden.
2. Im **Certificate Templates** Snap-In das vorhandene Template **Key Recovery Agent** modifizieren:  
   - **General**:
     - Publish certificate in Active Directory.
   - **Compatibility**:
     - CA: mindestens deine CA-Version. (Windows Server 2016)
     - Client: passender OS-Level (Windows 10/ Windows Server 2016).  
   - **Security**:
     - Den User `Corp KRA` hinzufügen und ihm die Permission Enroll geben.  

***

## 5. Templates auf der CA veröffentlichen

1. Auf `CA01` **Certification Authority** öffnen.  
2. CA-Name aufklappen → Rechtsklick auf **Certificate Templates** → **New → Certificate Template to Issue**.  
3. Folgende Templates auswählen:
   - `Corp WebServer` (für SSL auf Webservern / Web Enrollment).
   - `Corp User` 
   - KRA-Template (Standard **Key Recovery Agent**) für Key Archival.   
4. Mit OK bestätigen.

Achte darauf, dass die Berechtigungen auf den Templates mit deinen Gruppen/Serverobjekten zusammenpassen, damit Enrollment funktioniert.

***

## 6. Web Enrollment auf der CA

1. Server-Manager auf CA01 öffnen → `Rollen` → `Rollen hinzufügen`.
2. Bei `Active Directory-Zertifikatdienste` auf den Pfeil links klicken.
3. Jetzt noch `Certification Authority Web Enrollment` anhaken.
4. Dann installieren.
5. Nach der Installation bei der Konfiguration des Web Services alles beim default lassen und installieren.

***

## 6. SSL-Zertifikat für Web Enrollment anfordern (auf CA01)

Auf `CA01` (IIS-Server):

1. Auch wie beim Web Server in Modul 2.2 Double Escaping und Directory Browsing aktivieren
2. `CA01` zu der Gruppe der `Webserver` hinzufügen (Sonst kann man nicht das `Corp Webserver` Zertifikat bekommen)
3. **mmc** starten → Snap-In **Certificates** für **Computer account** → **Local computer** hinzufügen.  
4. Unter **Certificates (Local Computer) → Personal** rechtsklicken → **Request New Certificate…**.  
5. Certificate Enrollment Wizard:
   - Template **Corp WebServer** auswählen.  
   - Request abschließen, warten bis das Zertifikat als **Issued** in `Personal\Certificates` erscheint.

Das ausgestellte Zertifikat sollte im Subject/SAN den FQDN wie `CA01.corp.example.com` enthalten.

***

## 7. SSL in IIS für Web Enrollment aktivieren

Auf `CA01`:

1. **IIS Manager** öffnen.  
2. Links: Servername aufklappen → **Sites** → **Default Web Site** markieren.  
3. Rechts im **Actions**-Bereich: **Bindings…**.  
4. **Add…**:
   - **Type**: `https`.  
   - IP: `All Unassigned`.  
   - Port: `443`.  
   - Host name: FQDN (`CA01.corp.example.com`).  
   - **SSL certificate**: das zuvor ausgestellte `Corp WebServer`-Zertifikat auswählen.  
5. Mit OK und Close bestätigen.

Jetzt ist die Web Enrollment Site per HTTPS über FQDN erreichbar, z.B. `https://CA01.corp.example.com/certsrv`. Den FQDN kannst du in der Domain-Policy ggf. in die geeignete Zone (Trusted Sites / Intranet) aufnehmen, damit integrierte Authentifizierung sauber funktioniert.

***

## 8. Key Recovery Agent (KRA) Zertifikat anfordern

Aus Sicht eines KRA-Benutzers (z.B. `Corp\corp-KRA`):

1. Als KRA-User an einem Client anmelden.  
2. Web Enrollment über HTTPS öffnen: `https://CA01.corp.example.com/certsrv`.  
3. **Request a certificate** → **Create and submit a request to this CA**.  
4. Aus dem Template-Dropdown **Key Recovery Agent** auswählen.  
5. Option **Mark keys as exportable** aktivieren (wichtig für spätere Sicherung).  
6. Optional Friendly Name setzen, z.B. `KRA Cert`.  
7. Request abschicken.

Der Request landet als **Pending Request** auf der CA, da das Standard-KRA-Template in der Regel „Manager approval“ verlangt.

***

## 9. KRA-Zertifikat als CA-Administrator ausstellen

Auf `CA01`:

1. **Certification Authority** öffnen.  
2. CA-Name → **Pending Requests**.  
3. Den Request des KRA-Users anhand von **Requestor Name** / Zeitstempel identifizieren.  
4. Rechtsklick → **All Tasks → Issue**.

Damit wird das KRA-Zertifikat ausgestellt und steht dem Benutzer zum Abholen bereit.

***

## 10. KRA-Zertifikat installieren und sicher exportieren

Als KRA-User:

1. Wieder `https://pki.corp.example.com/certsrv` aufrufen.  
2. **View the status of a pending certificate request** auswählen.  
3. Den eigenen Request anhand Datum/Uhrzeit auswählen.  
4. **Install this certificate** anklicken und die Sicherheitsabfrage bestätigen.

Zur Absicherung empfiehlt sich Export in eine geschützte PFX-Datei:

1. **certmgr.msc** (Certificates für Current User) öffnen.  
2. Unter **Personal → Certificates** das **KRA Cert** finden.  
3. Rechtsklick → **All Tasks → Export** → Certificate Export Wizard:
   - **Yes, export the private key**.  
   - Exportformat: PFX, Option „Delete the private key if the export is successful“ aktivieren, restliche Optionen deaktivieren.  
   - Starkes Passwort vergeben.  
   - Output-Datei speichern.  
4. Nach erfolgreichem Export liegt der Private Key nicht mehr im User-Store vor, nur in der PFX-Datei.

***

## 11. Key Archival auf der CA aktivieren

Jetzt wird das CA-Verhalten so konfiguriert, dass für bestimmte Templates die privaten Schlüssel auf der CA archiviert werden.

1. Auf `CA01` die **Certification Authority** öffnen.  
2. Rechtsklick auf den CA-Namen → **Properties**.  
3. Tab **Recovery Agents**:
   - Option **Archive the key** aktivieren.  
   - **Number of recovery agents to use** i.d.R. auf `1` belassen (oder mehr, falls mehrere KRAs genutzt werden sollen).  
   - **Add…** klicken und das zuvor ausgestellte KRA-Zertifikat auswählen.  
4. Mit OK bestätigen.  
5. Aufforderung zum Neustart von Certificate Services mit **Yes** bestätigen.
