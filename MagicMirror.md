# Update
## Installl NPM
```
curl -sL https://deb.nodesource.com/setup_14.x | sudo bash -
sudo npm install -g npm
sudo npm install -g n
```
## Update NPM
```
sudo npm install -g npm
sudo npm install -g n
```
## Update Magic Mirror
```
cd ~/MagicMirror
git reset --hard
git pull && npm install
~/node_modules/pm2/bin/pm2 restart mm
```
## Setup autostart
```
~/node_modules/pm2/bin/pm2 start mm.sh
```
## Installation eines imaginären Displays
```
xserver-xorg-video-dummy
```
