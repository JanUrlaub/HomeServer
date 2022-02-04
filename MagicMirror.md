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
apt install xserver-xorg-video-dummy
```
/usr/share/X11/xorg.conf.d/20-dummy.conf 
```
Section "Monitor"
  Identifier "Monitor0"
  HorizSync 28.0-80.0
  VertRefresh 48.0-75.0
  # https://arachnoid.com/modelines/
  Modeline "1200x825_60.00" 81.16 1200 1264 1392 1584 825 826 829 854 -HSync +Vsync
EndSection
Section "Device"
  Identifier "Card0"
  Driver "dummy"
  VideoRam 256000
EndSection
Section "Screen"
  DefaultDepth 24
  Identifier "Screen0"
  Device "Card0"
  Monitor "Monitor0"
  SubSection "Display"
    Depth 24
    Modes "1200x825_60.00"
  EndSubSection
EndSection
```

## main.py
```
from time import sleep
from PIL import Image, ImageDraw, ImageFont, ImageGrab
from sys import path
from IT8951 import constants
from IT8951.display import AutoEPDDisplay
import imgkit



EPD_WIDTH = 1200
EPD_HEIGHT = 825


def main():
    options = {'width': EPD_WIDTH, 'height': EPD_HEIGHT}
    # https://pypi.org/project/imgkit/
    imgkit.from_url('https://ampache.janf.name/magicmirror.htm', 'screenshot.png', options=options)
    image = Image.open('screenshot.png')
    #image = ImageGrab.grab()

    display = AutoEPDDisplay(vcom=-1.78)
    display.frame_buf.paste(image)
    display.draw_full(constants.DisplayModes.GC16)
    #image.save("screenshot.png")


if __name__ == '__main__':
    main()

```
