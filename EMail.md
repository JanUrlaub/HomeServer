# /etc/apt/sources.list.d/rspamd.list
```list
deb http://rspamd.com/apt-stable/ buster main
deb-src http://rspamd.com/apt-stable/ buster main
```

# Installation
[Anleitung](MailServer.pdf)

# Filter einstellen
Nur manuell möglich (aktuell).  
Datei „spam-global.sieve“ im Ordner /var/vmail/sieve/global  
Nach Änderung Kompilierung nötig!: sievec /var/vmail/sieve/global  
Neustart vopn dovecot: service dovecot restart  

# lokale Status-Mails
Für die internen Mails von Linux habe ich alle eiträge auf root umgestellt und root wiederum auf meine E-Mail Adresse.
ACHTNUG: danach newaliases ausführen
```bash
postmaster: root
www-data: root
root: mail@adresse.at
```
# SMTP Relay
Mit SendInBlue kann man eine statische IP erhalten. Da diverse Spam Filter allein aufgrund der offiziell dynamischen IP die Mail sperren, ist dies nötig.  
Unter https://account.sendinblue.com/advanced/api Reiter SMTP können die Zugangsdaten eingesehen werden.
```config
### Sendinblue ###
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = static:[user]:[password]
smtp_sasl_security_options = noanonymous
smtp_tls_security_level = may
header_size_limit = 4096000
relayhost = smtp-relay.sendinblue.com:587
```
