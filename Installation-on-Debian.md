

<a href="https://photoboothproject.github.io" class="button hidden">Home</a>
<a href="https://photoboothproject.github.io/Changelog" class="button hidden">Changelog</a>
<a href="https://photoboothproject.github.io/INSTALL" class="button hidden">Install</a>
<a href="https://photoboothproject.github.io/FAQ_MENU" class="button hidden">FAQ</a>
<a href="https://photoboothproject.github.io/DONATION" class="button hidden">Donate</a>

---

## Automated installation on Raspberry Pi OS (previously called Raspbian) and on Debian / Debian based distributions:

To make the installation as simple as possible, we have created an installation script for you. It will setup your Raspberry Pi, Computer or Laptop as a full blown Photobooth (using Apache Webserver). This means, Photobooth and all needed packages and dependencies get installed and the automatic camera mount is disabled. On a Raspberry Pi you can choose that Photobooth is started in fullscreen on startup.

If you encounter any issues or want more freedom to configure your Pi, Computer or Laptop, we recommend you look at the detailed installation instruction below.

The installation script is intendet to work on Raspberry Pi OS based on Debian bullseye (released October 2021), but it should also work on Raspberry Pi OS based on Debian buster.

**Note:** If you are using an older version of Rasperry Pi OS or Debian / Debian based distribution Node.js v12.22.x will be installed if needed!

A valid username is needed to run the installer. Your OS username must be passed by the `-username` flag to the installer.
```sh
wget https://raw.githubusercontent.com/PhotoboothProject/photobooth/dev/install-photobooth.sh
sudo bash install-photobooth.sh -username='<YourUsername>'
```

For the user "pi", the command to install Photobooth needs to be:
```sh
sudo bash install-photobooth.sh -username='pi'
```

By default Apache is used for an easy and no-hassle setup as NGINX and Lighttpd need some additional steps.
To use NGINX run
```sh
sudo bash install-photobooth.sh -username='<YourUsername>' -webserver='nginx'
```

(additional Setup note: [Cromakeying is saving without finishing saving](FAQ#cromakeying-is-saving-without-finishing-saving) ).

To use Lighttpd as Webserver run
```sh
sudo bash install-photobooth.sh -username='<YourUsername>' -webserver='lighttpd'
```

To get to know all options you can simply run `sudo bash install-photobooth.sh -help`.


## Manually install Photobooth on Raspberry Pi OS (previously called Raspbian) and on Debian / Debian based distributions:

The steps below were tested on "Raspberry Pi OS with desktop" based on Debian Buster, but should work for Debian and all Debian based distributions. Photobooth can also be used on any other PC/Laptop running a supported OS.

### Update your system

```sh
sudo apt update
sudo apt dist-upgrade
```

### Install a Webserver
Currently NGINX, Lighttpd and Apache Webserver are supported.
For an easy and no-hassle setup you should install Apache Webserver.
NGINX has a smaller memory footprint and typically better performance, which is especially important on the Raspberry Pis, but it needs some additional steps until you're good to go. Also Lighttpd needs some additional steps.


#### Install Apache & PHP

```sh
sudo apt install -y libapache2-mod-php
```

#### or Install NGINX & PHP

```sh
sudo apt install -y nginx php-fpm
```
[Additional needed steps to enable PHP in NGINX](Installation-on-Debian-nginx)


#### or Install Lighttpd & PHP

```sh
sudo apt install -y lighttpd php-fpm
```

[Additional needed steps to enable PHP in Lighttpd](Installation-on-Debian-lighttpd)


### Install dependencies

```sh
sudo apt install -y git php-gd php-zip gphoto2 libimage-exiftool-perl nodejs rsync udisks2
```

To install all client dependencies you also have to [install yarn](https://yarnpkg.com/lang/en/docs/install/#debian-stable):
```sh
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install -y yarn
```

**Optional:** If you have a new camera, you can also install the latest version of libgphoto2 directly from the maintainer. Choose "Install last stable release":
```sh
wget https://raw.githubusercontent.com/gonzalo/gphoto2-updater/master/gphoto2-updater.sh
wget https://raw.githubusercontent.com/gonzalo/gphoto2-updater/master/.env
chmod +x gphoto2-updater.sh
sudo ./gphoto2-updater.sh
```


### Install photobooth

Give our webserver user access to `/var/www/`:
```sh
sudo chown -R www-data:www-data /var/www/
```

Get the Photobooth source:
```sh
cd /var/www/
sudo -u www-data -s
rm -r html/*
git clone https://github.com/PhotoboothProject/photobooth html
cd /var/www/html
git submodule update --init
yarn install
yarn build
exit
```

**Please note:** depending on your hardware `yarn install` and `yarn build` takes up to 15min!

Next we have to give our webserver user access to the USB device:
```sh
sudo gpasswd -a www-data plugdev
```

If you like to use a printer you need to have `CUPS` installed. On Raspbian `CUPS` is not installed by default:
```sh
sudo apt install -y cups
```

Next you also have to add your webserver user to the `lp` and `lpadmin` group:
```sh
sudo gpasswd -a www-data lp
sudo gpasswd -a www-data lpadmin
```

By default the CUPS webinterface can only be accessed via [http://localhost:631](http://localhost:631) from your local machine.

To remote access CUPS from other clients you need to run the following commands:
```sh
sudo cupsctl --remote-any
sudo /etc/init.d/cups restart
```


### Install Remote Buzzer support

Please follow the steps mentioned in the FAQ:

**Q:** [Can I use Hardware Button to take a Picture?](FAQ#can-i-use-hardware-button-to-take-a-picture)

### Try it out

Now you should restart your Raspberry Pi to apply those settings:
```sh
reboot
```

Please use the following to test if your Webserver is able to take pictures (gphoto must be executed in a dir with write permission):
```sh
cd /var/www/html
sudo -u www-data gphoto2 --capture-image
```

If it is not working, your operation system probably automatically mounted your camera. You can unmount it, or remove execution permission for gphoto2 Volume Monitor to ensure that the camera is not mounted anymore:
```sh
sudo chmod -x /usr/lib/gvfs/gvfs-gphoto2-volume-monitor
```

Now reboot or unmount your camera with the following commands (you get a list of mounted cameras with `gio mount -l`):
```
gio mount -u gphoto2://YOUR-CAMERA
```

Now try again.

If everything is working, open the IP address (you get it via `ip addr`) of your Raspberry Pi, or if you open it on your machine, type [http://localhost](http://localhost) in your browser.

