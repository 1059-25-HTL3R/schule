# BS1.4

## Ansible
<img src="./IMAGES/Ansible_logo.svg.png"  width="40%" height="30%">

## Plan
wir wollen [ansible](https://docs.ansible.com/) lernen aufzusetzen und damit Linux server aufsetzen und konfigurieren.

Dazu benötigen wir eine ["Control-Node"](https://docs.ansible.com/projects/ansible/latest/network/getting_started/basic_concepts.html#control-node) die als orchestrator agiert. (einen PC / Server der alle anderen nodes ansteuert und via ssh oder winrm (bei windows clients)).



## Control-Node
| interface    | ip          | lan segment|
| ------------ | ----------- | ---------- |
| Ethernet1    | 192.168.2.2 | Lan_2-1    |
| NAT-Interface| DHCP        | - |

- ### Konfiguration
    

