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
### /etc/apt/sources.list.d/rspamd.list
```list
deb http://rspamd.com/apt-stable/ buster main
deb-src http://rspamd.com/apt-stable/ buster main
```
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