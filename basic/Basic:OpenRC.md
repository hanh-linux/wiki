## OpenRC
> This Wiki page is out-of-date. Try at your own risks.
>
OpenRC (`openrc`) is a dependency-based init system that has more features than the traditional SysVinit (`sysvinit`), replaces the default `/sbin/init` by it own program. It is developed by Gentoo developers and designed to use on another GNU/Linux or BSD systems.
## Installation
By default, `openrc` is installed on your system.
### Services
Install the `*-openrc` packages to use the services.
### Configuration
OpenRC configuration is in `/etc/rc.conf`. 
### Network management
OpenRC uses `netifrc` to manage network as default. Hanh Linux uses `iwd` that is compatible with OpenRC.It is possible to install another network manager such as `NetworkManager` and `connman`.
## Usage
### Runlevels
By default, OpenRC uses `boot`, `sysinit`, `default` and `shutdown`. Other runlevels can be `nonetwork`. It is possible to add custom runlevel(s) by using:
```````````````
mkdir /etc/runlevels/<runlevel>
rc-update -s add default <runlevel>
```````````````
Switch to the new runlevel by: `openrc <runlevel>`
<br>
Add `softlevel=<runlevel>` to bootloader config (`/boot/grub/grub.cfg`) to replace `default` runlevel when OpenRC starts.
### Services
To modify services, use:
``````````````````
rc-update add <service> <runlevel>
rc-update del <service> <runlevel>
``````````````````
To start/stop services, use:
`````````````````
/etc/init.d/<service> start
/etc/init.d/<service> stop
`````````````````
or
`````````````
rc-service <service> start
rc-service <service> stop
`````````````
### Status
See all services by using:
```
rc-update show -v
```
See all services status by using:
```
rc-status --servicelist
```
Or just `rc-status` for current runlevel 
## More features
- [Stacked Runlevel](https://wiki.gentoo.org/wiki/OpenRC/StackedRunlevel)
- [CGroups](https://wiki.gentoo.org/wiki/OpenRC/CGroups)
- [Hotplug (deprecated)](https://wiki.gentoo.org/wiki/OpenRC/Event_driven)
- [Dependency behavior](https://wiki.gentoo.org/wiki/OpenRC#Dependency_behavior)
## See also
- [OpenRC at Gentoo Wiki](https://wiki.gentoo.org/wiki/OpenRC): More details about OpenRC
- [OpenRC at Artix Wiki](https://wiki.artixlinux.org/Main/OpenRC): OpenRC configuration and usage
- [OpenRC at Arch Wiki](https://wiki.archlinux.org/title/OpenRC)<sup>unofficial support</sup>: OpenRC troubleshooting
- [OpenRC guide on Alpine Linux](https://docs.alpinelinux.org/user-handbook/0.1a/Working/openrc.html): OpenRC basic handbook by Alpine Linux