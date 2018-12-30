# Setup AMPPS 3.8 in Ubuntu 18.10
If you're like me and want to have your LAMP Stack setup simple and ready to go, you would love this piece of software. AMPPS is a product from the well-known Softaculous team, that packs an Apache Web Server, MySQL, PHP, Perl, Python and SOFTACULOUS.

## AMPPS Installation
```console
$ sudo apt-get update
```
Install much needed dependencies
```console
$ sudo apt-get install libfontconfig1 libxrender1
```
Get AMPPS 3.8 installer with wget
```console
$ wget http://s4.softaculous.com/a/ampps/files/Ampps-3.8-x86_64.run
```
Set necessary permissions
```console
$ sudo chmod 0755 Ampps-3.8-x86_64.run
```
Run installer
```console
$ sudo ./Ampps-3.8-x86_64.run
```

The program is installed now, but if you try to run it at this stage you will notice some glitches.

## Fix UI

```console
$ sudo nano /etc/environment
```

Add this line to the end of the file to stop Qt form using the MIT-SHM X11 extension
```console
$ export QT_X11_NO_MITSHM=1
```

## Fix Apache and PHP not running

```console
$ cd /usr/local/ampps/apache/lib
$ sudo mkdir backup
$ sudo mv ./libapr* ./backup/
$ sudo apt-get -y install libaprutil1 libaprutil1-dev libapr1 libapr1-dev
```

If Ubuntu says that the packages are already installed, try this instead

```console
$ sudo apt-get -y install --reinstall libaprutil1 libaprutil1-dev libapr1 libapr1-dev
```

Now you could run AMPPS from the command line. It's working!!!

```console
$ cd /usr/local/ampps
$ sudo ./Ampps
```

## Create a GNOME Launcher

```console
$ cd /usr/share/applications/
$ touch ampps.desktop
$ sudo nano ampps.desktop
```
Now paste the above code
```bash
[Desktop Entry]
Name=Ampps
Comment="Apache MySQL MongoDB PostgreSQL Perl Php Softaculous Stack"
GenericName="Web Tools Stack"
Exec=pkexec /usr/local/ampps/Ampps
Icon=/usr/local/ampps/ampps_large.png
Type=Application
Terminal=false
StartupNotify=true
StartupWMClass="Web Server"
Categories=Utility;WebServer;Development;Database;
MimeType=text/plain;inode/directory;
Actions=new-window;
Keywords=ampps;
```
Download the AMPPS icon from this repo and move it to the correct path
```bash
$ cd /Downloads
$ sudo mv ampps_large.png /user/local/ampps/
```

Since gksu is deprecated people should switch to PolicyKit for access control. pkexec is a standard way of runing programs as a different user and is part of polkit. [Reference](https://forum.xfce.org/viewtopic.php?id=12348)

```console
$ cd /usr/share/polkit-1/actions/
$ touch com.ubuntu.ampps.policy
$ sudo nano com.ubuntu.ampps.policy
```
Now paste the above code

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE policyconfig PUBLIC
 "-//freedesktop//DTD PolicyKit Policy Configuration 1.0//EN"
 "http://www.freedesktop.org/standards/PolicyKit/1.0/policyconfig.dtd">
<policyconfig>
  <vendor>Ampps</vendor>
  <vendor_url>ampps</vendor_url>
  <icon_name>ampps</icon_name>
  <action id="org.freedesktop.policykit.pkexec.ampps">
   <description>Run "ampps"</description>
   <message>Authentication is required to run Ampps</message>
   <defaults>
     <allow_any>auth_admin</allow_any>
     <allow_inactive>auth_admin</allow_inactive>
     <allow_active>auth_admin</allow_active>
   </defaults>
     <annotate key="org.freedesktop.policykit.exec.path">/usr/local/ampps/Ampps</annotate>
     <annotate key="org.freedesktop.policykit.exec.allow_gui">true</annotate>
   </action>
</policyconfig>
```
Now you should be able to see the program launcher. It will ask you for root privileges to run AMPPS.
