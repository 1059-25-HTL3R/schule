# BS 3.4 – TFTP-Server mit AppArmor absichern

## 1. Grundlagen

### TFTP

Das **Trivial File Transfer Protocol (TFTP)** ist ein einfaches, verbindungsloses Protokoll für den Transfer kleiner Dateien über UDP.  
Einsatzgebiete: Boot-Images, Firmware-Updates, Konfigurationsdateien von Netzwerkgeräten.  

**Sicherheitsaspekt:** TFTP hat keine Authentifizierung. Deshalb sollte der Server **nur intern** betrieben und zusätzlich durch **AppArmor** abgesichert werden.

---

## TFTP-Server Konfiguration (Ubuntu/Linux)

### Installation

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install tftpd-hpa 
```

### Konfiguration

Bearbeite `/etc/default/tftpd-hpa`:

`sudo nano /etc/default/tftpd-hpa`

``` bash
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/srv/tftp"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="--secure --create"
```

### Verzeichniss erstellen

```bash
chmod 755 /srv/tftp/
chown tftp:tftp /srv/tftp
touch /srv/tftp/running-config
```

### Service starten

```bash
sudo systemctl restart tftpd-hpa
sudo systemctl enable tftpd-hpa
```

---

## App Armor Konfiguration

### Instalation

```bash
sudo apt install apparmor-utils

```

### Profile erstellen

Dateil `/etc/apparmor.d/usr.sbin.tftpd-custom` erstellen:

```bash
  #include <tunables/global>

profile tftpd-custom /usr/sbin/in.tftpd {
  #include <abstractions/base>
  #include <abstractions/nameservice>

  /etc/hosts.allow r,
  /etc/hosts.deny r,

  # Für --secure (chroot ins TFTP-Verzeichnis)
  capability sys_chroot,

  capability net_bind_service,
  capability setuid,
  capability setgid,

  # Netzwerkzugriffe (UDP + dynamische Ports)
  network inet dgram,
  network inet6 dgram,

  # TFTP-Verzeichnis
  /srv/tftp/ r,
  /srv/tftp/** rwkl,

  # Eigenes Programm
  /usr/sbin/in.tftpd ix,

  # Bibliotheken
  /lib/** r,
  /usr/lib/** r,
  /proc/*/status r,
}
```

| erlaubte aktioenen                     | warum nötig                                            |
| -------------------------------------- | ------------------------------------------------------ |
| capability net_bind_service            | Darf auf Port 69 (unter 1024) lauschen                 |
| capability sys_chroot                  | Darf sich in /srv/tftp „einsperren“ (--secure)         |
| capability setuid + setgid             | Darf vom root zum User tftp wechseln                   |
| network inet dgram + inet6 dgram       | UDP-Netzwerkverkehr (TFTP ist UDP)                     |
| /srv/tftp/** rwkl                      | Darf nur in diesem Verzeichnis Dateien lesen/schreiben |
| /etc/passwd, /etc/group, nsswitch usw. | Darf den User tftp auflösen                            |
| /lib/\*\* und /usr/lib/\*\*            | Darf seine Bibliotheken laden                          |
| /usr/sbin/in.tftpd ix                  | Darf sich selbst ausführen                             |

**INFO!** AppArmor hat 2 Modi

- Enforce
  Verstöße gegen das Regelwerk werden blockiert und protokolliert.
- Complain
  Verstöße werden nur im Log protokolliert, aber nicht verhindert, ideal zum Erstellen neuer Profile.
  
### Aktiviren

```bash
sudo apparmor_parser -r /etc/apparmor.d/usr.sbin.tftpd-custom
sudo aa-enforce /etc/apparmor.d/usr.sbin.tftpd-custom
sudo aa-status
```

### Firewall

```bash
sudo ufw allow 69/udp
```

### Testen

#### Download

```bash
cd /tmp
tftp localhost # Öffnet tftp modus
get running-config
quit
cat running-config
```

`cat` sollte denselben inhalt wie von `/srv/tftp/running-config` anzeigen

#### Upload

vorberreiten:

```bash
echo "Modified device configuration" > /tmp/mod-config.txt
sudo touch /srv/tftp/uploaded-config.txt
sudo chown tftp:tftp /srv/tftp/uploaded-config.txt
sudo chmod 664 /srv/tftp/uploaded-config.txt
```

hochladen:

```bash
tftp localhost # öffnet tftp modus
put /tmp/mod-config.txt uploaded-config.txt
quit
```

prüfen:

```bash
sudo ls -la /srv/tftp/uploaded-config.txt
sudo cat /srv/tftp/uploaded-config.txt
sudo rm /srv/tftp/uploaded-config.txt  # Aufräumen
```
