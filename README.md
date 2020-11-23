Infos:
- Schritte/Prozesse dokumentieren
- Server: **sdi9b.mi.hdm-stuttgart.de**


# DNS: 02.11.2020 - 09.11.2020
- bind Installation problemlos
- DNS Konfiguration schlecht verständlich, da die Aufgabe nicht ganz Praxisnah ist bzw. an eigenem Server wenig Sinn ergibt

# LDAP: 16.11.2020 - 23.11.2020
- LDAP vorstellen
- Verbinden zum HDM LDAP Server war problemlos, ebenso das filtern
- Die Verbindung mit auth erlaubt es einem mehr Attribute einzusehen
- Installation des OpenLDAP servers war einfach, so wie die reconfiguration via slapd
- Server wasnt running after installation and had to be started
- login mit admin pw funktioniert erstmal nicht, da er noch auf die alte db zugreift
- mussten slapd purgen, da noch alte daten ausgelesen wurden

### Exercises
- [x] Browse an existing LDAP Server
    - DN: uid=am180,ou=userlist,dc=hdm-stuttgart,dc=de
- [x] Set up an OpenLdap server
    - `aptitude install dialog`
    - `aptitude install slapd`
    - Passwort eingeben: secret
    - `dpkg-reconfigure slapd`
    - Server-Konfiguration auslassen? -> Nein
    - DNS betrayer.com
    - NAME DER ORGANISAZION betrayer.com
- [ ] Populating your DIT
- [ ] Testing a bind operation as non - admin user
- [ ] Filter based search
- [ ] Extending an existing entry
- [ ] Accessing LDAP data by a mail client
- [ ] LDAP configuration
- [ ] LDAP based user login
- [ ] Backup and recovery / restore
- [ ] Accessing LDAP by a Java™ application