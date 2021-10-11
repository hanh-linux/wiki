## Core libraries
> This is deprecated and not tested, probably harmful to your system. Try at your own risk
>
By default, Hanh Linux only supports x64 architecture and C/C++ languages `glibc`, `gcc-libs` and `gcc` to reduce system size and slowness. This document is a guide to reinstall the multilib one. 
### Pre-installation
Comment out these line in `/etc/pacman.conf`: 
`````````
[64only]
Include = /etc/pacman.d/mirrorlist-hanh
`````````
Run a database update: 
``````````````
pacman -Syy
``````````````
`glibc`, `gcc-libs` and `gcc` version must be similar with the multilib one.
### Installation
> **REPEAT: This is not TESTED. TRY AT YOUR OWN RISK**
>
Run: 
``````````````
pacman -S gcc-libs glibc
pacman -S gcc # if you need to compile
``````````````
