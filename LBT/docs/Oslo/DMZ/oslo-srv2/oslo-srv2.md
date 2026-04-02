# oslo-srv2

- [oslo-srv2](#oslo-srv2)
  - [BIND9](#bind9)
    - [Instalation](#instalation)
    - [Configuration](#configuration)
    - [Service neustarten](#service-neustarten)
  - [Postfix](#postfix)
    - [Installieren](#installieren)
    - [Grund-Konifigurieren](#grund-konifigurieren)
    - [Mailbox format Konfigurieren](#mailbox-format-konfigurieren)
    - [Smpt authentication konfiguration](#smpt-authentication-konfiguration)
    - [TLS Konfiguration](#tls-konfiguration)
  - [Prometheus Monitoring service](#prometheus-monitoring-service)
    - [Node exporter Instalation](#node-exporter-instalation)
    - [Automatisches Starten](#automatisches-starten)
    - [Service aktiviren und starten](#service-aktiviren-und-starten)

## BIND9

Ein DNS-Server in der DMZ soll als DNS-Forwarder für das gesammte Active-Direcoty dienen.

Source: [Official BIND9 Guide](https://ubuntu.com/server/docs/how-to/networking/install-dns/)

### Instalation

```bash
sudo apt install bind9
```

### Configuration

in der ``/etc/bind/named.conf.options`` forwarder eintragen:

- 8.8.8.8
- 1.1.1.1

``sudo nano /etc/bind/named.conf.options``

```bash
options {
    forwarders {
        1.2.3.4;
        5.6.7.8;
    };
};
```

Schliussendlich soll das File so ausehen:

![/etc/bind/named.conf.options_konfiguriert](./IMAGES/BIND9_etc_bind_named.conf.options_forwarders.png)

### Service neustarten

```bash
sudo systemctl restart bind9.service
```

---

## Postfix

Source: [Official Postfix Guide](https://ubuntu.com/server/docs/how-to/mail-services/install-postfix/)

### Installieren

```bash
sudo apt install postfix
```

der Instalationsdialouge wird später nochmal ausgeführt.

### Grund-Konifigurieren

Eckdaten:

- Domain: ``equinor.no``
- network / class range: alle netze aus der email kommen können
- username: ``steve``
- mailbox format: Maildir

Instalationsdialoige nochmal ausführen

```bash
sudo dpkg-reconfigure postfix
```

Im dialouge:

- Internet Site
- ``equinor.no``
- ``steve``
- ``equinor.no``, localhost.localdomain, localhost
- no
- 127.0.0.0/8 \[::ffff:127.0.0.0\]/104 \[::1\]/128 ``<alle netze aus denen eine email kommen kann>``
- 0
- \+
- all

### Mailbox format Konfigurieren

```bash
sudo postconf -e 'home_mailbox = Maildir/'
```

Das Platziert neie mails in ``/home/<username>/Maildir``, also muss der "Mail Delivery Agend (MDA)" auf den selben pfad konfigurieren sein.

### Smpt authentication konfiguration

```bash
sudo postconf -e 'smtpd_sasl_type = dovecot'
sudo postconf -e 'smtpd_sasl_path = private/auth'
sudo postconf -e 'smtpd_sasl_local_domain ='
sudo postconf -e 'smtpd_sasl_security_options = noanonymous'
sudo postconf -e 'broken_sasl_auth_clients = yes'
sudo postconf -e 'smtpd_sasl_auth_enable = yes'
sudo postconf -e 'smtpd_recipient_restrictions = \permit_sasl_authenticated,permit_mynetworks,reject_unauth_destination'
```

### TLS Konfiguration

**MUSS NOCH GEMACH WERDEN!!!!!!**

geht grad nicht weil die UCS noch gewartet wird (CA wird nicht hochgefahren)

## Prometheus Monitoring service

### Node exporter Instalation

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz
tar xvfz node_exporter-1.10.2.linux-amd64.tar.gz
```

### Automatisches Starten

Eine neue service File erstellen

```bash
sudo nano /etc/systemd/system/prometheus-node-exporter.service
```

diesen inhalt hineingeben:

```bash
[Unit]
Description=Prometheus Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=root
Group=root
ExecStart=/home/junioradmin/node_exporter-1.10.2.linux-amd64/node_exporter

Restart=always
RestarSec=5s
ProtectSystem=strict
NoNewPrivileges=true
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

### Service aktiviren und starten

```bash
#systemd neu laden
sudo systemctl daemon-reload

#service für autostart enablen  
sudo systemctl enable prometheus-node-exporter.service

#service starten
sudo systemctl start prometheus-node-exporter.service
```
