# CLFS status
The new CLFS system created by Sun Haiyong is the new template and claimed working system to compile cross-tools and rootfs for LoongArch.  
However, the new CLFS is not compatible with Loongnix 20, since the new glibc LoongArch port on Github changed syscalls and they are dependent on the new kernel.  
I spent a lot of time on trying to chroot in CLFS, like static compiled bash using the native toolchain on Loongnix finally let me to chroot in new CLFS, even GCC 12 in CLFS can compile Hello-world.c and run. However, other tools like make invokes new syscalls will crash.  

In addition, there are problems with booting CLFS.  
The new CLFS system can be downloaded from here: https://github.com/sunhaiyong1978/CLFS-for-LoongArch/releases/download/20210801/loongarch64-clfs-system-20210801.tar.bz2  
However, I tried booting the CLFS from Sun Haiyong with GRUB on Loongnix, it will get stuck after loading the radeon driver.  
![photo_2021-08-09_23-04-47](https://user-images.githubusercontent.com/53088781/128760292-ee4a7187-6d28-46b1-91c3-38a39f8715ae.jpg)
First, I only add `root=/dev/sda3` after loading kernel in grub.cfg, and with Loongnix kernel and initrd. The output looks like this:  
![photo_2021-08-09_23-16-05](https://user-images.githubusercontent.com/53088781/128761675-17a7d2ad-7658-4ffb-a596-d29fccc0886b.jpg)
Then, I use mentor's suggestion that add `init=/bin/bash` to grub.cfg and with Loongnix kernel and initrd. Then the output looks like this  
![photo_2021-08-09_23-10-25](https://user-images.githubusercontent.com/53088781/128760987-7cc18afb-e534-4b6e-8042-440d2b7f0ed1.jpg)
The reason for failed switch_root probably is because the bash compiled with new toolchain can not using the dynamic library on the host system. The similar case appears in chroot.   
Loongnix initrd also contains systemd, have a look at switch_root: https://github.com/systemd/systemd/blob/main/src/shared/switch-root.c  

# boot from USB

I use the new kernel and initrd from new CLFS suggested by Sun Haiyong, and boot them from USB.  
Though, IMAO the problem is not caused by not booting from USB nor something wrong with grub.cfg.
It will again get stuck after loading Linux kernel with `quiet` suggested by Sun.  
![photo_2021-08-09_23-38-12](https://user-images.githubusercontent.com/53088781/128764160-8ecbc64d-9757-45cc-9073-46037af2ad8e.jpg)
The likely explanation is that the new Linux kernel is not complete or not compatible with the Lemote A2101 we got.  
Also phorcys in Telegram group also reported that CLFS with GRUB on Loongnix or CLFS's GRUB will not boot on his Lemote A2101.  

# midterm goal

build CLFS for LoongArch completely, make it bootable.

# 
