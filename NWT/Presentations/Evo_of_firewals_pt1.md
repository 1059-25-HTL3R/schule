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

Öffnete den weg für standhaftere Firewalls.

---

#### Packetfilter Eigenschaften:
    
Filtern einzelne Packete anhand einfacher regeln:

- Source und Destination IP-Adresse
- Port Nummer
- Protokol

---
Packetfilter firewalls sind **Stateless**!



- Jedes Packet wird **individuel** inspected
- keine History von altem Traffic
    - Stateless firewalls erkennen nicht ob ein eingehendes Packet eine antwort auf eine davor gesendete request ist.
- filtert nur anhand von einfachen regeln
    - es werden nur IP, Port und Protokol überprüft
- sehr viel Konfigurationsaufwand bei komplexen Topologien

---
#### Funktionsweise

ACLs *(Paketfilter)* sind einfach und relativ schnell zu konfigurieren. Haben aber nur sehr spezielle Anwendungen

**Bsp:**

Um lokalen SSH-Zugang als Whitelist zu ermöglichen, verwenden wir eine ACL, die ausgewählte Adressen, die mit TCP Port 22 (SSH) anfragen, zulässt.

<img src="./IMAGES/ACL/Clean_ACL_beispiel_screenshot_fixed2.png"  width="60%" height="">

*der rechte Router (R2) kann nur noch von der addresse 10.0.0.2 via ssh administriert werden.*

# Stateful & Cbac
### Stateful Firewalls:
Anfang 2000er zweite Generation von Firewalls:

#### Stateful Firewalls Eigenschaften
- Pakete werden nicht mehr generell als alleinstehnd angesehen
- Pakete werden als Teile eines größeren Austausches zwischen Hosts betrachtet
- Zustände der Verbindungen werden überwacht
- Kontext des Traffic wird ermittelt und zwischengespeichert
- Zusammenhänge zwischen den Packeten werden beachtet

#### Funktionsweise:
##### States    
Werden vewendet um den Status der Session anzugeben.

**Bsp:**
Client Application startet TCP Verbindung:
- bei TCP werden die 4 bits SYN,ACK,RST,FIN zur Bestimmung vom State verwendet
- TCP SYN Flag wird beim Start der Verbindung gesetzt
- Firewall erkennt das Flag und setzt State auf NEW
- Danach kommt die Antwort vom Server mit TCP SYN + ACK Flags
- Firewall setzt nun Connection State auf ESTABLISHED, da nun von beiden Richtungen Pakete erkannt wurden
- Nun folgt TCP RST oder FIN + ACK Flags
- Firewall bereitet State für Löschung vor

##### State table
Wird verwendet um zu überprüfen ob Pakete zu einer vorhandenen Session gehören.
Beinhaltet session Information:
  - Source IP
  - Destination IP
  - Protocol
  - State

#### Cbac
Cbac ist die Cisco Implementation der Stateful packet inspection

Cbac überprüft neben Layer 3 und 4 auch ein paar Layer 7 Informationen

# Zone based Firewalls

anfang der 2000er: als Erweiterung der Statefull-Firewalls

#### ZBF Eigenschaften:
- **Zonen**
    - interfaces werden **genau einer** Zonen zugewiesen.
    - Traffic innerhalb derselben Zone ist standardmäßig erlaubt, zwischen verschiedenen Zonen jedoch standardmäßig verboten, solange kein Zone-Pair existiert.
    --- 


- **Class-Maps**
    
    in der classmap wird festgelegt:
    - welche Traffic-Typen / Protokolle inspiziert oder gefiltert werden solle (z. B. tcp, udp, http, icmp, usw.)

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