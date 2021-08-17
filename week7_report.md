# APT status

The APT requires a lot of depedencies, and building those depedencies seems error-prone on ACBS, since there is no `libapt-pkg` in CLFS before installing APT.  
Thus, I choose to build those depedencies from source and cut most optional requirements.  
In this process, one big and important software is systemd, I use compile flags from https://github.com/sunhaiyong1978/CLFS-for-LoongArch/blob/main/CLFS_For_LoongArch64-20210801.md#systemd  
after installing important libraries, I decide to use the wayaround from AOSC OS retro to disable zstd, systemd, gtest build dependencies and documentation generation, since they are not necessary in this early stage.  

```
export CPPFLAGS="${CPPFLAGS} -DHAVE_SECCOMP=0"

sed -e '/add_subdirectory(doc)/d' \
    -e '/add_subdirectory(test)/d' \
    -i "$SRCDIR"/CMakeLists.txt
```
After that, enter the APT source folder, and compile it by Cmake  
Notice that `-DCMAKE_INSTALL_PREFIX:PATH=/usr` is necessary if dpkg is installed with flag `--prefix=/usr`

```
cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr -DWITH_DOC=OFF -DUSE_NLS=OFF .
make all install
```

# compile errors

If not specified, The errors are all coming from building CLFS where I'm using the old GCC-8 toolchain.  

* On flex:  

```
flex-realloc.o  -lm
./stage1flex   -o stage1scan.c ./scan.l
make[2]: *** [Makefile:1696: stage1scan.c] Segmentation fault (core dumped)
make[2]: Leaving directory '/mnt/clfs/sources/flex-2.6.4/src'
make[1]: *** [Makefile:546: all] Error 2
make[1]: Leaving directory '/mnt/clfs/sources/flex-2.6.4/src'
make: *** [Makefile:533: all-recursive] Error 1
Making install in src
make[1]: Entering directory '/mnt/clfs/sources/flex-2.6.4/src'
./stage1flex   -o stage1scan.c ./scan.l
make[1]: *** [Makefile:1696: stage1scan.c] Segmentation fault (core dumped)
make[1]: Leaving directory '/mnt/clfs/sources/flex-2.6.4/src'
make: *** [Makefile:533: install-recursive] Error 1
```


# dpkg and APT configuration

The dpkg and APT need to know what packages are installed in current system,  maybe edit `/var/lib/dpkg/status` or `/usr/local/var/lib/dpkg/status` guided by https://www.linuxfromscratch.org/hints/downloads/files/dpkg.txt  
I use `ln -sv /var/lib/dpkg/status /usr/local/var/lib/dpkg/status` since I installed dpkg with `--prefix=/usr`  
Also, see the old LFS attachment for basic example of dpkg/status : http://www.linuxfromscratch.org/hints/downloads/files/ATTACHMENTS/dpkg/status

# ACBS scripts

## major change

- openssl use linux-generic64
- libffi add loongarch64 patch
- systemd change build script (unset CFLAGS CXXFLAGS) and add loongarch64 patch
- bzip2 remove /usr/lib/libbz2.a in beyond
- xz change build script

## minor change

- expat update version and source link
- update dpkg version and source link
- mpdecimal change defines
- libxml2 cut depends
- xz cut build po4a
- pcre2 without jit
- brotli chmod +x *.so

# environment variable

`export DPKG_FORCE=overwrite`
`export PYTHONPATH=/root/.local/lib/python3.9/site-packages`

# dependency and solution

- lm-sensors actually also depend on flex

```
/bin/bison
bison -p sensors_yy -d lib/conf-parse.y -o lib/conf-parse.c
Please install flex, then run "make clean" and try again
Makefile:175: lib/conf-lex.ad: No such file or directory
make: *** [Makefile:283: lib/conf-lex.c] Error 1
```
compile glib with ACBS will cause core dumped
so I compile glib on Loongnix and copy libglib*.so into chroot

# dpkg warnings

after `export DPKG_FORCE=overwrite` these warnings will appear during `acbs-build libnsl2 rpcsvc-proto`  

- libnsl2
```
Unpacking libnsl2 (1.3.0-1) ...
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nis.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nis.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nis_callback.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nis_callback.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nis_object.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nis_tags.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nislib.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/yp.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/yp.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/yp_prot.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/ypclnt.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/yppasswd.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/yppasswd.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/ypupd.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/lib/libnsl.so', which is also in package glibc 1:2.28
```
- rpcsvc-proto

```
Preparing to unpack rpcsvc-proto_1.4.2-1_loongarch64.deb ...
Unpacking rpcsvc-proto (1.4.2-1) ...
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/bin/rpcgen', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/bootparam_prot.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/bootparam_prot.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/key_prot.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/key_prot.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/klm_prot.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/klm_prot.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/mount.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/mount.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nfs_prot.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nfs_prot.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nlm_prot.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/nlm_prot.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rex.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rex.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rquota.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rquota.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rstat.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rstat.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rusers.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rusers.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/sm_inter.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/sm_inter.x', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/spray.h', which is also in package glibc 1:2.28
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/spray.x', which is also in package glibc 1:2.28
Setting up rpcsvc-proto (1.4.2-1) ...
```
