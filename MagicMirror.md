# Update
Generelle Anleitung: https://www.howtoforge.com/how-to-install-python-on-debian-11/

## Install dev packages 
```
apt install libssl-dev libffi-dev git python3-pip
```
## Install display Driver
```
git clone https://github.com/GregDMeyer/IT8951.git
cd IT8951
sudo pip install -r requirements.txt
sudo pip install ./
sudo pip install pillow
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
    imgkit.from_url('https://banking.janf.name/magicmirror.php', 'screenshot.png', options=options)
    image = Image.open('screenshot.png')
    #image = ImageGrab.grab()

    display = AutoEPDDisplay(vcom=-1.78)
    display.frame_buf.paste(image)
    display.draw_full(constants.DisplayModes.GC16)
    #image.save("screenshot.png")


if __name__ == '__main__':
    main()

```
