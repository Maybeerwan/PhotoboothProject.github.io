

<a href="https://photoboothproject.github.io" class="button hidden">Home</a>
<a href="https://photoboothproject.github.io/Changelog" class="button hidden">Changelog</a>
<a href="https://photoboothproject.github.io/INSTALL" class="button hidden">Install</a>
<a href="https://photoboothproject.github.io/FAQ_MENU" class="button hidden">FAQ</a>
<a href="https://photoboothproject.github.io/DONATION" class="button hidden">Donate</a>

---

## Installation on Windows

### Download needed files
- Download Apache2 and Visual C++ Redistributable for Visual Studio: [https://www.apachelounge.com/download](https://www.apachelounge.com/download)
- Download PHP 7.3.x or PHP 7.4.x (VC15 x64 Thread Safe): [https://windows.php.net/download](https://windows.php.net/download)
- Download Digicamcontrol: [http://digicamcontrol.com/download](http://digicamcontrol.com/download)
- Download Notepad++: [https://notepad-plus-plus.org/downloads](https://notepad-plus-plus.org/downloads)
- Download latest Photobooth release (_photobooth-4.x.x.zip_ or _photobooth-4.x.x.tar.gz_ - **Note:** the _Source code_ files won't work!): [https://github.com/PhotoboothProject/photobooth/releases](https://github.com/PhotoboothProject/photobooth/releases)

### Install & extract needed Software
- Install Notepad++
- Install Visual C++ Redistributable for Visual Studio
- Extract the Apache2 ZIP (_httpd-2.4.X-winXX-XXXX.zip_) to C:/
- Extract the PHP ZIP (_php-7.X.XX-Win32-vc15-xXX.zip_) to C:/php

### Prepare Apache HTTP Server
Edit `C:\Apache24\conf\httpd.conf` using **Notepad++**  
Find the following text (~ on line 284):
```
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
```
And change it to  
```
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>
```

To the end of the file add the following:  
```
LoadModule php7_module "C:/php/php7apache2_4.dll"
<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>

PHPIniDir "C:/php"
```
For reference see [f260b49](https://github.com/PhotoboothProject/photobooth/commit/f260b49d2029825d33eb9d35ceda3f19423418db)


Inside `C:\Apache24\htdocs` add a new file called `info.php` and add the following content:  
```
<?php
phpinfo();
?>
```

Inside C:\Apache24 create a new file called `cmd.bat` and add th following content:
```
cd "C:\Apache24\bin"
cmd
```

### Prepare PHP
Go to `C:\php` and rename the `php.ini-production` to `php.ini`.

Edit the `php.ini` using **Notepad++** to enable the GD library:  
Find `;extension=fileinfo` and remove the `;` in front of the line.  
Find `;extension=gd2` and remove the `;` in front of the line.  
Find `;extension_dir = "ext"` and remove the `;` in front of the line and change it to `extension_dir = "C:/php/ext"`.

For reference see [ff4259a](https://github.com/PhotoboothProject/photobooth/commit/ff4259aece2094922c1d9b8fc2825fb44a710560)

### Start Apache Server
Go to `C:\Apache24` and right click on the cmd.bat, choose "Run as administrator":  
To start the Webserver on boot automatically, type `httpd.exe -k install`.

Once that's done, lets start our webserver:  
`httpd.exe -k start`

If you need to stop the webserver (e.g. if you like to change the `php.ini`):  
`httpd.exe -k stop`

### Test your Webserver & PHP
Open [http://localhost/info.php](http://localhost/info.php) in your Browser, you should see the PHP Information page.

### Install Digicamcontrol
Install Digicamcontrol to `C:\Apache24\htdocs\digicamcontrol\`

### Setup Photobooth
Remove all files inside `C:\Apache24\htdocs\`.  
Next you need to extract the Photobooth Release-ZIP to `C:\Apache24\htdocs\`.  

Open [http://localhost/admin](http://localhost/admin) in your Browser and adjust your "*take picture command*" (inside the "*Commands*" section):  
`C:\Apache24\htdocs\digicamcontrol\CameraControlCmd.exe /capture /filename %s`

### Enjoy!
You should now be able to use Photobooth on your Windows machine!  
