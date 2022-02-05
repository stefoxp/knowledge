# Ubuntu OpenBox

This is my summary of the [Ubuntu Openbox lessons](https://www.ubuntuopenbox.com/lessons/), by Dat H. Nguyen.

I use it while learning and as quick reference. It is not intended to be an standalone substitution of the book so if you really want to learn the concepts here presented, buy and read the book and use this repository as a reference and guide.

If you are the publisher and think this repository should not be public, just write me on http://linkedin.com/in/stepasquini and I will make it private.

Contributions: Issues, comments and pull requests are welcome.

## Ubuntu minimal installation

From [https://ubuntu.com/download/alternative-downloads](https://ubuntu.com/download/alternative-downloads) download the network installer.

Install without any package of software.

## Openbox installation

The necessary packages for a competent Ubuntu operating system using Openbox are:

```bash
sudo apt-get install xorg openbox openbox-themes openbox-xdgmenu suckless-tools obmenu lxappearance terminator lxpanel thunar thunar-volman thunar-archive-plugin thunar-media-tags-plugin humanity-icon-theme gvfs gvfs-backends nitrogen alsa-base alsa-utils vlc numlockx light-locker lightdm wicd lxpolkit xfce4-notifyd
```

[Overview of packages on ubuntuopenbox.com](https://www.ubuntuopenbox.com/lessons/m31-overview-of-the-installation/)

> I cannot install openbox-themes openbox-xdgmenu packages
ligthdm don't complete login. Error: lightdm: pam_succeed_if(lightdm:auth): requirement "user ingroup nopasswdlogin" not met by user ...

``` bash
# Change keyboard layout into shell
sudo loadkeys us

# edit lightdm configuration (https://wiki.ubuntu-it.org/AmbienteGrafico/DisplayManager/LightDM)
sudo vi /etc/lightdm/lightdm.conf

# restart lightdm
systemctl restart lightdm
```

[https://acavalin.com/p/lightdm_passwordless_login](https://acavalin.com/p/lightdm_passwordless_login)
