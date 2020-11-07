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
