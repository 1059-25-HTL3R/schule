# Ansible aufsetzen

Ansible besteht aus zwei grundlegenden bausteinen:
- **Control-Node**
    
    die Control node stellt eine Verbindung mit den Target nodes her und configuriert diese standartmässig über ssh.

- **Target Nodes**

    muss von der Controlnode erreichbar sein damit sie konfiguriert werden kann.
    - ssh server ist standart (muss oft manell instaliert werden)
---
**Wichtig!!!**

auf **Windows** schaut die Geschichte ganz anders aus! (nicht über ssh sonder winrm)

---


## Control-Node

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
        *hier müssen nicht umbeding änderungen vorgenommen werden*


---

## Target-Node(s)

Target server/nodes müssen nur eine ssh verbindung mir der Control node aufbauen können damit ansible die konfiguration des jeweiligen servers übernehmen kann.

### best practice:
- ssh instalieren und den ssh-Schlüssel von der Control node auf den Target Server spielen.

    mit dem Command:
    ```
    ssh-copy-id <Target-IP>
    ```
---

## Sudo commands:

manche Befehle benötigen root Rechte um ausgeführt werden zu können

- *Beispiel: der Command zum ändern des hostnames.*

Doch da ein root level login über ssh einige Sicherheits-Risiken aufbringt verwenden wir: **ansible Vaults**
