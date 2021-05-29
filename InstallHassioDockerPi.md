# mögliche Systeme
Hypriot OS: kein WLAN  
Raspbery OS: nur 32 Bit  
Ubuntu Core: fehlende Schreibrechte bei supervisor Installation  
Debian: kein WLAN  
Ubuntu Server: Groß, mit vielen Funktionen, Aber auch schwergewichtig  

# Allgemeiner Ablauf der Installation
Ubuntu ARM 64Bit installieren (ARM8/Raspi3)  
Docker Installieren  
Supervisor installieren  

# Ubuntu auf Stick
Einfach nach Raspberry Ubuntu Googeln und herunterladen  
# Weiter mit Bash auf dem Pi Server
```bash
# Erstmal Updaten ;) jq ist für supervisor - 
sudo apt update
sudo apt upgrade
sudo apt install docker.io

# Dann Docker installieren
sudo docker volume create portainer_data
sudo docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```
# Dann supervisor Installieren

## Skript
```bash
sudo apt install jq
wget https://raw.githubusercontent.com/home-assistant/supervised-installer/master/installer.sh  
# Entfernen aller vorkommen von network-manager und NetworkManager
nano installer.sh  
sudo bash installer.sh --machine raspberrypi3
# Warten bis Home Assistent vollständig gestartet ist
sudo service hassio-supervisor restart
sudo service hassio-apparmor restart
```
# Home Assistant
config/configuration.yaml
```yaml
climate:
  - platform: eq3btsmart
    devices:
      kitchen:
        mac: '00:00:00:00:00:00'

```

# Hinweise

## Raspberry Einrichtung
Tools
```bash
apt install raspi-config mc
```
Bei Fehlern der network-config einfach Clean up der Config mit: 
```bash
cloud-init clean -r  
```
Um den Raspberry einstellen zu können, gibt es ein Config Tool. Dies wird vor allem für den Raspi 4 interessant.  
```bash
sudo raspi-config
```
## Netzwerk bei PI einstellen
Siehe https://linuxize.com/post/how-to-configure-static-ip-address-on-ubuntu-18-04/  
```conf
version: 2
ethernets:
  eth0:
    dhcp4: true
    optional: true
wifis:
  wlan0:
    dhcp4: no
    optional: true
    access-points:
      "WLAN-QERF9P 5G":
        password: "3839020377131378"
    addresses:
      - 192.168.2.3/24
    gateway4: 192.168.2.1
    nameservers:
      addresses: [192.168.2.1]
```
