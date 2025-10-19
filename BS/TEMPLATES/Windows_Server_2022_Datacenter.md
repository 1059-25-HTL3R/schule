# Windows_Server_2022_Datacenter Template description

produkt key: **WFTDG-FNTW2-RVMQ9-RBPXX-CDPY7**

- ## 1 - VM Aufsetzen
    - eine Leere VM aufsetzen 
    - NAT interface _(für Internet)_
    - die gewüschte ISO datei im CD-laufwerk einlegen.
    - VM hochfahren und von der **ISO** booten.
    - instalations process abschließen _(Product key muss nicht eingegeben werden)_
    - passwort eingeben und einloggen
        - passwort: **junioradmin123!**

    Instalation -> **Abgeschloßen**
- ## 2 - VM-ware-Tools instalation
    - im VM-ware reiter "VM" -> "install VM-Ware-Tools" auswählen.
    - im explorer sollte eine neue ISO erscheinen
    - in dieser ISO "setup" ausführen
    - standard ist ausreichend
    - neustarten

    ->VM-Ware-Tools -> fertig
- ## 3 - Template Erstellen
    - in das `%SystemRoot%\system32\sysprep\` direcotry wechseln
    
        command `cd %SystemRoot%\system32\sysprep\`
    - den command: `%SystemRoot%\system32\sysprep\sysprep.exe /quiet /oobe /generalize /shutdown`
    ausführen
    - nachdem der command fertig ist die **VM NICHT mehr andrehen!!!**
    - die ISO entfernen in den VM-settings
    - in den VM-settings im reiter "options" ganz unten in "advanced" ein häckchen bei "Enable Template mode" setzen
    - zum Abschluss nochmal einen Snapshot erstellen *(dieser wird zum klonen verwendet)*

 -> **Template erfolgreich erstellt**