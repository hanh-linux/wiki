## Linux From Scratch
> This Wiki page is deprecated. Try at your own risks.
>
> `pacman` 6.0 build error is fixed. See [this thread](https://bbs.archlinux.org/viewtopic.php?pid=1985353#p1985353)
>
This document is a guide for building a LFS system with `pacman` and `openrc` (can be replaced with `runit` or `s6`) to be an Artix-based system. 
<br>
Based on [Linux From Scratch 10.1](https://linuxfromscratch.org/lfs/view/10.1/) and [benvd/lfs-pacman](https://github.com/benvd/lfs-pacman)
### Requirements
See [LFS requirements](https://linuxfromscratch.org/lfs/view/10.1/chapter02/hostreqs.html)
### Filesystem
See [LFS filesystem](https://linuxfromscratch.org/lfs/view/10.1/chapter02/chapter02.html), the guide is in 2.4 - 2.7
### Packages and patches
Download sources from `wget-list`
<br> 
- Replaces LFS patches by using them [Artix Linux Gitea](https://gitea.artixlinux.org). Also, some packages won't patch properly so users must manually remove patches from PKGBUILD or install from the server.
### Final preparations
- Limited directories layout: Download `filesytem` package from Artix Linux server and unpack it to `$LFS` instead of creating `bin`,`etc`,`lib`,`sbin`,`usr`,`var`,`lib64`. Create `$LFS/tools` directory. 
- Remove `.BUILDINFO`, `.PKGINFO` and `.MTREE`
### Cross-toolchain
See [Cross-toolchain](https://www.linuxfromscratch.org/lfs/view/10.1/chapter05/chapter05.html)
- With `glibc`, execute the commands below to the build directory: 
`````````
echo "slibdir=/usr/lib" >> configparms
echo "rtlddir=/usr/lib" >> configparms
echo "sbindir=/usr/bin" >> configparms
echo "rootsbindir=/usr/bin" >> configparms
`````````
### Cross compiling
See [Cross compiling tools](https://www.linuxfromscratch.org/lfs/view/10.1/chapter06/chapter06.html)
- Do not install `m4`. We will install it later
### Install temporary tools
See [`chroot` and install additional tools](https://www.linuxfromscratch.org/lfs/view/10.1/chapter07/chapter07.html)
- Enter `chroot` with command below:
Enter `chroot` with commands below:
`````````````````
chroot "$LFS" /usr/bin/env -i   \
    HOME=/root                  \
    TERM="$TERM"                \
    PS1='(lfs chroot) \u:\w\$ ' \
    PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin:/tools/sbin \
    LD_LIBRARY_PATH=/usr/lib:/tools/lib \ 
    /bin/bash --login +h
`````````````````
- `filesystem` package included essential directories so you only need to modify essential files.
- Skip `libstdc++` installation. Install `gettext`, `bison`, `perl`, `texinfo` to `/tools` instead `/usr`
- For `pacman` 5.2 or older, follow LFS guide install `Python` (required for `glibc`)
- For `pacman` 6.0, install `libffi`, `Python`, `ninja` and `meson` to `/tools` as `make` dependencies 
##### `libffi`
``````````
./configure --prefix=/tools --disable-static 
make
make install
``````````
##### `Python`
``````````
./configure --prefix=/tools --enable-shared --with-ensurepip=yes ## for setup_tools and its dependencies
make 
make install
``````````
##### `ninja`
``````````
python3 configure.py --bootstrap
install -vm755 ninja /tools/bin/
``````````
##### `meson`
``````````
python3 setup.py build
python3 setup.py install --root=dest
cp -rv dest/tools/* /tools/
``````````
Install `vim` as a temporary text editor.

`````````
./configure --prefix=/tools
make
make install
`````````
Clean up the temporary system and do backup. If your system has `squashfs-tools`, do a backup by:

`````````
mksquashfs $LFS <path to backup file>
`````````
To restore, use:
`````````
rm -rf $LFS
unsquashfs -f -d $LFS <path to backup file>
`````````
Remember to `umount` virtual kernel filesystems (`$LFS/dev/pts`,`$LFS/dev`, `$LFS/proc`. `$LFS/sys` and `$LFS/run` ) before backup or restore. 
### Final system
Don't use anything in [LFS Chapter 8](https://www.linuxfromscratch.org/lfs/view/10.1/chapter08/chapter08.html). 
<br>
Enter `chroot` with commands below:
`````````````````
chroot "$LFS" /usr/bin/env -i   \
    HOME=/root                  \
    TERM="$TERM"                \
    PS1='(lfs chroot) \u:\w\$ ' \
    PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin:/tools/sbin \
    LD_LIBRARY_PATH=/usr/lib:/tools/lib \ 
    /bin/bash --login +h
`````````````````
Install `pacman` and its dependencies 
<br>
##### `zlib`
````````````````
./configure --prefix=/usr
make
make install
````````````````
##### `pkg-config`
`````````
./configure --prefix=/usr            \
            --with-internal-glib       \
            --disable-compile-warnings \
            --disable-host-tool        \
            --disable-shared           \
            --docdir=/tools/share/doc/pkg-config-0.29.2 ## reduce documentation size
make 
make install
`````````
##### `libcap`
`````````
make
make RAISE_SETFCAP=no lib=lib prefix=/usr install
chmod -v 755 /usr/lib/libcap.so
`````````
##### `libtool`, `autoconf`, `automake`
These are `fakeroot` required dependencies to compile. Install to /tools by:
```````````
./configure --prefix=/tools
make
make install
```````````
Install `autoconf` before install `automake`
##### `fakeroot`
Move to `doc/`, copy `faked.1` and `fakeroot.1` to `de/`, `es/`, `fr/`, `nl/`, `pt/`, `sv/`.
<br>
Use patches for `fakeroot` from [Artix Linux Gitea](https://gitea.artixlinux.org/packagesF/fakeroot/src/branch/master/trunk/) then install `fakeroot` by:
`````
./bootstrap
./configure --prefix=/tools                 \
            --libdir=/tools/lib/libfakeroot \
            --with-ipc=sysv
make
make install
`````
##### `openssl`
``````````
./config --prefix=/usr         \
         --openssldir=/etc/ssl \
         --libdir=lib          \
         shared 
make
make install
``````````

##### `libarchive`
> `libarchive` must be the last one to be compiled, otherwise `pacman` will fail when trying to compile `libalpm.so` 
>
```````````````
./configure --prefix=/usr --disable-static --without-xml2
make 
make install
```````````````
##### `pacman` 5.2 or older
```````````
./configure --prefix=/usr   \
            --disable-doc     \ # need asciidoc
            --disable-shared  \
            --sysconfdir=/etc \
            --localstatedir=/var
make
make install
```````````
##### `pacman` 6.0
``````````
meson --prefix=/usr \
--buildtype=plain   \
-Ddoc=disabled        \
-Ddoxygen=disabled     \
-Dscriptlet-shell=/usr/bin/bash \
-Dldconfig=/usr/bin/ldconfig    build
meson compile -C build
meson install -C build
``````````
##### `zstd`
``````````
make
make prefix=/usr install
``````````
Add a regular user to `/etc/passwd` since `makepkg` is not allowed to run with `root` permission:
````````````````
<username>:x:1000:999:/home/<username>:/bin/bash
````````````````
`/etc/group` must contain `users:x:999:`
<br>
Create `<username>` home directory:
`````````````````
mkdir -pv /home/<username>
chown -Rv <username>:users /home/username/
`````````````````
Rechroot with <username>: 
``````````````
chroot --userspec=1000:999 "$LFS" /usr/bin/env -i \
     HOME=/home/<username>     \
     TERM="$TERM"       \
     PS1='$? \u:\w \$ ' \
     PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin:/tools/sbin \
     LD_LIBRARY_PATH=/usr/lib:/tools/lib \
     /bin/bash --login +h
``````````````
It is recommended to split 3 terminal emulator. 
- A `root` account on host to download patches, configuration files and other important files
- A `root` account on `chroot` to install packages via `pacman`
- An unprivileged user to use `makepkg`
#### Install `pacman` package with `pacman`
- Move to your `home` directory
- Download `PKGBUILD`, `makepkg.conf` and `pacman.conf` from [Artix Linux Gitea](https://gitea.artixlinux.org/packagesP/pacman/src/branch/master/trunk)
- From `PKGBUILD`, comment out the `depends`, `makedepends`, `checkdepends` and `groups` line. We won't use package group, also ignore dependencies that are not installed via `pacman`.
- Use `makepkg --nocheck --skipinteg` to compile the packages
- Modify `sources` so it only includes local `pacman` tarball, `makepkg.conf` and `pacman.conf`. 
- For `pacman` 5.2, use the `configure` for `pacman` above.
- Use `root` permission to install `pacman` by:
```````
pacman -U pacman-*.pkg.tar.*
```````
- Go to `/etc/pacman.conf`, disable `SigOptions` at line 39 and 40 and all remote server.
#### Install packages to final system
The process would be like: 
- Create a directory in your `home` directory
- Download `PKGBUILD`, patches, binary file, config, .etc from [Artix Linux Gitea](https://gitea.artixlinux.org) and copy package tarball to a directory.
- Disable `groups`, `depends`, `makedepends`, `checkdepends` in `PKGBUILD`.
- Modify `source` for local files .
- Remove all the things related to multilib build process since LFS doesn't support multilib. (`glibc` and `gcc` build files include this)
- Use `makepkg --skipinteg` (and `--nocheck` for unnecessary packages)  
- Use `pacman -U <package>.pkg.tar.zst --overwrite '*'` since a lot of binaries, libraries and files are existed. 
#### Note
- Do not compile `eudev`, `zlib`, `shadow`, `pam`, `linux` and `linux-headers` since some problems may happen with these packages.
- Do not compile `sysvinit` because we are using `openrc` as a replacement. 
- Due to differences with the tarballs, some patches won't be able to apply to the source. Simply disable them to continue the process.
- Some `PKGBUILD` files build split packages, simply remove it from `pkgname` and its `package-*` function if you want to save size except listed packages below. 
- Some `PKGBUILD` files won't work properly at the first time. Modify the `PKGBUILD` and test it again for several times.
- Use the packages and their dependencies in `base` `PKGBUILD`. See [`base` from Artix Linux Gitea](https://gitea.artixlinux.org/packagesB/base/src/branch/master/trunk/PKGBUILD). 
- Due to compiling error, try to disable some `configure` flags. Use the part below as a reference.
#### Individual packages
> RENOTE: Use this as a reference.
>
##### `filesystem`
- Install `iana-etc` before install `filesystem`
- Some files in `/etc` with `*.pacnew` are package files. Use them later to restore default or remove them.
##### `glibc`
- Remove all other split packages from `pkgname` and its function except `glibc`
- Remove all `CFLAGS` and `CXXFLAGS` at `/etc/makepkg.conf` for build process. After that, restore them. 
- Remove all lines that modify `CFLAGS` and `CXXFLAGS` except the re-enable fortify one.
- Remove all `i686` build lines.
- Modify the `_configure_flags` to be:
`````
      --prefix=/usr
      --with-headers=/usr/include
      --enable-kernel=4.4
      --enable-multi-arch
      --enable-stack-protector=strong
      --disable-profile
      --disable-werror
`````
##### `gcc` and `gcc-libs`
- Remove all split packages at `pkgname` and functions except `gcc` and `gcc-libs`
- Use configure flags as below:
````````````````````
      --prefix=/usr \
      --libdir=/usr/lib \
      --libexecdir=/usr/lib \
      --mandir=/usr/share/man \
      --infodir=/usr/share/info \
      --enable-languages=c,c++ \
      --with-isl \
      --with-linker-hash-style=gnu \
      --with-system-zlib \
      --enable-__cxa_atexit \
      --enable-cet=auto \
      --enable-checking=release \
      --enable-clocale=gnu \
      --enable-default-pie \
      --enable-default-ssp \
      --enable-gnu-indirect-function \
      --enable-gnu-unique-object \
      --enable-install-libiberty \
      --enable-linker-build-id \
      --enable-lto \
      --enable-plugin \
      --enable-threads=posix \
      --disable-libssp \
      --disable-libstdcxx-pch \
      --disable-libunwind-exceptions \
      --disable-werror \
      --enable-shared \
      --disable-multilib \
      gdc_include_dir=/usr/include/dlang/gdc

````````````````````
- Remove all `*/32/*` libraries lines. Also, `libatomic`, `libgomp`, `libitm`, `libquadmath`, `libsanitizer/{a,l,ub,t}san`, `libstdc++-v3/src` and `libvtv` are available to `install-toolexeclibsLTLIBRARIES`.
- Do not use `install-info` for `gcc-libs` to reduce compile time. 
##### `bash`
- Comment out all `sha256sums` part after `package()`
##### `tzdata`
- Simply download it from Artix Linux mirror and install. See mirrorlist below
##### `fakeroot`
- Remove line contains `po4a`. Manually copy `faked.1` and `fakeroot.1` like above before `makepkg`.
##### `pacman` dependencies
- Similar to above, make packages and install `libarchive` as the last packages.
#### Install another packages
##### Pre-installation
- Download `wget` tarball from [BLFS](https://linuxfromscratch.org/blfs/view/11.0/basicnet/wget.html)
- Install the package by:
``````````
./configure --prefix=/tools --with-ssl=openssl
make
make install
``````````
- Download `artix-mirrorlist` from one of Artix Linux mirrors. See [available mirrors](https://gitea.artixlinux.org/packagesA/artix-mirrorlist/src/branch/master/trunk/mirrorlist)
- Use `pacman -U artix-mirrorlist*.pkg.tar.*`
- Go to `/etc/pacman.conf`: 
  - Uncomment `XferCommand` line with `wget`and change it to `XferCommand = /tools/bin/wget --no-check-certificate <flags>`
  - Uncomment `galaxy`, `world` and `system` repositories. 
- Modify `/etc/resolv.conf` contains at least one `nameserver`
- Use `pacman -Syy curl` to install `curl` as the main packages downloader.
- Go to `/etc/pacman.conf` again:
  - Comment out `XferCommand` line with `/tools/bin/wget`
  - Uncomment `XferCommand` line uses `curl`
- Do a `pacman -Syy`
##### Installation
- Install `eudev`, `zlib`, `shadow`, `pam`, `linux` and `linux-headers` with `--overwrite` flag.
- Install `linux-firmware`, `mkinitcpio`, `sudo` (or `opendoas`), `openrc`, networking programs, `grub` and a text editor.
##### Post installation
- Configure `/etc/sudoers` (`/etc/doas.conf` if you use `opendoas`).
- Reset `root` password with `passwd` 
- Create a symlink for `openrc-init` to `init` or the system won't boot normally. 
#### Finish build
- Remove `/tools` to save size and avoid some problems with `mkinitcpio`. Remove `/sources` to save size.
- Strip debugging symbols by using the following commands (optional): 
````````````
save_usrlib="libquadmath.so.0.0.0 libstdc++.so.6.0.28 libitm.so.1.0.0 libatomic.so.1.2.0 ld-2.33.so libc-2.33.so libpthread-2.33.so libthread_db-1.0.so" 

cd /usr/lib

for LIB in $save_usrlib; do
    objcopy --only-keep-debug $LIB $LIB.dbg
    strip --strip-unneeded $LIB
    objcopy --add-gnu-debuglink=$LIB.dbg $LIB
done

unset LIB save_usrlib

find /usr/lib -type f -name \*.a \
   -exec strip --strip-debug {} ';'

find /usr/lib -type f -name \*.so* ! -name \*dbg \
   -exec strip --strip-unneeded {} ';'

find /usr/{bin,libexec} -type f \
    -exec strip --strip-all {} ';'
````````````
- Remove `src` and `pkg` in any compiled package build directories. Move build directory to your host `/home` to reuse it again for future build. 
- Remove `<username>` by using: `userdel -r <username>`
- Remove `/boot/initramfs*`
- Modify `/etc/fstab`, run `mkinitcpio -p linux` and install `grub` bootloader to your disk by:
``````````````````````
grub-install --target i386-pc /dev/sdX
update-grub
``````````````````````
- Or if your LFS partition is installed on the same disk with your host, you must have the separate `/boot` partition and `os-prober`. Then run `update-grub`. 
> Install `efibootmgr` to LFS if you want to use UEFI boot.
>
### See also: 
- [UEFI on LFS](https://www.linuxfromscratch.org/hints/downloads/files/lfs-uefi.txt): Guide to setup UEFI on LFS
- [benvd/lfs-pacman](https://github.com/benvd/lfs-pacman): `pacman` on LFS
- [LFS bootable image on Hanh Wiki]()<sup>not available</sup>: Create an image allow to boot LFS read-only from external devices. 