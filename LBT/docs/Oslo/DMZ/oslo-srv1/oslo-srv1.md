# oslo-srv1

## Apache

Source: [Official Ubuntu-Apache Guide](https://ubuntu.com/tutorials/install-and-configure-apache)

### Apache-instalation

instalieren:

```bash
sudo apt install apache2
```

FEHLT: HTTPS, TLS Zertifikate

## rSyslog

Source: [Official rsyslog Guide](https://www.rsyslog.com/ubuntu-repository/)

### rSyslog-instalation

instalieren:

```bash
sudo add-apt-repository ppa:adiscon/v8-stable
sudo apt-get update
sudo apt-get install rsyslog
```

konfiguration:

den syslog diesnt über den TCP Port 514 erreichbar machen.

im File ``/etc/rsyslog.conf`` folgende zeilen auskommentiern:

![rsyslog_oslo-srv1_TCP](./IMAGES/rsyslog_TCP.png)

## uFTPd

Source: [Official uFTPd Guide](https://www.uftpserver.com/wiki/uftp-server-installation)

### uFTPd-instalation

Prerequisits instalieren:

```bash
sudo apt-get install git gcc make
```

uFTPd source klonen:

```bash
git clone https://github.com/kingk85/uFTP.git
```

FEHLT: COMPILEN UND ÄNDERUNGEN im ``Makefile`` dokumentieren.
