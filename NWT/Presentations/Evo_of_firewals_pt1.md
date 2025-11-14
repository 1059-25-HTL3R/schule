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

<GRAFIK EINFÜGEN>


## Statefull & Cbac

## Zone based Firewalls

## Fun facts

## Quellen
- [www.paloaltonetworks.com](https://www.paloaltonetworks.com/cyberpedia/history-of-firewalls)