# Evolution of firewalls Pt.1

#### Jan Willimek, Benjamin Zwettler

## Überblick

| Merkmal | Packet Filter | Stateful / CBAC | ZBF |
| - | - | - | - |
| Stateful | nein | ja | ja  |
|Konf. Aufwand| hoch | mittel | mittel |
| Anwendung | einfache acces control (Bsp. ssh whitelist) | dynamischer verkehr (Bsp. rückverbindung ermöglichen) | Netzwerk segmentierung (Bsp. unterschiedliche vertrauensberreiche) |

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

ACLs *(Paketfilter)* sind einfach und relativ schnell zu konfigurieren. Haben aber nur sehr spezielle anwendungen

**Bsp:**

Um lokalen ssh zugang als whitelist zu ermöglichen verwenden wir eine ACL die ausgewählte Addressen auf unseren ssh tcp port zuläst und den rest blockiert.

<img src="./IMAGES/ACL/Clean_ACL_beispiel_screenshot.png"  width="100%" height="">

*der rechte router kann nur noch von der addresse 10.0.0.2 via ssh administriert werden.*

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


# Zone based Firewalls

anfang der 2000er: als Erweiterung der Statefull-Firewalls

#### ZBF Eigenschaften:
- Zonen
    - interfaces werden zonen zugewiesen
- Zonen-Paare


    - haben "Richtungen"
        
        das Zonen-Paar "A Nach B" kann sich anders verhalten als das Zonen-Paar "B nach A"
- class-map's
    
    in der classmap wird bestimmt:

    - welche "Typen" an Traffic überprüft werden (Bsp. https, tcp, etc.)

    - eine ACL die traffic anhand von addressen zuläst
    
- policy-map's
    - 


## Fun facts



## Quellen
- [paloaltonetworks.com](https://www.paloaltonetworks.com/cyberpedia/history-of-firewalls)
- [illumio.com](https://www.illumio.com/blog/firewall-stateful-inspection)
- [fortinet.com](https://www.fortinet.com/de/resources/cyberglossary/stateful-firewall)
- [paubox.com](https://www.paubox.com/blog/what-is-a-stateful-firewall)
- [geeksforgeeks.org](https://www.geeksforgeeks.org/computer-networks/zone-based-firewall/)
- [cisco.com](https://www.cisco.com/c/de_de/support/docs/unified-communications/unified-border-element/220378-configure-zone-based-firewall-zbfw-co.html)
