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

## Apache Webserver
[Per Subdomain](https://community.home-assistant.io/t/reverse-proxy-with-apache/196942)


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

