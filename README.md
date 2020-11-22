Infos:
- Schritte/Prozesse dokumentieren

# 02.11.2020
- bind Installation problemlos
- DNS Konfiguration schlecht verständlich, da die Aufgabe nicht ganz Praxisnah ist bzw. an eigenem Server wenig Sinn ergibt

# 09.11.2020
- Wiederholungssession für bind

# 16.11.2020
- LDAP vorstellen
- Verbinden zum HDM LDAP Server war problemlos, ebenso das filtern
- Die Verbindung mit auth erlaubt es einem mehr Attribute einzusehen
- Installation des OpenLDAP servers war einfach, so wie die reconfiguration via slapd
- Server wasnt running after installation and had to be started
- login mit admin pw funktioniert erstmal nicht, da er noch auf die alte db zugreift

Browse an existing LDAP Server

- [ ] Reading data from an existing server requires a suitable client software. We recommend using Apache Directory Studio.
- [ ] Setup Apache Directory Studio to anonymously connect to ldap1.hdm-stuttgart.de using TLS. Depending on your location this may require VPN.
- [ ] Browse the DIT.
- [ ] Use a filter like (uid=xy234) to find your personal entry beneath ou=userlist,dc=hdm-stuttgart,dc=de. Use the corresponding DN e.g. uid=xy234, ou=userlist,dc=hdm-stuttgart,dc=de to reconnect using password authentication. Then browse your own entry again. Can you spot any difference?
- [ ] Repeat the previous steps by using the command line ldapsearch utility.