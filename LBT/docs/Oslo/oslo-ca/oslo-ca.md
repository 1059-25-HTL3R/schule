# oslo-ca

- Rolle AD CS installieren
- Wizard zur Konfiguration einer Enterprise Root-CA durchgehen, neue Keys generieren
- Rolle Web Server (IIS) installieren
- Ordner C:\CertEnroll erstellen und SMB- & NTFS-Berechtigungen für Gruppe "Cert Publishers" auf zumindest "Ändern" stellen
- Ordner im IIS-Manager freigeben und "Double Escaping" aktivieren
- AIA- und CDP-Pfade unter Extensions ergänzen


Auf oslo-dc2 gpmc.msc:

- Neue GPO, auf Domäne verlinken "PKI-Policy"
- Root-Zertifikat importieren
- Auto-Enrollment in der Computer- und User-Configuration aktivieren und die zwei Häkchen mitnehmen
- Zertifikatstemplates "Workstation Authentication" und "Users" duplizieren, Auto-Enrollment für Domain Computers bzw. Domain Users aktivieren
