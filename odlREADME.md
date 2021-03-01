Infos:
- Schritte/Prozesse dokumentieren
- Server: **sdi9b.mi.hdm-stuttgart.de**

# Icinga: 01.02.2021 - 22.02.2021

- [ ] Installation
    - apt install icinga2 icinga2-ido-mysql
    - mysql passwort: XHwKgtC9qrs8
    - systemctl start icinga2.service


# File cloud: 25.01.2021 - 01.03.2021

## https://sdi9b.mi.hdm-stuttgart.de/nextcloud/
- `apt install php-gd php-json php-mysql php-curl`
- `apt install php-intl php-mcrypt php-imagick`
- `apt install php-zip php-xmlwriter php-xmlreader php-xml php-mbstring php-simplexml`
- PHP-Version to 7.2
    - `apt install software-properties-common`
    - `apt install apt-transport-https
wget -q -O- https://packages.sury.org/php/apt.gpg | apt-key add -
echo "deb https://packages.sury.org/php/ stretch main" | tee /etc/apt/sources.list.d/php.list
apt-get update`
    - `apt install php7.2 php7.2-cgi php7.2-cli php7.2-common php7.2-curl php7.2-fpm php7.2-gd php7.2-json php7.2-mbstring php7.2-mysql php7.2-opcache php7.2-readline php7.2-xml php7.2-zip`
    - Remove old PHP version: `apt purge php7.0`
- unip package installieren: `apt install vim unzip`
- in den HTML-Verzeichnis wechseln: `cd /var/www/html`
- NC-daten downloaden: `wget https://download.nextcloud.com/server/releases/latest.zip`
- unpacken: `unzip latest.zip`
- `cd nextcloud`
- in Apache-Sites-available Ordner wechseln: `cd /etc/apache2/sites-available/`
- `wget https://raw.githubusercontent.com/dicenl/nextcloud/master/vhost.conf`
- `mv vhost.conf nextcloud.conf`
- nextcloud.conf anpassen
- create DB
    - login: `mysql -u root -p`
    - `CREATE DATABASE nextcloud;
GRANT ALL ON nextcloud.* to 'nextcloud'@'localhost' IDENTIFIED BY 'db_sdi9b/';
FLUSH PRIVILEGES;
exit`
- `apt-get install python-certbot-apache -y`
- nano /var/www/nc_perm.sh and put code from tutorial
- chmod 750 /var/www/nc_perm.sh
- nano /etc/php/7.4/apache2/php.ini
- apt install php7.4-ldap
- systemctl restart apache2

hilfreichende Links:
- https://www.howtoforge.de/anleitung/wie-man-nextcloud-unter-debian-10-installiert/
- https://www.bitblokes.de/nextcloud-9-auf-ubuntu-server-16-04-installieren/
- https://help.nextcloud.com/t/complete-nc-installation-on-debian-9-stretch-and-manual-update/21881

# Apache web server: 07.12.2020 - 25.01.2021

### Schnipsel:
- `aptitude install apache2` Installation von Apache
- `a2ensite` create symlink to configuration file in /apache2/sites-enable/
- `a2enconf`
- `a2disconf`
- `systemctl reload apache2`
- `systemctl restart apache2`

### Exercises
- [x] **First Steps**
    1. sdi9b.mi.hdm-stuttgart.de -> Apache2 Debian Default Page - It works!
    2. `mv index.html doc.html` -> Server does not find index.html, it observe the whole Directory
    3. index.html in /var/www/html/ überschreiben
    4. `apt-get install apache2-doc`, server path is configured in /etc/apache2/sites-available/000-default.conf and /etc/apache2/apache2.conf
    5. **sdi9b.mi.hdm-stuttgart.de/am180**
        - Create new directory `mkdir /var/www/html/home/sdidoc`
        - new index.html file.
        - Create new Alias Directive in /etc/apache2/apache2.conf    
        - `Alias "/am180" "/var/www/html/home/sdidoc"`
        - `<Directory /var/www/html/home/sdidoc/>
        AllowOverride None
        Require all granted
        </Directory>`
        - `service apache2 restart`
- [x] **Virtual hosts**
    siehe: https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-14-04
    1. Default-Konfigurationsdatei in /etc/apache2/sites-available/ duplizieren und in am180.conf umbennen
    2. Servername hinterlegen: am180.mi.hdm-stuttgart.de
    3. BIND-Konfiguration anpassen, NameServer erweitern: am180.mi.hdm-stuttgart.de.        IN          CNAME       sdi9b.mi.hdm-stuttgart.de.
        - Probleme?!!! 
        - **shell: `nslookup -> am180` muss NS finden!!!**
    4. Netzwerk-Adapter-Einstellungen anpassen, VPN->IpV4->Eigenschaften->Bevorzugte DNS-Server: 141.62.75.123 hinterlegen
    5. `nano /bind9/db.sdi9b` add am180 and manual as A records
    6. create new site-configurations in /apache2/sites-available/am180.conf & manual.conf
    7. generate configurations with `a2ensite manual.conf`, reload apache2:
        - http://am180.mi.hdm-stuttgart.de
        - http://manual.mi.hdm-stuttgart.de
- [x] **SSL / TLS Support**
    1. Enable SSL: `a2enmod ssl`
    2. Create the Root Certificate:
        - `openssl genrsa -des3 -out rootCA.key 2048`, password: sdi9b_Am180
        - `openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.pem`, Fragen beatnworten.
        - Nun sollten wir im Hauptverzeichnis zwei Dateien wiederfinden: rootCA.key und rootCA.pem
    3. Install Root Certificate into your workstation: https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/#installing-root-cert
    4. Create A Certificate: 
        - `openssl genrsa -out device.key 2048`
        - generate the certificate signing request: `openssl req -new -key device.key -out device.csr`, You’ll be asked various questions (Country, State/Province, etc.). Answer them how you see fit. The important question to answer though is common-name.
        - create the certificate: `openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 500 -sha256`
        - now we have three files: device.key (private key), device.csr (certificate signing request) and device.crt (signed certificate).
        - kopiere den private key und signed certificate in die jeweiligen SSL Verzeichnisse:
            - `cp device.crt /etc/ssl/certs/sdi9bam180.crt`
            - `cp device.key /etc/ssl/private/sdi9bam180.key`
    5. Configure Apache, edit /apache2/sites-available/am180.conf and restart apache
- [x] **LDAP authentication**
    `a2enmond ldap`
    `a2enmond authnz_ldap`
    1. in LDAP-Browser navigate to the organizationUnit, in which you want create this testuser.
    2. new Entry -> "inetOrgPerson" : 
    `#!RESULT OK
    #!CONNECTION ldap://sdi9b.mi.hdm-stuttgart.de:389
    #!DATE 2021-01-18T08:24:33.679
    dn: cn=tuser,ou=testing,ou=software,ou=departments,dc=betrayer,dc=com
    changetype: add
    cn: tuser
    sn: Testuser
    objectClass: inetOrgPerson
    objectClass: organizationalPerson
    objectClass: person
    objectClass: top`
    2. "new attribute" -> Typ: userPassword -> Method: SMD5 -> pw: tusdi9b
    `#!RESULT OK
    #!CONNECTION ldap://sdi9b.mi.hdm-stuttgart.de:389
    #!DATE 2021-01-18T08:33:23.103
    dn: cn=tuser,ou=testing,ou=software,ou=departments,dc=betrayer,dc=com
    changetype: modify
    add: userPassword
    userPassword:: e1NNRDV9NXdTZGdPV3JzZXRqRG1sY2cyM0xVN3FJOFloczhCM3A=`
    3. "new attribute" -> uid: tuser
    4. duplicate old ldap-server-connection, rename and reset confs -> authentication: cn=tuser,ou=testing,ou=software,ou=departments,dc=betrayer,dc=com -> pw see at top
    5. open /etc/apache2/apache.conf add to existed directory /var/www
    `AllowOverride None
    AuthName "Private"
    AuthType Basic
    AuthBasicProvider ldap
    AuthLDAPURL ldap://sdi9b.mi.hdm-stuttgart.de/ou=testing,ou=software,ou=departments,dc=betrayer,dc=com?uid
    Require valid-user`
    ?uid -> Argument for user checking
    6. restart apache2 and open http://sdi9b.mi.hdm-stuttgart.de/ in browser
- [x] **Mysql™ database administration**
    1. MySQL installieren: `apt install mysql-server`
    2. MySQL konfigurieren: `mysql_secure_installation`
        - pw: _sdi9b!
        - Dies führt Sie durch eine Reihe von Eingabeaufforderungen, in denen Sie einige Änderungen an den Sicherheitsoptionen Ihrer MySQL-Installation vornehmen können. Die erste Eingabeaufforderung fragt, ob Sie das validierte Passwort-Plugin einrichten möchten, mit dem Sie die Stärke Ihres MySQL-Passworts testen können. Unabhängig von Ihrer Wahl wird die nächste Eingabeaufforderung darin bestehen, ein Passwort für den MySQL-Root-Benutzer festzulegen. Geben Sie ein sicheres Passwort Ihrer Wahl ein und bestätigen Sie es anschließend.
        - Von dort aus können Sie Y und dann ENTER drücken, um die Standardwerte für alle folgenden Fragen zu übernehmen. Dadurch werden einige anonyme Benutzer und die Testdatenbank entfernt, Remote-Root-Logins deaktiviert und diese neuen Regeln geladen, so dass MySQL die von Ihnen vorgenommenen Änderungen sofort berücksichtigt.
        - "All done!  If you've completed all of the above steps, your MariaDB installation should now be secure. Thanks for using MariaDB!"
        - Use mysql server in console with: **mysql**
    3. Installieren von phpMyAdmin: `apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl`
        - Wählen Sie für die Serverauswahl **apache2**
        - Wählen Sie **Yes**, wenn Sie gefragt werden, ob dbconfig-common zum Einrichten der Datenbank verwendet werden soll.
        - Sie werden dann aufgefordert, ein MySQL-Anwendungspasswort für phpMyAdmin auszuwählen und zu bestätigen
        - Der Installationsvorgang fügt die Apache-Konfigurationsdatei phpMyAdmin in das Verzeichnis /etc/apache2/conf-enabled/ ein, wo sie automatisch gelesen wird. Um die Konfiguration von Apache und PHP für die Arbeit mit phpMyAdmin abzuschließen, müssen Sie in diesem Abschnitt des Tutorials nur noch explizit die mbstring-PHP-Erweiterung aktivieren. Geben Sie dazu Folgendes ein:
        - `sudo phpenmod mbstring`
        - Starten Sie anschließend Apache neu, damit Ihre Änderungen erkannt werden `systemctl restart apache2`
        - http://sdi9b.mi.hdm-stuttgart.de/phpmyadmin/
        - `mysql -u root` -> `UPDATE mysql.user SET authentication_string = PASSWORD('sdi_9b') WHERE User = 'root' AND Host = 'localhost';`
        - restart service: `systemctl start mariadb`
- [x] **Providing WEB based user management to your LDAP Server**
    - Install LDAP Account Manager: `apt -y install ldap-account-manager`
    - http://sdi9b.mi.hdm-stuttgart.de/lam -> [LAM configuration] -> Edit server profiles -> Default password is **lam**
    - The first thing to change is Profile Password, this is at the end of General Settings page -> **lSmdi9!**
    - Next is to set LDAP Server address and Tree suffix. 
        - Server address: sdi9b.mi.hdm-stuttgart.de
        - Tree suffix: dc=betrayer,dc=com
        - Security settings: List of valid users: cn=admin,dc=betrayer,dc=com
        - 
- [ ] **Publish your documentation**
    - http://sdi9b.mi.hdm-stuttgart.de/doc/

# LDAP: 16.11.2020 - 06.12.2020
- Verbinden zum HDM LDAP Server war problemlos, ebenso das filtern
- Die Verbindung mit auth erlaubt es einem mehr Attribute einzusehen
- Installation des OpenLDAP servers war einfach, so wie die reconfiguration via slapd
- Server wasnt running after installation and had to be started
- login mit admin pw funktioniert erstmal nicht, da er noch auf die alte db zugreift

### Exercises
- [x] **Browse an existing LDAP Server**
    - DN: uid=am180,ou=userlist,dc=hdm-stuttgart,dc=de
- [x] **Set up an OpenLdap server**
    - `aptitude install dialog`
    - `aptitude install slapd`
    - Passwort eingeben: secret
    - `dpkg-reconfigure slapd`
    - Server-Konfiguration auslassen? -> Nein
    - DNS & NAME DER ORGANISAZION: betrayer.com
    - Falls Fehler auftreten bzw. neue Konfiguration des DNS Domains nicht übernommen werden kann, müssen bestimmte Ordner geleert werden, damit Änderungen wirksam werden (/var/lib/ldap/, /var/backups/slapd...../)
- [x] **Populating your DIT**
    - Authentifizierung in Apache Directory Studio:
        - Bind DN oder Benutzer: cn=admin,dc=betrayer,dc=com
        - slapd-MasterPasswort
- [x] **Testing a bind operation as non - admin user**
    - Authentifizierung in Apache Directory Studio anpassen und den Zugriff mit dem zuletzt angelegten User testen:
        - Bind DN oder Benutzer: uid=**bean**,ou=devel,ou=software,ou=departments,dc=betrayer;dc=com
        - Passwort: bean
- [ ] **Filter based search**
    - All users with a uid attribute value starting with the letter “b”.
        - Suchbasis: ou=devel,ou=software,ou=departments,dc=betrayer,dc=com  -  Filter: (uid=b*)
    - All entries either with either a defined uid attribute or a ou attribute starting with letter “d”.
    - All users entries within the whole DIT having a gidNumber value of 100.
    - All user entries belonging to the billing department having a uid value greater than 1023.
    - All user entries within the whole DIT having a commonName containing the substring “ei”.
    - All user entries within the whole DIT belonging to gidNumber == 100 or having a uid value starting with letter “t”.
- [x] **Extending an existing entry**
    - DN: uid=bean,ou=devel,ou=software,ou=departments,dc=betrayer;dc=com
        - Neue **objectClass* =posixAccount hinzufügen
        - Attribute uidNumber, gidNumber und homeDirectory mit Werten belegen
- [ ] **Accessing LDAP data by a mail client**
- [ ] **LDAP configuration**
- [ ] **LDAP based user login**
- [ ] **Backup and recovery / restore**
- [ ] **Accessing LDAP by a Java™ application**


# DNS: 02.11.2020 - 09.11.2020

### Schnipsel:
- `systemctl restart bind9`

### Exercises
- bind Installation problemlos
- DNS Konfiguration schlecht verständlich, da die Aufgabe nicht ganz Praxisnah ist bzw. an eigenem Server wenig Sinn ergibt
