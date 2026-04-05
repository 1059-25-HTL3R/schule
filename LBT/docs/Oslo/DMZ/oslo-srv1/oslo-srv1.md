# oslo-srv1

- [oslo-srv1](#oslo-srv1)
  - [Apache](#apache)
    - [Apache-instalation](#apache-instalation)
    - [Zertifikat von CA erstellen](#zertifikat-von-ca-erstellen)
      - [Step 1: Create the "Apache-Manual" Template](#step-1-create-the-apache-manual-template)
      - [Step 2: Publish the Template](#step-2-publish-the-template)
      - [Step 3: Generate the CSR (On the Apache Server)](#step-3-generate-the-csr-on-the-apache-server)
      - [Step 4: Sign the CSR (On the CA/Windows Server)](#step-4-sign-the-csr-on-the-cawindows-server)
      - [Step 5: Export the Root CA](#step-5-export-the-root-ca)
    - [Apache HTTPS Konfiguration](#apache-https-konfiguration)
  - [rSyslog](#rsyslog)
    - [rSyslog-instalation](#rsyslog-instalation)
  - [uFTPd](#uftpd)
    - [uFTPd-instalation](#uftpd-instalation)
      - [1. The "Check Your Text" Test](#1-the-check-your-text-test)
      - [2. Converting to PEM (If needed)](#2-converting-to-pem-if-needed)
      - [3. Handling the UFTP "Passphrase" Issue](#3-handling-the-uftp-passphrase-issue)
      - [4. Final File Setup for UFTP](#4-final-file-setup-for-uftp)
      - [5. Correcting those Permissions](#5-correcting-those-permissions)


## Apache

Source: [Official Ubuntu-Apache Guide](https://ubuntu.com/tutorials/install-and-configure-apache)

### Apache-instalation

instalieren:

```bash
sudo apt install apache2
```

### Zertifikat von CA erstellen

#### Step 1: Create the "Apache-Manual" Template

You need to create a template on your CA that doesn't require a computer account.

1. Log into your **CA Server**.
2. Open **Certificate Templates Console** (`certtmpl.msc`).
3. Right-click the **Web Server** template and select **Duplicate Template**.
4. **General Tab:** Rename it to `OsloWebServer`.
5. **Subject Name Tab:** This is the most important part. Select **Supply in the request**.
    * *Note: You'll get a warning about security. This is fine for manual requests, as long as you (the admin) are the one signing them.*
6. **Extensions Tab:** Ensure **Server Authentication** is listed under Application Policies.
7. **Security Tab:** Ensure the user account you are logged in with has **Enroll** permissions.
8. Click **OK**.

#### Step 2: Publish the Template

The CA won't use the template until it's published.

1. Open **Certification Authority** (`certsrv.msc`).
2. Right-click **Certificate Templates** -> **New** -> **Certificate Template to Issue**.
3. Select your new `OsloWebServer` template and click **OK**.

#### Step 3: Generate the CSR (On the Apache Server)

When generating the CSR on Linux, make sure you include the **Subject Alternative Name (SAN)**. Modern browsers will mark your site as "Not Secure" if you only use the Common Name.

Create a file named `openssl.conf`:

```ini
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req
prompt = no

[req_distinguished_name]
C = NO
L = Stavanger
O = Equinor
CN = oslo-srv1.corp.equinor.no

[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1 = oslo-srv1.corp.equinor.no
DNS.2 = www.corp.equinor.no
```

Then run the command:

```bash
openssl req -new -out apache.csr -newkey rsa:2048 -nodes -keyout apache.key -config openssl.conf
```

#### Step 4: Sign the CSR (On the CA/Windows Server)

Copy that `apache.csr` file over to your CA (or any domain-joined machine where you have admin rights).

Open **Command Prompt (Admin)** and run the following:

```cmd
certreq -submit -attrib "CertificateTemplate:Apache-DMZ" apache.csr
```

**What happens next:**

1. A dialog box will pop up. Select your **CA**.
2. If the template is set up correctly, it will immediately prompt you to save the output. 
3. Save it as `apache-signed.cer`.

#### Step 5: Export the Root CA

Apache needs the full chain. On your CA:

1. Run `certutil -ca.cert root-ca.cer`.
2. Move both `apache-signed.cer` and `root-ca.cer` back to your Apache server.

### Apache HTTPS Konfiguration

Move your new certificate and the Root CA cert to your Apache server (`/etc/ssl/certs/`) and your private key to `/etc/ssl/private`.

Edit your SSL virtual host configuration (`/etc/apache2/sites-available/default-ssl.conf`):

```conf
<VirtualHost *:443>
    ServerName webserver.yourdomain.com
    DocumentRoot /var/www/html

    SSLEngine on
    
    # The certificate you got from ADCS
    SSLCertificateFile /etc/ssl/certs/apache-server.crt
    
    # The private key you generated in Step 1
    SSLCertificateKeyFile /etc/ssl/private/apache-server.key
    
    # The Root CA certificate (so clients trust the chain)
    SSLCertificateChainFile /etc/ssl/certs/root-ca.crt
</VirtualHost>
```

Enable the SSL module and site (on Debian/Ubuntu):

```bash
sudo a2enmod ssl
sudo a2ensite default-ssl
```

Test the configuration:

```bash
sudo apache2ctl configtest
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

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

Source: [Official uFTPd Guide](https://www.uftpserver.com/wiki/uftp-server-installation) Option 1

### uFTPd-instalation

Prerequisits instalieren:

```bash
sudo apt-get install git gcc make
```

uFTPd source klonen:

```bash
git clone https://github.com/kingk85/uFTP.git
```

Optionen nach Installguide im `Makefile` anpassen

Zertifikat analog zu Apache. Dann noch:

In the world of SSL, extensions like `.key`, `.cer`, `.crt`, and `.pem` are often used interchangeably, which is confusing. Most of the time, the only difference is the **extension**, not the actual data inside.

Since you are moving from a Windows CA to a Linux-based UFTP/Apache environment, here is how to get the format exactly right.

#### 1. The "Check Your Text" Test

Before running any commands, open your `.key` and `.cer` files in a text editor (like Notepad or Nano).

* If the files look like this: `-----BEGIN CERTIFICATE-----` or `-----BEGIN RSA PRIVATE KEY-----` followed by a block of random text, they are **already in PEM format**. You can literally just rename them to `.pem`.
* If the files look like a bunch of scrambled symbols/gibberish, they are in **DER (Binary)** format and need to be converted.

#### 2. Converting to PEM (If needed)

If your files are binary (common with Windows exports), run these commands:

**For the Certificate:**

```bash
openssl x509 -inform der -in yourcert.cer -out cert.pem
```

**For the Key:**

```bash
openssl rsa -inform der -in yourkey.key -out key.pem
```

#### 3. Handling the UFTP "Passphrase" Issue

The screenshot you shared showed a specific command: `openssl rsa -in key.pem -out newkey.pem`. 

The purpose of that command in the UFTP guide is to **strip the password** from the private key. If your private key has a passphrase, UFTP will hang on startup because it’s waiting for a human to type the password.

**To create an "unlocked" key for UFTP:**

```bash
openssl rsa -in your_current_key.key -out /etc/uFTP/key.pem
```

*(If it asks for a password, type it once. The output file `key.pem` will now be unencrypted and ready for the service to use.)*

#### 4. Final File Setup for UFTP

Based on the standard UFTP setup, you should end up with two files in `/etc/uFTP/`:

1. **`key.pem`**: Your private key (unencrypted).
2. **`cert.pem`**: Your signed certificate from the ADCS CA. 
    * *Pro Tip:* If UFTP throws "Chain Verification" errors, you may need to append your Root CA to this file: 
        `cat yourcert.crt root-ca.crt > cert.pem`

#### 5. Correcting those Permissions

In your screenshot, the command `chmod -rwx` was used. **Don't do that.** It removes all read, write, and execute permissions, meaning even the UFTP service can't see the certificate.

**Use these instead:**

```bash
# Give root ownership
chown root:root /etc/uFTP/*.pem

# Private key: Read/Write for root ONLY (Safe)
chmod 600 /etc/uFTP/key.pem

# Certificate: Read for everyone, Write for root
chmod 644 /etc/uFTP/cert.pem
```

Danach kann man User erstellen in `/etc/uftpd.cfg`

Einloggen zb mittels WinSCP: FTP & Explicit encryption (TLS) & username/password
