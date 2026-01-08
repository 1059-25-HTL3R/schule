# BS 3.5 

## Angabe: 
Wordpress ist das weltweit am meisten verwendete CMS. Verwende das Metasploit-Framework, um in einen Webserver mit installiertem Wordpress einzudringen. Im ersten Schritt setzt du entweder unter Kali oder in einer eigenen VM mit docker-compose einen Wordpress-Server auf. Im zweiten Schritt führst du mit Metasploit einen Scan durch. Im dritten Schritt führst du eine Password-Attacke auf Wordpress mit wpscan durch. Im letzten Schritt bekommst du Zugang zum Server mithilfe einer "Reverse Shell". Mit Meterpreter kannst du jede Menge an Infos vom attackierten System herausfinden. 

In den Labornotizen findet man zum Schluss eine Schritt für Schritt Anleitung der Attacke.
- Metasploit 2nd Edition, The Penetration Tester’s Guide, no starch press, 2025
- Metasploit Penetration Testing Cookbook, Third Edition, Packt, 2018
- https://docs.docker.com/compose/wordpress/
- https://jonathansblog.co.uk/metasploit-tutorial-for-beginners
- https://www.offensive-security.com/metasploit-unleashed/
- https://www.hackingarticles.in/multiple-ways-to-crack-wordpress-login/
- https://www.hackingarticles.in/wordpress-reverse-shell/




---
## 1. Anforderungen

1. Kali linux und Ubuntu VM erstellen
2. Beide sollen ein NAT interface haben und über DHCP ihre IP beziehen

## 2. Ubuntu Server Konfiguration:

Linux Ubuntu aufsetzen und diese Anleitung befolgen:
- https://dev.to/teetoflame/virtual-machine-setup-and-wordpress-installation-documentation-28m7 

### 2.1. Web server installieren: 

Prerequisites installieren: 
```
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php unzip wget -y
```

Secure MySQL Installation starten und den Installationsprozess folgen: 

```
sudo mysql_secure_installation
```

WordPress Datenbank anlegen: 

```
sudo mysql -u root -p
```
Hier jetzt im SQL Prompt eine Datenbank, User anlegen und Privileges verteilen. 

```
CREATE DATABASE wordpress;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'Ganzgeheim123!';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Jetzt WordPress installieren

``` 
wget https://wordpress.org/latest.tar.gz
tar -xvzf latest.tar.gz
sudo mv wordpress /var/www/html/
```

Permission an Wordpress verteilen: 
```
sudo chown -R www-data:www-data /var/www/html/wordpress
sudo chmod -R 755 /var/www/html/wordpress
```

### 2.2 Apache Server anpassen für WordPress: 

Konfiguration von Apache anpassen damit die Startseite von Wordpress genommen wird und nicht die default von apache

```
sudo nano /etc/apache2/sites-available/000-default.conf
```

Jetzt in dem File DocumentRoot verändern: 

```
DocumentRoot /var/www/html/wordpress
```

Nun die Default Apache Site deaktivieren und rewrite modul aktivieren und apache neustarten, damit die Änderungen effekt nehmen: 

```
sudo a2dissite 000-default.conf
sudo a2enmod rewrite
sudo systemctl restart apache2
```

Jetzt wenn man im Browser die IP des Servers eingibt sollte man auf die Installations Fenster von Wordpress kommen.

### 2.3 WordPress Erstinstallation

Den Installations Dialog einfach folgen. 

![images](./IMAGES/Wordpress_Install_1.png)

Danach kommt man dann zu einem anderen Fenster dort dann auch einfach installation durchlaufen. 

## 3. Kali Linux 

### 3.1 WPscan 
- https://www.hackingarticles.in/multiple-ways-to-crack-wordpress-login/

WPscan is a command-line tool which is used as a black box vulnerability scanner. It is commonly used by security professionals and bloggers to test the security of their website. WPscan comes pre-installed on the most security-based Linux distributions and it is also available as a plug-in.

wpscan --url http://192.168.1.100/wordpress/ -U users.txt -P /usr/share/wordlists/rockyou.txt


