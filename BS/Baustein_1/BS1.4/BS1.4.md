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

        ssh conection + ssh key übertragen + ssh auf remote aufsetzen
        





## Ansible Playbooks
- ### Grundkonfiguration von servern

    was muss konfiguriert werden?
    - Hostname
    - interfaces
        - mindestens ein Interface muss konfiguriert sein damit ansible das gerät konfigurieren kann.
    - packes upgraden
    
    - firewall?

    ### Das Inventory File:
    das inventory file ist dafür da die zu bearbeitenden hosts zu listen.

    ### Das Vault File:
    sensible daten wie Passwörter sollten nicht in klartext abgespeichert werden. Ansible bietet hierfür sogenannte "Vaults" an die die Passwörter verschlüsselt abspeichern und nur wärend dem ausführen entschlüsselt werden.

    **command zum erstellen eines vaults:**
    ```
    ansible-vault create <pfad zum erstellen>
    ```
    dannach muss mein ein vault passwort eingeben mit dem der vault verschlüsselt wird

    **command zum bearbeiten:**
    ```
    ansible-vault edit <pfad zum file>
    ```
    das vault passwort muss einggeben werde um das file zu bearbeiten



    ### Das Playbook:
    ```
    - name: My first play
    hosts: server_linux
    become: true

    vars_files:
    - vault.yml
  
    tasks:
    - name: Ping my hosts
     ansible.builtin.ping:

    - name: Print message
     ansible.builtin.debug:
       msg: Hello world
       
    - name: Set a hostname
     ansible.builtin.hostname:
       name: "{{ hostname }}"
    ```
    *dieses Playbook pingt alle hosts aus der gruppe "server_linux", gibt dann eine debug message aus und ändert den hostname von allen hosts zu den hostnames die im inventory file mit der variable hostname abgespeichert sind.*

    ### Der Command zum ausführen:
    ```
    ansible-playbook -i <path zum inventory file> <path zum playbook file> --ask-vault-pass
    ```

    


## Keywords
- **playbook**
    
    Ein Bauplan in ```yaml``` geschrieben.


    [Quelle](https://docs.ansible.com/projects/ansible/latest/playbook_guide/index.html)
- **task**
- **module**    
- **template**
- **plugin**
- **vaults**
    
    vaulst speichert sensible daten wie passwörter oder adminkonten gesichert ab.

    create vault:
    ```
    ansible-vault create vault.yml
    ```

    in die gerade erstellte "vault.yml" das passwort schreiben:
    ```
    vault_sudo_password: "junioradmin"
    ```

    
    [Quelle](https://docs.ansible.com/projects/ansible/latest/vault_guide/vault.html)