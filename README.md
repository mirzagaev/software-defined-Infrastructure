Infos:
- Schritte/Prozesse dokumentieren
- Server: **sdi9b.mi.hdm-stuttgart.de**

# LDAP: 16.11.2020 - 30.11.2020
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
        - 
    - All users entries within the whole DIT having a gidNumber value of 100.
    - All user entries belonging to the billing department having a uid value greater than 1023.
    - All user entries within the whole DIT having a commonName containing the substring “ei”.
    - All user entries within the whole DIT belonging to gidNumber == 100 or having a uid value starting with letter “t”.
- [ ] **Extending an existing entry**
- [ ] **Accessing LDAP data by a mail client**
- [ ] **LDAP configuration**
- [ ] **LDAP based user login**
- [ ] **Backup and recovery / restore**
- [ ] **Accessing LDAP by a Java™ application**


# DNS: 02.11.2020 - 09.11.2020
- bind Installation problemlos
- DNS Konfiguration schlecht verständlich, da die Aufgabe nicht ganz Praxisnah ist bzw. an eigenem Server wenig Sinn ergibt