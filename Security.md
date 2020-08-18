# Security
## GoAccess

```bash
echo "deb http://deb.goaccess.io/ $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/goaccess.list
wget -O - https://deb.goaccess.io/gnugpg.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/goaccess.gpg add -
apt-get update
apt-get install goaccess
mkdir /opt/goaccess_db
```
/etc/goaccess/goaccess.conf
```conf
log-format COMBINED
exclude-ip 192.168.2.1
output /var/www/nextcloud/report.html
geoip-database /usr/share/GeoIP/GeoLite2-City.mmdb
db-path /opt/goaccess_db
persist true
restore true
```
crontab -e
```cron
  *  *  *   *   *    goaccess /var/log/apache2/*_access.log 2>1 1>/dev/null
```
## Fail2Ban
## Apache
/etc/apache2/conf-available/security.conf
```conf
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```
### Letsencrypt