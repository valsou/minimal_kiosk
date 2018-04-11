# minimal_kiosk
Web page kiosk, using Raspberry Pi with Raspbian. All automated.

## 1. Web page
* Responsive screen !

* Google slide. (iframe with hardcoded size and a ugly black background...)
* Date & Time.
* Weather (now, in 1 hour, tomorrow).

## 2. Init a Raspberry Pi with Raspbian Lite
* Change password for Pi user.
* Avoid Pi user to sudo without asking for a password.
* Install all dependencies needed (Xserver, Chromium, Openbox...).
* Configure Openbox to autostart chromium with parameters via Xserver.
* Create a autostart bash in pi directory to start Xserver at boot.

* To do :

* Power off the display every night at 7PM and power on every day at 7AM.
* Update weather/hour/slide only during the day of school (i.e. 7AM to 7PM).
* Improve design.
* Reboot the RPi every day, or once a week. -> maybe not useful.
