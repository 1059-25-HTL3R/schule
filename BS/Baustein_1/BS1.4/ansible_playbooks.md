# Ansible Playbooks

ein playbook ist eine sammlung an sogenannten "tasks" welche eine angegebene Gruppe von Hosts konfiguriert.
- Sprache: Yaml
    
    Bsp. Playbook:
    ``` yml
    - name: My first playbook
      hosts: server_linux
  
      tasks:
        - name: Ping my hosts
          ansible.builtin.ping:
    ```
    *dieses Playbook pingt alle Hosts in der Gruppe "server_linux"*