# Evolution of firewalls Pt.1

#### Jan Willimek, Benjamin Zwettler

## Überblick

| Merkmal | Packet Filter | Stateful / CBAC | ZBF |
| - | - | - | - |
| Stateful | nein | ja | ja  |
|Konf. Aufwand| hoch | mittel | mittel |
| Anwendung | einfache acces control (Bsp. ssh whitelist) | dynamischer verkehr (Bsp. rückverbindungen ermöglichen) | Netzwerk Segmentierung (Bsp. unterschiedliche vertrauensberreiche) |

---
# Packetfilter (ACL's)
ende der 1980er: der erste Ansatz einer Firewall

Öffnete den Weg für standhaftere Firewalls.

---

#### Packetfilter Eigenschaften:
    
Filtern einzelne Pakete anhand einfacher Regeln:

- Source und Destination IP-Adresse
- Port Nummer
- Protokoll

---
Packetfilter Firewalls sind **Stateless**!



- Jedes Packet wird **individuel** inspected
- keine History von altem Traffic
    - Stateless Firewalls erkennen nicht ob ein eingehendes Packet eine Antwort auf eine davor gesendete Request ist.
- filtert nur anhand von einfachen Regeln
    - es werden nur IP, Port und Protokoll überprüft
- sehr viel Konfigurationsaufwand bei komplexen Topologien

---
#### Funktionsweise

ACLs *(Paketfilter)* sind einfach und relativ schnell zu konfigurieren. Haben aber nur sehr spezielle Anwendungen

**Bsp:**

Um lokalen SSH-Zugang als Whitelist zu ermöglichen, verwenden wir eine ACL, die ausgewählte Adressen, die mit TCP Port 22 (SSH) anfragen, zulässt.

<img src="./IMAGES/ACL/Clean_ACL_beispiel_screenshot_fixed2.png"  width="60%" height="">

*der rechte Router (R2) kann nur noch von der Adresse 10.0.0.2 via ssh administriert werden.*

# Stateful & Cbac
### Stateful Firewalls:
Anfang 2000er zweite Generation von Firewalls:

#### Stateful Firewalls Eigenschaften
- Pakete werden nicht mehr isoliert betrachtet, sondern im Zusammenhang mit zuvor gesehenem Traffic
- Pakete werden als Teile einer Session/Connection zwischen Hosts angesehen
- Zusammenhänge zwischen den Paketen werden beachtet um die Zugehörigkeit zu einer Connection zu überprüfen
- Die Stateful Firewall aktualisiert den Status der einzelnen Verbindungen
- Nur Pakete die zu gültigen/bestehenden Connections gehören werden zugelassen

#### Funktionsweise:

Stateful Firewalls befinden sich auf Layer 3 und 4 im OSI Modell.

##### States    
Werden vewendet um den Status der Session anzugeben.

**Bsp:**
Aufbau einer TCP Connection:
- Die TCP Phasen SYN, SYN-ACK und ACK werden von der Stateful Firewall verwendet um die involvierten Kommunikationspartner zu identifizieren
- Die Firewall findet während des Handshakes die Informationen über Quelle, Ziel, Reihenfolge der Pakete und Daten innerhalb des Pakets heraus
- Diese Informationen werden zur Filterung von Paketen verwendet
- Nach einem TCP Reset (RST) oder Finish (FIN) wird die Connection für die Löschung vorbereitet und alle zukünftigen Pakete der Connection gedropped

##### State table
Wird verwendet um zu überprüfen ob Pakete zu einer vorhandenen Session gehören.
Beinhaltet Session Information:
  - Source IP
  - Destination IP
  - Protocol
  - Current State of Connection

#### Cbac
Cbac ist die Cisco Implementation der Stateful Packet Inspection

Cbac überprüft neben Layer 3 und 4 auch ein paar Layer 7 Informationen

# Zone based Firewalls

anfang der 2000er: als Erweiterung der Stateful-Firewalls

#### ZBF Eigenschaften:
- **Zonen**
    - Interfaces werden **genau einer** Zone zugewiesen.
    - Traffic innerhalb derselben Zone ist standardmäßig erlaubt, zwischen verschiedenen Zonen jedoch standardmäßig verboten, solange kein Zone-Pair existiert.
    --- 


- **Class-Maps**
    
    in der classmap wird festgelegt:
    - welche Traffic-Typen / Protokolle inspiziert oder gefiltert werden sollen (z. B. tcp, udp, http, icmp, usw.)

    - optional: eine Match-ACL, die Traffic anhand von IP-Adressen/Ports definiert
    (z. B. „nur 10.0.0.0/24 darf über TCP 22“)

    Wichtig: Class-Maps definieren nur welcher Traffic gematcht wird – **nicht** was damit passiert.
    
    --- 
    
- **Policy-Maps**

    In einer Policy-Map wird festgelegt:

    - welche Class-Maps angewendet werden, und

    - wie der gematchte Traffic behandelt werden soll:
      - ```inspect```(zustandsbehaftete Firewall – erlaubt Rückverkehr)
      - ```pass```(einfach erlauben, ohne Inspektion)
      - ```drop```(verwerfen)
    - Was mit nicht gematchtem Traffic passiert
    (Standard ist drop, wenn nicht anders konfiguriert)

    ---

- **Zonen-Paare**

    In einem Zonen-Paar wird definiert:
    
    - von welcher Source-Zone
    - zu welcher Destination-Zone
    - welche Policy-Map auf diesen Traffic angewendet wird

## Config
- ### Packetfilter *(ACL)*
    Acces-List:
    ```
    Router(config)#ip access-list extended TerminalAccess
    Router(config-ext-nacl)#permit tcp host 10.0.0.2 any eq 22
    Router(config-ext-nacl)#deny tcp any any
    Router(config-ext-nacl)#exit
    ```
    List auf line geben:
    ```
    Router(config)#line vty 0 4 
    Router(config-line)#access-class TerminalAccess in
    Router(config-line)#exit
    ```
    auf interface:
    ```
    Router(config)#interface GigabitEthernet 0/0
    Router(config-if)#ip access-group TerminalAccess in
    Router(config-if)#exit
    ```


- ### Stateful FW (CBAC)
    ```
    ip inspect name MY-CBAC http
    ip inspect name MY-CBAC ftp
    interface GigabitEthernet0/0
     ip inspect MY-CBAC out
     
    ```

- ### Zone-Based-Firewall
    Zonen erstellen:
    ```
    zone security INSIDE
    zone security OUTSIDE
    ```

    Interfaces Zonen zuweisen:
    ```
    interface GigabitEthernet0/0
     zone-member security INSIDE
    
    interface GigabitEthernet0/1
     zone-member security OUTSIDE
    ```

    Traffic Klassen erstellen:
    ```
    class-map type inspect match-any EXAMPLEMAP
    match access-group 101
    ```


## Fun facts

- Der Begriff "Firewall" stammt von Brandmauern welche im Bauwesen verwendet werden um die Ausbreitung von Feuer in kleinere Bereiche einzudämmen.

## Quellen
- [paloaltonetworks.com](https://www.paloaltonetworks.com/cyberpedia/history-of-firewalls)
- [illumio.com](https://www.illumio.com/blog/firewall-stateful-inspection)
- [fortinet.com](https://www.fortinet.com/de/resources/cyberglossary/stateful-firewall)
- [paubox.com](https://www.paubox.com/blog/what-is-a-stateful-firewall)
- [geeksforgeeks.org (ZBF)](https://www.geeksforgeeks.org/computer-networks/zone-based-firewall/)
- [geeksforgeeks.org (cbac)](https://www.geeksforgeeks.org/computer-networks/context-based-access-control-cbac/)
- [cisco.com](https://www.cisco.com/c/de_de/support/docs/unified-communications/unified-border-element/220378-configure-zone-based-firewall-zbfw-co.html)
- [cisco.com (cbac)](https://www.cisco.com/c/en/us/td/docs/ios/sec_data_plane/configuration/guide/12_4/sec_data_plane_12_4_book/sec_cfg_content_ac.pdf)