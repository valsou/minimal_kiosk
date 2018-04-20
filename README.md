# Minimal kiosk

## 1. Prerequisites
* Download Raspbian Lite in its latest version (in 2018 : Stretch).
https://www.raspberrypi.org/downloads/raspbian/

* Download Etcher.
https://etcher.io/

## 2. Setup SD Card
Burn Raspbian Lite .iso with Etcher on your SD Card.
Insert the SD Card in your Raspberry Pi.
Boot.

## 3. Configure Raspbian Lite
### Verify Internet connection
Verify that the internet connection is OK.

### Initialization
Type `sudo raspi-config`.

#### Change keyboard layout
It will be easier to type the commands.

Choose `Localisation Options`.

Choose `Keyboard Layout`.

Continue the setup process while selecting your desired keyboard layout.

In my case : Azerty layout with French keyboard.

#### Change timezone
It will be necessary to have a reliable date/time on your screen.

Choose `Localisation Options`.

Choose `Timezone`.

Select your Country/City.

In my case : Europe/Paris.

### Update the Pi

Type `sudo apt-get update && sudo apt-get upgrade -y`.

### Create a new user
For security reason we will create a new user and delete `pi`user.

#### Create new user
We will choose as username : `mounier`.

##### Add new user

Type `sudo adduser mounier`.

Type a strong password for this user.

##### Give user sudo permissions

Type `sudo adduser mounier sudo`.

##### Delete pi user

Type `exit` to logout pi user.

Type the credentials for your new user, `mounier` in this example.

Then, type `sudo deluser remove-home pi`.

#### Extra security
As another security, be sure each time sudo is used a password is prompted.

Type `sudo nano /etc/sudoers.d/010_pi-nopasswd`.

Edit the document to the following :
```
pi ALL=(ALL) PASSWD: ALL
```

### Auto login our new user
Type `sudo raspi-config`.

Select `Boot options` then `Console/Autologin` (second choice).

Reboot typing `reboot`.

`mounier` user will be auto-logged. Each time sudo command is used, a password will be prompted.

## 4. Configure Kiosk functionnality

### Install & configure Kiosk utilites

#### Import the web page
Minimal kiosk comes with a .html page all written in HTML/CSS/JavaScript. It shows :
* Date & time
* Weather (thanks to the Meteorologisk Institutt of Norway https://api.met.no/)
* a Google Slide

You can edit the JavaScript constants in `index.html`.
Replace this part :
```
const SLIDE_URL = 'http://you-url';
const LAT = latitude;
const LON = longitude;
const ELE = elevation;
```

The document is not exempt of bugs and is badly internationalized. Please, open issues to propose me solutions.

---
You have two methods :

##### Static
Mount your SD Card on your computer. Be sure that you can see `ext4` format drive.

In the folder `home/mounier/` create a folder named `minimal_kiosk` and copy/paste the page and its dependances.

##### Dynamic (prefrence)
Install `git`.
Type `sudo apt-get install git`.

Type `cd /home/mounier`.

Type `git pull https://github.com/valsou/minimal_kiosk`.

Type `cd minimal_kiosk`.

Type `git update-index --assume-unchanged config.js`.

#### Minimal environment
Type `sudo apt-get install --no-install-recommends xserver-xorg x11-xserver-utils xinit openbox`.

#### Browser (Chromium)
Type `sudo apt-get install --no-install-recommends chromium-browser`.

#### Configure Openbox
Type `sudo nano /etc/xdg/openbox/autostart` and edit the document as following :

```
# Disable any form of screen saver / screen blanking / power management
xset s off
xset s noblank
xset -dpms

# Allow quitting the X server with CTRL-ATL-Backspace
setxkbmap -option terminate:ctrl_alt_bksp

# Start Chromium in kiosk mode
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' ~/.config/chromium/'Local State'
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/; s/"exit_type":"[^"]\+"/"exit_type":"Normal"/' ~/.config/chromium/Default/Preferences
chromium-browser --disable-infobars --kiosk 'http://your-url-here'
```

In our case the URL will be : `/home/mounier/minimal_kiosk/index.html`.

#### Try it !
Try to launch the web page.
Type `startx -- -nocursor`.

A GUI environment is launched and Chromium should open the web page.

To exit the Kiosk press on your keyboard `CTRL`+`ALT`+`BACK SPACE`.

If `startx` returned an error, try this :

Type `sudo rm /home/mounier/.Xauthority`.

And retry !

#### Open Kiosk automatically on Boot
Type `nano /home/mounier/.bash_profile` and edit the document as following :

```
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && startx -- -nocursor
```

If you have chosen to import the kiosk page dynamically, edit the document as following :

```
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && cd /home/mounier/minimal_kiosk && git reset --hard HEAD && git pull && startx -- -nocursor
```

This way, each time Kiosk is launched, the web page is updated.

### Automation (cron)
We can automate :
* the screen extinction, to save energy.
* the browser/GUI environment extinction, to save bandwith
* updates of the web page from Github.com

We edit the cron file of our user.
Type `crontab -e`.

Edit the document as following :

```
00 7 * * * vcgencmd display_power 1
00 19 * * * vcgencmd display_power 0
00 19 * * * killall xinit
50 6 * * * pkill -KILL -u mounier
```

In the order :
* Every day at 07:00 AM -> power on the screen
* Every day at 07:00 PM -> power off the screen
* Every day at 07:00 PM -> shutdown GUI environment/chromium
* Every day at 6:50 AM -> open GUI environment/chromium
