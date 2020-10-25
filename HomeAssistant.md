# Home Assistant
Dokumentation für Home Assistent Server

## grundsätzliche Einrichtung
[Home Assistent itself](https://wiki.instar.de/Software/Linux/Home_Assistant/)
## Heizungsregler
[Bluetooth Heizungsregler Integration](https://www.home-assistant.io/integrations/eq3btsmart/)  
[Bluetooth Heizungsregler PIP](https://github.com/rytilahti/python-eq3bt)  
```bash
apt install libpcre3=2:8.39-12 # was an issue on my server
apt install libglib2.0-dev bluez
hcitool lescan # get bluetooth devices
su -s /bin/bash homeassistant
python3 -m venv /opt/homeassistant
pip3 install python-eq3bt
/opt/homeassistant/.local/bin/eq3cli --mac 00:00:00:00:00:00 locked --target false
/opt/homeassistant/.local/bin/eq3cli --mac 00:00:00:00:00:00 locked --target true
```
Aus unerfindlichen Gründen stürzt das Bluetooth Modul neuerdings (2020-08-19) intern ab.  
Der Service läuft, es werden aber keine Dienste mehr erkannt.  
Der Fix sieht wie folgt aus:  
```bash
nano /lib/systemd/system/bluetooth.service
```
Problem ist das SIM-Access-Profile, das die Meldung "Operation not permitted" verursacht.  
```service
ExecStart=/usr/lib/bluetooth/bluetoothd --noplugin=sap
```
```bash
systemctl daemon-reload
```
Auch ist ein Problem, dass der Bluetooth-Adapter regelmäßig nicht mehr erreichbar ist.
Er geht einfach in den suspend Modus.
Zum deaktiveren
```conf
#/etc/systemd/system/bt.service
[Unit]
Description=Disable autosuspend for bluetooth devices
Requires=bluetooth.target
After=bluetooth.target

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/opt/bluetooth
NotifyAccess=main

[Install]
WantedBy=multi-user.target
```

```bash
#!/bin/bash
#/opt/bluetooth

# Prevents the Bluetooth USB card from getting reset which disconnects the mouse
BTUSB_DEV="8087:0a2b"
BTUSB_BINDING="$(lsusb -d "$BTUSB_DEV" |
    cut -f 1 -d : |
    sed -e 's,Bus ,,' -e 's, Device ,/,' |
    xargs -I {} udevadm info -q path -n /dev/bus/usb/{} |
    xargs basename)"

echo "Disabling autosuspend for Bluetooth USB: $BTUSB_BINDING..."
echo -1 > "/sys/bus/usb/devices/$BTUSB_BINDING/power/autosuspend_delay_ms"
```
```bash
chmod +x /opt/bluetooth
systemctl enable bt.service
systemctl start bt.service
```

## Apache Webserver
[Per Subdomain](https://community.home-assistant.io/t/reverse-proxy-with-apache/196942)  
/etc/apache2/sites-available/homeassitent.conf
```conf
<VirtualHost *:443>
    ServerName xxxxxx
    ProxyPreserveHost On
    ProxyRequests off
    ProxyPass /api/websocket ws://localhost:8123/api/websocket
    ProxyPassReverse /api/websocket ws://localhost:8123/api/websocket
    ProxyPass / http://localhost:8123/
    ProxyPassReverse / http://localhost:8123/

    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} =websocket [NC]
    RewriteRule /(.*)  ws://localhost:8123/$1 [P,L]
    RewriteCond %{HTTP:Upgrade} !=websocket [NC]
    RewriteRule /(.*)  http://localhost:8123/$1 [P,L]

    SSLCertificateFile /etc/letsencrypt/live/xxxxxx/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/xxxxxx/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf

    CustomLog ${APACHE_LOG_DIR}/home_access.log combined
	ErrorLog ${APACHE_LOG_DIR}/home_error.log
</VirtualHost>
```

## Kodi 
[Anleitung Home Assitent Community](https://www.home-assistant.io/integrations/kodi/)

## mysql
```bash
apt install libmariadbclient-dev sqlite3
su -s /bin/bash homeassistant
python3 -m venv /opt/homeassistant
pip3 install mysqlclient --upgrade
```

## Grafana
https://grafana.com/docs/grafana/latest/installation/debian/

