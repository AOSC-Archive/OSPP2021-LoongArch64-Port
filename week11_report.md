# BuildKit
On CLFS, after the stage 1 in https://github.com/AOSC-Dev/scriptlets/blob/master/loongarch-bootstrap, first, edit $TARGETDIR/etc/apt/sources.list to add the following line
```
deb [allow-insecure=yes] http://0.0.0.0:8000 ./
```
Then, add new ssh connection to Yukoaioi and run
```
python3 -m http.server -d /clfs-test-bak/clfs-test/debs
```
finally chroot into $TARGETDIR and do `apt update` to see whether it works.

The following command contains the built deb packages in BuildKit that are needed to install in bootstrap stage 2.

```
apt install apt btrfs-progs cryptsetup dbus debianutils dosfstools e2fsprogs \
f2fs-tools fcron fuse gnupg gptfdisk os-prober parted polkit rfkill \
shadow smartmontools sysfsutils trousers xfsprogs sudo bcache-tools \
mdadm multipath-tools mtd-utils nvme-cli quota-tools sg3-utils \
genfstab exfat-utils gpart jfsutils \
reiserfsprogs hfsprogs hddtemp powertop apt-file \
aosc-aaa bash gcc-runtime zlib \
binutils bison diffutils flex gawk gcc groff libtool m4 make patch \
pkg-config sed autoconf automake cmake gettext help2man texinfo \
meson ninja \
ed nano \
bind bluez dhcp dnsmasq ethtool geoip-api-c iana-etc idnkit inetutils \
iproute2 iptables iw net-tools openssh nfs-utils nghttp2 ppp \
rsync shadowsocks-libev wireless-tools wpa-supplicant proxychains-ng mtr \
idnkit netcat lksctp-tools \
python-2 python-3 pip virtualenv \
systemd modemmanager networkmanager \
bzip2 coreutils diffutils convmv cpio dos2unix elfutils file findutils \
gawk gpm grep groff gzip hdparm htop iotop kbd kmod less lrzip \
lsb-release lsof lz4 lzip lzo lzop man-db nano neofetch nethogs newt \
patch pciutils procps psmisc screen sed sdparm tmux pv \
progress tar texinfo unrar unzip usbutils util-linux which zip \
xz lm-sensors expect libarchive man-pages lshw tree arch-chroot \
libpsl pcre2 curl wget libsigsegv w3m
```

## packages unable to build

- atm, since it requires Rust
- v2ray, since it requires Go
- kernel-tools, failed because upstream linux 4.19 tarball have some different APIs with loongnix's kernel
- gdb cgdb strace valgrind in debug-base, since all packages need porting

# build packages

- w3m
`w3m` mandatory requires `gc`  
`gc` needs porting to LoongArch. There are porting suggestions here: https://github.com/ivmai/bdwgc/blob/master/doc/porting.md  
I don't have much time and decide to try the ported `libgc` on Loongnix.
Frist, I manually let the ported `libgc-dev` and `libgc1` debs in Loongnix to install on CLFS
```
export TARGETDIR=/
cd /tmp/work
echo "------ar libgc1c2_7.6.4-0.4.lnd.1_loongarch64.deb to /tmp/work------"
ar -x /sources/libgc1c2_7.6.4-0.4.lnd.1_loongarch64.deb
cd $TARGETDIR
echo "------extracting data.tar.xz to $TARGETDIR------"
tar xvf /tmp/work/data.tar.xz -C $TARGETDIR
cd /tmp/work
echo "------ar libgc-dev_7.6.4-0.4.lnd.1_loongarch64.deb to /tmp/work------"
ar -x /sources/libgc-dev_7.6.4-0.4.lnd.1_loongarch64.deb
cd $TARGETDIR
echo "------extracting data.tar.xz to $TARGETDIR------"
tar xvf /tmp/work/data.tar.xz -C $TARGETDIR
```
Then, I move all `/usr/lib/loongarch64-linux-gnu/libgc*` to `/usr/lib`  
Finally, I can build w3m without issue.  
Notice that, the `libgc` headers does not contain any LoongArch ported code, so I am using binary blob and this is only experimental and temporary solution.  


# Compile error

- kernel-tools

```
make[2]: Entering directory '/var/cache/acbs/build/acbs.wnj2xt4e/linux-4.19.206/tools/usb/usbip/libsrc'
  CC       libusbip_la-names.lo
  CC       libusbip_la-usbip_host_driver.lo
  CC       libusbip_la-usbip_device_driver.lo
  CC       libusbip_la-usbip_host_common.lo
  CC       libusbip_la-usbip_common.lo
usbip_device_driver.c: In function 'read_usb_vudc_device':
usbip_device_driver.c:108:2: error: 'strncpy' specified bound 256 equals destination size [-Werror=stringop-truncation]
  strncpy(dev->path, path, SYSFS_PATH_MAX);
  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
usbip_device_driver.c:127:2: error: 'strncpy' specified bound 32 equals destination size [-Werror=stringop-truncation]
  strncpy(dev->busid, name, SYSFS_BUS_ID_SIZE);
  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cc1: all warnings being treated as errors
make[2]: *** [Makefile:471: libusbip_la-usbip_device_driver.lo] Error 1
make[2]: *** Waiting for unfinished jobs....
usbip_common.c: In function 'read_usb_device':
usbip_common.c:229:2: error: 'strncpy' specified bound 256 equals destination size [-Werror=stringop-truncation]
  strncpy(udev->path,  path,  SYSFS_PATH_MAX);
  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
usbip_common.c:230:2: error: 'strncpy' specified bound 32 equals destination size [-Werror=stringop-truncation]
  strncpy(udev->busid, name, SYSFS_BUS_ID_SIZE);
  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cc1: all warnings being treated as errors
make[2]: *** [Makefile:478: libusbip_la-usbip_common.lo] Error 1
make[2]: Leaving directory '/var/cache/acbs/build/acbs.wnj2xt4e/linux-4.19.206/tools/usb/usbip/libsrc'
make[1]: *** [Makefile:497: all-recursive] Error 1
make[1]: Leaving directory '/var/cache/acbs/build/acbs.wnj2xt4e/linux-4.19.206/tools/usb/usbip'
make: *** [Makefile:365: all] Error 2
```


# personal affairs

This week, my study term begins, though not busy right now, I need to spend some time on it.  
Also, my neighbors seem not very happy, they make noises at random time, force me to change my live area often. 
