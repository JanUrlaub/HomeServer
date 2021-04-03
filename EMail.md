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
