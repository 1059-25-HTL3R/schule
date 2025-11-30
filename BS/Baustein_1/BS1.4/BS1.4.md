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
    - **ansible Instalieren:**

        wir verwenden eine ubuntu VM als control node.

        ansible benutzt ein PPA (Personal Package Archive).

        PPA zu repos hinzufügen:
        ```
        sudo apt update
        sudo apt install software-properties-common
        sudo add-apt-repository --yes --update ppa:ansible/ansible
        ```
        ansible instalieren:
        ```
        sudo apt install ansible
        ```

        [Quelle](https://docs.ansible.com/projects/ansible/latest/installation_guide/installation_distros.html)
    ---

    - **ansible konfigurieren:**
    
        konfig file:
        ```
        /etc/ansible/ansible.conf
        ```
        





## Ansible Playbooks
- ### Grundkonfiguration von servern
    
    was muss konfiguriert werden?
    - Hostname
    - interfaces
        - mindestens ein Interface muss konfiguriert sein damit ansible das gerät konfigurieren kann.
    - User?
    - 


    


## Keywords
- **playbook**
    
    Ein Bauplan in ```yaml``` geschrieben.


    [Quelle](https://docs.ansible.com/projects/ansible/latest/playbook_guide/index.html)
- **task**
- **module**    
- **template**
- **plugin**