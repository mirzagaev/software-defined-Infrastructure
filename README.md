Infos:
- Schritte/Prozesse dokumentieren
- Server: **sdi9b.mi.hdm-stuttgart.de**

# Apache web server: 07.12.2020 - 21.12.2020

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
- [ ] **SSL / TLS Support**
- [ ] **LDAP authentication**
- [ ] **Mysql™ database administration**
- [ ] **Providing WEB based user management to your LDAP Server**
- [ ] **Publish your documentation**

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