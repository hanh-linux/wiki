## `systemd` alternative tools
> This Wiki page is out-of-date. Try at your own risks.
>
This guide is for users who just move from other Linux distributions use `systemd` can find a repleacement for its features.
<br>
<br>
From `systemd` [project web page](https://systemd.io/): "Other parts include a logging daemon, utilities to control basic system configuration like the hostname, date, locale, maintain a list of logged-in users and running containers and virtual machines, system accounts, runtime directories and settings, and daemons to manage simple network configuration, network time synchronization, log forwarding, and name resolution."
### Services manager
Replaces: `systemctl`
See [Basic:OpenRC](https://github.com/hanhlinux/hanhlinux/wiki/Basic:OpenRC) 
### Logging daemon
Replaces: `journalctl`
<br>
Install `syslog-ng` and `syslog-ng-openrc` packages. Enable `syslog-ng` service by:
```````````````
rc-service syslog-ng start
rc-update add syslog-ng default
```````````````
For more information, see [syslog-ng on Gentoo Wiki](https://wiki.gentoo.org/wiki/Syslog-ng#Configuration)
### Hostname configuration
Replaces: `hostnamectl`
<br>
Add hostname to `/etc/hostname` by:
`````````````
<hostname>
`````````````
Add these lines `/etc/hosts`:
`````````````
127.0.0.1        localhost
::1              localhost
127.0.0.1        <hostname>
`````````````
Or just: 
``````````````
127.0.0.1 localhost <hostname>
``````````````
### Time connfiguration
Replaces: `timedatectl`
<br>
Create a symlink to /etc/localtime by: 
````````````````
ln -svf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
````````````````
`<Region>/<City>` can be found at `/usr/share/zoneinfo`
It is possible to use `hwclock` (create `/etc/adjtime`): 
````````````````
hwclock --systohc # sync system time to hardware clock
hwclock --hctosys # sync hardware clock to system time
````````````````
### Locale configuration
Replaces: `localectl`
<br>
Comment out a locale in `/etc/locale.gen` by using a text editor. For example, with `vim`:
```````````````
vim /etc/locale.gen
# Start of /etc/locale.gen
.......
en_US.UTF-8
.......
# End of /etc/locale.gen
```````````````
Add locale to `/etc/locale.conf` by: 
``````````
LANG=<locale>
``````````
Set keyboard layout by (add this line to ~/.bashrc for autostart): 
````````
loadkeys <keyboard layout>
````````
`<keyboard layout>` can be found at `/usr/share/kbd/keymaps`

### Logged users
Replaces: `loginctl`
<br>
Use these commands below:
```````````````````````
w
who -a
users
```````````````````````
For more details, see [UNIX/Linux list current logged in users](https://www.cyberciti.biz/faq/unix-linux-list-current-logged-in-users/)
### Network configuration 
Replaces: `systemd-networkd`
<br>
`netifrc` is the default framework for `openrc` for network management and configuration. For usage, see [Netifrc on Gentoo Wiki](https://wiki.gentoo.org/wiki/Netifrc#Configuration_file)
Also, check out `iwctl`, `wpa_supplicant` and `NetworkManager` as an alternative favor.
### Network time synchronization
Replace: `systemd-timesyncd`
<br>
Install `ntp` and `ntp-openrc` packages. Enable `ntp` service by: 
`````````````````
rc-service ntp-cilent start
rc-update add ntp-cilent default
`````````````````
For usage, see [NTP on Gentoo Wiki](https://wiki.gentoo.org/wiki/Ntp#Configuration)

### Name resolution
Replace: `systemd-resolved`
<br>
`dhcpcd` - DHCP Cilent Daemon (not `dhcpd` - DHCP Daemon), `NetworkManager`, `openresolv` are `/etc/resolv.conf` management framework. 
<br> Also, you can modify `/etc/resolv.conf` manually. See [resolv.conf configuration on Gentoo Wiki](https://wiki.gentoo.org/wiki/Resolv.conf#Configuration)

