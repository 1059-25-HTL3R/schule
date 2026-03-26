
Erkunden Sie, wie session-based Remote Desktop Services implementiert und wie RDS Session Hosts aufgesetzt und konfiguriert werden. Erstellen Sie ein HowTo für die Implementierung eines RDS Session Hosts.

---

## 1. Voraussetzungen planen

- AD vorhanden aus den vorherigen Übungen (Ich habe das aus der PKI Übung verwendet) 
- Firewall‑Regel: TCP 3389 erlauben
- RDS Server IP: 192.168.1.12/24 + Hostname geändert auf RDS_Server
---

## 2. RDS Session Host Rolle installieren

### 2.1 Wizard installation

1. Auf dem RDS Server als Domain-Admin anmelden und **Server-Manager** öffnen.
2. **Manage → Add Roles and Features** wählen.
3. **Role‑based or feature‑based installation** auswählen, Zielserver wählen.  
4. Unter **Server Roles** die Rolle **Remote Desktop Services** aktivieren.
5. Unter **Role Services** mindestens wählen: 
   - **Remote Desktop Session Host**  
   - **Remote Desktop Licensing**, falls dieser Server auch Lizenzserver sein soll  
6. Assistent mit **Next** und **Install** abschließen, automatischen Neustart erlauben.

---

## 3. Benutzerrechte und RDP-Zugriff

1. Im **Server-Manager** unter **Local Server → Remote Desktop** prüfen, dass „Allow remote connections“ aktiviert ist.  
2. In den **Systemeigenschaften → Remote** sicherstellen:  
   - „Allow remote connections to this computer“ aktiv  
   - optional „Allow connections only from computers running Remote Desktop with Network Level Authentication“ aktivieren.
3. Benutzer/Gruppen, die sich per RDP anmelden dürfen, in die lokale oder domänenbasierte Gruppe **Remote Desktop Users** aufnehmen.
4. Optional über GPO: Sitzungslimits, Idle‑Timeouts, maximale Verbindungen usw. im Zweig  
   `Remote Desktop Services → Remote Desktop Session Host → Connections / Session Time Limits` konfigurieren.

---

## 4. Anwendungen und Profile

- Benötigte Anwendungen (Office, Fachanwendungen) direkt auf dem Session Host installieren, ggf. im Installationsmodus (`change user /install` vor, `change user /execute` nach der Installation bei älteren Apps).
- Für größere Umgebungen oder mehrere Hosts: Einsatz von **User Profile Disks** oder servergespeicherten Profilen in einer RDS‑Collection einplanen.

---

## 5. (Optional) Session-basierte RDS-Collection mit Broker

Für eine vollwertige RDS‑Umgebung mit Connection Broker und Web Access:

1. Auf einem Management‑Server im **Server-Manager** zu **Remote Desktop Services → Overview** wechseln. 
2. **Tasks → Create Session Deployment** wählen und **Session-based desktop deployment** auswählen. 
3. Serverrollen zuweisen:  
   - RD Connection Broker  
   - RD Web Access  
   - RD Session Host (dein Host aus Abschnitt 2) 
4. Nach Abschluss unter **Remote Desktop Services → Collections → Tasks → Create Session Collection** eine neue Collection anlegen, Session Host hinzufügen, Benutzergruppen und Pfad für User Profile Disks definieren.

---

## 6. Funktionstest und Härtung

1. Von einem Client aus **mstsc.exe** starten und mit Servernamen oder veröffentlichtem FQDN verbinden. 
2. Mit einem berechtigten Benutzerkonto anmelden und prüfen, ob Desktop, Anwendungen, Drucker und Zwischenablage erwartungsgemäß funktionieren.
3. Sicherheitsmaßnahmen umsetzen:  
   - RDP nur intern freigeben, externer Zugriff ausschließlich über VPN oder RD Gateway.
   - Starke Passwortrichtlinien, ggf. MFA am Gateway.
   - Lokale Administratoren minimieren, Logging/Monitoring (z.B. SIEM) aktivieren.

---

