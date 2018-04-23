# Minimal kiosk

![Screenshot example](preview.png)

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

### (Optional) Configure Proxy

Type `cd /etc/apt/apt.conf.d`.

Create a file named `10proxy`typing `sudo nano 10proxy`.

Fill the document with :

```
Acquire::http::Proxy "http://proxyaddress:proxyport/";
Acquire::https::Proxy "http://proxyaddress:proxyport/";
```

Then, type `sudo nano /etc/environment`.

Fille the document with :

```
export http_proxy="http://proxyaddress:port/"
export https_proxy="http://proxyaddress:port/"
```

### Update the Pi

Type `sudo apt-get update && sudo apt-get upgrade -y`.

### Create a new user
For security reason we will create a new user and delete `pi`user.

#### Create new user
We will choose as username : `mounier`.

##### Add new user

Type `sudo adduser mounier`.

Type a strong password for this user.

##### Give user privileges

Type `sudo adduser mounier sudo`.

Type `sudo usermod -a -G video mounier`.

##### Delete pi user

Type `exit` to logout pi user.

Type the credentials for your new user, `mounier` in this example.

Then, type `sudo deluser --remove-home pi`.

#### Extra security
As another security, be sure each time sudo is used a password is prompted.

Type `sudo nano /etc/sudoers.d/010_pi-nopasswd`.

Edit the document to the following :
```
mounier ALL=(ALL) PASSWD: ALL
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

You can edit the JavaScript constants in `js/config.js`.
Don't forget to copy `config.sample.js` to `config.js` with your own configuration.

The document is not exempt of bugs and is badly internationalized. Please, open issues to propose me solutions.

---
You have two methods :

##### Static
Mount your SD Card on your computer. Be sure that you can see `ext4` format drive.

In the folder `home/mounier/` create a folder named `minimal_kiosk` and copy/paste the page and its dependances.

##### Dynamic (preference)
Install `git`.
Type `sudo apt-get install git`.

Type `cd /home/mounier`.

Type `git clone https://github.com/valsou/minimal_kiosk`.

Type : `cd minimal_kiosk`.

Let's create a .gitignore to ignore our configuration file.

```
cp js/config.sample.js js/config.js
nano .gitignore
```

Insert `js/config.js` and save the file.

Now you can edit your own configuration file.
Each update, the `config.sample.js` file may be updated, but not yours.

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

If `startx` shows an error, try to delete `.Xauthority` file and reboot.

Type `sudo rm -R /home/mounier/.Xauthority`.

Type `reboot`.

#### Open Kiosk automatically on Boot
Type `nano /home/mounier/.bash_profile` and edit the document as following :

```
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && startx -- -nocursor
```

If you have chosen to import the kiosk page dynamically, edit the document as following :

```
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && cd /home/mounier/minimal_kiosk && git pull && startx -- -nocursor
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
0 7 * * * vcgencmd display_power 1
0 19 * * * vcgencmd display_power 0
0 19 * * * killall xinit
50 6 * * * pkill -KILL -u mounier
0 0 * * * cd /home/mounier/minimal_kiosk && git pull
```

In the order :
* Every day at 07:00 AM -> power on the screen
* Every day at 07:00 PM -> power off the screen
* Every day at 07:00 PM -> shutdown GUI environment/chromium
* Every day at 6:50 AM -> open GUI environment/chromium
* Every day at midnight update git repo
