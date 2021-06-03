# Server
## Cloud Server
/etc/apache2/sites-available/nextcloud.conf
```conf
<VirtualHost *:443>
	ServerName xxxxxx
	ServerAlias imap.xxxxxx
	ServerAlias imaps.xxxxxx
	ServerAlias mail.xxxxxx
	ServerAlias smtp.xxxxxx
	ServerAlias smtpauths.xxxxxx

	DocumentRoot /var/www/nextcloud

	CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined
	ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
	
	<Directory /var/www/nextcloud/>
	 	Require all granted
		AllowOverride All
		Options FollowSymLinks MultiViews
		<IfModule mod_dav.c>
		Dav off
		</IfModule>
	</Directory>

	Include /etc/letsencrypt/options-ssl-apache.conf
	SSLCertificateFile /etc/letsencrypt/live/xxxxxx/fullchain.pem
	SSLCertificateKeyFile /etc/letsencrypt/live/xxxxxx/privkey.pem
</VirtualHost>
```
## Mail Server
[E-Mail Server](EMail.md)

## Musik Server
/etc/apache2/sites-available/fampache.config
```conf
<VirtualHost *:443>
	ServerName ampache.xxxxxx

    CustomLog ${APACHE_LOG_DIR}/ampache_access.log combined
	ErrorLog  ${APACHE_LOG_DIR}/ampache_error.log

	DocumentRoot /var/www/ampache

	<Directory /var/www/ampache>
       	AllowOverride All
	</Directory>

	Include /etc/letsencrypt/options-ssl-apache.conf
	SSLCertificateFile /etc/letsencrypt/live/xxxxxx/fullchain.pem
	SSLCertificateKeyFile /etc/letsencrypt/live/xxxxxx/privkey.pem
</VirtualHost>
```
## Media Server
### /etc/apt/sources.list.d/multimedia.list
```list
deb http://debian-mirrors.sdinet.de/deb-multimedia stable main
deb-src http://debian-mirrors.sdinet.de/deb-multimedia stable main
```
## Smart Home
[Home Assistant](HomeAssistant.md)
## Security Concepts
[Security](Security.md)

## WLAN
```bash
apt install wpasupplicant
wpa_passphrase 'Netzwerkname' Passwort
nano /etc/network/interfaces
```
Den Netzwerkname OHNE '
```conf
iface wlp58s0 inet dhcp
        wpa-ssid Netzwerkname
        wpa-psk Ergebnis aus wpa_passphrase
```

## Kodi
Kodi läuft als standalone Service und benutzt nur das Minimalsystem von X11 zur grafischen Darstellung.  
Leider habe ich die damals gefundene Doku nicht gespeichert; die anderen Hilfen sind gerne recht komplex und funktionieren am Ende nicht mal.  
Ein scheinbar aktuelles Git dazu https://github.com/graysky2/kodi-standalone-service  
```bash
apt install kodi kodi-standalone
```
Mein Systemd Eintrag. todo wechsel auf wayland.  
/etc/systemd/system/kodi/service
```service
[Unit]
Description=Kodi standalone (X11)
After=systemd-user-sessions.service network-online.target sound.target mysqld.service
Requires=network-online.target
Conflicts=getty@tty1.service

[Service]
User=kodi
Group=kodi
PAMName=login
TTYPath=/dev/tty1
ExecStart=/usr/bin/xinit /usr/bin/kodi-standalone -- :0 -nolisten tcp vt1
Restart=on-abort
StandardInput=tty

[Install]
WantedBy=graphical.target
```
Konfiguraationsdateien liegen uznter ~/.kodi des Kodi Benutzers (siehe passwd).

## Borg Backup
Listed die Größe pro Archiv auf. Bitte REPOSITORY_DIR mit dem Pfad zum Repo ersetzen.
```bash
printf 'Archive\t\t\t\t\tOrig\tComp\tDedup\n'; printf '%-32.32s\t%s\t%s\t%s\n' $(borg info --json --sort-by name --glob-archives '*' REPOSITORY_DIR | jq '.archives[] | "\(.name) \(.stats.original_size) \(.stats.compressed_size) \(.stats.deduplicated_size)"' | sed --expression='s/^"//;s/"$//' | numfmt --field='2-4' --to=iec)
```

# Raspi
[Raspi Server](InstallHassioDockerPi.md)

# Client
## Nextcloud
## SIP Telefonie
[Telekom - grundsätzliche Einstellungen](https://www.telekom.de/hilfe/festnetz-internet-tv/ip-basierter-anschluss/einstellungen-fuer-die-ip-telefonie-mit-anderen-clients?samChecked=true)

### Allgemeine Einstellungen

SIP-ID/Benutzer: Ihre Telefonnummer
Bildschirmname (falls vorhanden): Ihre Telefonnummer
Authentifizierungsname/Benutzername: Ihre E-Mail-Adresse, z. B. ihr-name@t-online.de
Passwort: Ihr Passwort
SIP-Proxy: tel.t-online.de
Registrar: tel.t-online.de
Realm: tel.t-online.de
STUN-Server: stun.t-online.de
Outbound-Proxy: leer lassen oder ebenfalls tel.t-online.de

Hinweise:
  -  Geben Sie die SIP-ID ohne Leerzeichen und Sonderzeichen ein (entspricht Vorwahl und Rufnummer)
  -  Die Eingabe der SIP-ID und des Bildschirmnamens müssen übereinstimmen.
  -  Den Benutzernamen bitte vollständig klein schreiben.

