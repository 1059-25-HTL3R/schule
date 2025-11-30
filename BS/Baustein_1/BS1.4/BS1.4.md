# BS1.4

## Ansible
<img src="./IMAGES/Ansible_logo.svg.png"  width="40%" height="30%">

## Aufgabe
Lab: Richte eine grundlegende Ansible Testumgebung, bestehend aus Linux Systemen, ein. 
- Grundkonfiguration
- Konfiguration eines DHCP Servers 
- Konfiguration eines DNS Servers 
-  Firewall-Konfiguration
Konfiguration von statischem Routing

## Plan
wir wollen [ansible](https://docs.ansible.com/) aufzusetzen um damit schnell Linux server aufsetzen und konfigurieren.

Dazu benötigen wir eine ["Control-Node"](https://docs.ansible.com/projects/ansible/latest/network/getting_started/basic_concepts.html#control-node) die als orchestrator agiert. (einen PC / Server der alle anderen nodes ansteuert und via ssh oder winrm (bei windows clients) konfiguriert).



## Control-Node
| interface    | ip          | lan segment|
| ------------ | ----------- | ---------- |
| Ethernet1    | 192.168.2.2 | Lan_2-1    |
| NAT-Interface| DHCP        | - |

- ### Konfiguration
    


## Keywords
    - module
    - task
    - template
    - playbook
    - plugin