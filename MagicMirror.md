# Update
Generelle Anleitung: https://www.howtoforge.com/how-to-install-python-on-debian-11/

## Install dev packages 
```
apt install libssl-dev libffi-dev
```
## install Python
```
wget https://www.python.org/ftp/python/3.9.9/Python-3.9.9.tgz
tar xzf Python-3.9.9.tgz
cd Python-3.9.9
./configure --enable-optimizations
sudo make install
```
## Install display Driver
```
git clone https://github.com/GregDMeyer/IT8951.git
cd IT8951
sudo pip3.9 install -r requirements.txt
sudo pip3.9 install ./
sudo pip3.9 install six
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
