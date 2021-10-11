## Hanh Linux
> This article is out-of-date since Hanh Linux is going to be an independent Linux distribution
>

A Linux distribution based on Artix by using LFS/BLFS guide to build. Hanh Linux supports x86_64 architecture, with rolling-release model and have a guided installer for users.
### Features
- Simplicity: Around 120 packages installed; only contains basic packages, network management (dhcpcd and iwd),  an editor and some packages support the installation.
- Lightweight: The installation image is under 650MB, and the system size is under 2,25 GB. We are adding support for netinstall so user can have a more lightweight system.
- Rolling release: Access the newest packages, or roll back to their earlier versions. Not really stable, but more fun with expiriment.
- `systemd`-less: Thanks to Artix, you can live without using a bloated and insecure init daemon by using OpenRC - Gentoo universal init daemon.
### History
07/2021: Hanh Linux 1.0 release.
### See also
- [LFS target architecture](https://linuxfromscratch.org/lfs/view/stable/prologue/architecture.html): Non-multilib core libraries and components
- [systemd reception](https://en.wikipedia.org/wiki/Systemd#Reception): systemd and Unix users
- [OpenRC on Hanh Wiki](https://github.com/hanhlinux/hanhlinux/wiki/Basic:OpenRC): Guide to use OpenRC
