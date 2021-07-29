# Home Assistant
Dokumentation für Home Assistent Server

## grundsätzliche Einrichtung
[Home Assistent itself](https://wiki.instar.de/Software/Linux/Home_Assistant/)
```bash
apt install python3-pip python3-venv bluez
```  
Veraltet  
```bash
apt install python3-dev libmariadbclient-dev sqlite3 libglib2.0-dev libpcre3=2:8.39-12 # was an issue on my server
```
Einstweilen muss Python 3.8 manuell eingerichtet werden.  
[How to Install Python 3.8 on Debian 10](https://linuxize.com/post/how-to-install-python-3-8-on-debian-10/)  

Danach Python Umgebung einrichten. venv bei update überspringen.
```bash
sudo su -s /bin/bash homeassistant
python3.8 -m venv /opt/homeassistant
source /opt/homeassistant/bin/activate
pip3 install --upgrade homeassistant mysqlclient python-eq3bt
```

## Heizungsregler
[Bluetooth Heizungsregler Integration](https://www.home-assistant.io/integrations/eq3btsmart/)  
[Bluetooth Heizungsregler PIP](https://github.com/rytilahti/python-eq3bt)  
```bash
hcitool lescan # get bluetooth devices
eq3cli --mac 00:00:00:00:00:00 locked --target false
eq3cli --mac 00:00:00:00:00:00 locked --target true
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
Dabei kann man auch gleich den Dokumentationseintrag korrgieren, weswegen der Start grundesätzlich noch ein Warning ausspuckt.  
Das -d muss entfernt werden.
```service
Documentation=man:bluetoothd (8)
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
Die /opt/bluetooth in ExeStart  
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
nach Erstellen der Datei  
```bash
chmod +x /opt/bluetooth
systemctl enable bt.service
systemctl start bt.service
```
Und die Config aus config/configuration.yaml
```yaml
climate:
  - platform: eq3btsmart
    devices:
      kitchen:
        mac: '00:1A:22:0F:04:9F'
      office:
        mac: '00:1A:22:11:5B:25'
      living:
        mac: '00:1A:22:11:CD:EA'  
      bedroom:
        mac: '00:1A:22:0F:06:40'
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

## Grafana
https://grafana.com/docs/grafana/latest/installation/debian/

