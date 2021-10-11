## Basic:Installation
> This Wiki page is out-of-date. Try at your own risks.
>
### Pre-installation
- For UEFI installation, consider disable Secure Boot.
- Make sure you have compatible system requirements. See [system requirements](https://osdn.net/projects/hanhlinuxiso/releases/). 
- For using a USB boot creator overwrites default installation disc label, remember to append one of these variables:
1. `isolabel`: Device label. (`isolabel=<label>`)
2. `isodev`: Partition name (`isodev=/dev/sdXy`)
3. `isouuid`: Device UUID (`isouuid="<UUID>"`)
- Append variable to `<device_root>/syslinux/syslinux.cfg` if using Legacy boot
- Append variable to `<device_root>/grub/grub.cfg` if using UEFI boot or edit the GRUB command line when booting.
- Boot from external device, select Hanh Linux to boot and follow what the ISO tells you to do.
### Installation 
- Login and change keymaps by using: `loadkey`
- Run `installer` to start the installation and follow the instructions.
- See more information that supports the installation below. 
- After finish installation, reboot into your installed system.
#### Problems 
- When enter the device name, please enter `/dev/sdX` (not `sdX`), the same as when enter the partition (`/dev/sdXy`, not `sdXy`). 
- When you are installing to the new disk, please use `fdisk` suggestions provided in the installer or do it before. 
- A `chroot` dropping support is added to the installer. Please use it to do some work such formatting other partitions or modify `/etc/fstab`. 
- When the installation breaks or it is interrupted, reboot the ISO to `umount` the device, otherwise the installation will fail to install to the device
##### Separate boot partition
> You can do this step before running `installer` or in the dropped `chroot` shell 
>
- First, you need to create a partition. It must be formatted in FAT32 and must be equal or larger than 512MB (536870912 bytes)
- Mount it to `<partition_you_want_to_install>/boot/efi` if you want to use UEFI. Also, mount `efivarfs` to `/sys/firmware/efi/efivars`
- Exit the `chroot` shell if you are in it
- With Legacy, use the default command.
- With UEFI, use the custom command for: `grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub /dev/sdX`. 
### Post installation 
- Install the `tzdata` (since releases before 1.2.1 aren't contained this package.)
```````````````
pacman -S tzdata --overwrite '*'
```````````````
- Install Xorg and its drivers by: 
````````````
pacman -S xorg xorg-server xorg-xinit xorg-drivers
````````````
- Install a DE/WM by using `pacman`. It is recommended to use a WM for speed and lower RAM usage. 
For further information, see [List of DE from ArchWiki](https://wiki.archlinux.org/title/desktop_environment#List_of_desktop_environments) and [List of WM from ArchWiki](https://wiki.archlinux.org/title/window_manager#List_of_window_managers). 
- Install some optional tools such as office, media player and image viewer
##### Display manager
Display manager isn't important to install. If you want something like this, add these lines to:
- `.bash_profile`
`````````````
read -p "Press any key to start Xorg. Use ^C to abort"
startx
`````````````
- `.xinitrc`
`````````````
exec <your DE/WM>
`````````````
##### VirtualBox
For Legacy VirtualBox, mount the default Guest Addition image then run the VirtualBox Guest Addition `.run` file to finish setup. You do not need to compile additional modules