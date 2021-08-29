# progess for building packages

## need LLVM or rustc
- librsvg -> graphviz -> vala -> gtk3
- gc -> guile -> graphviz
## gtk-2

before build gtk-2 and after building gdk-pxibuf, run `gdk-pixbuf-query-loaders --update-cache`, otherwise, the following error will appear when building gtk-2  
```
failed to load "./apple-red.png": Couldn't recgognize the image file
format for './apple-red.png'
```
The solution is taken from `https://lists.linuxfromscratch.org/sympa/arc/blfs-support/2012-06/msg00037.html`

during installing the gtk-2 deb, the error will appear
```
gtk-query-immodules-2.0: error while loading shared libraries: libpango-1.0.so.0: cannot open shared object file: No such file or directory
```
strangely, libpango-1.0.so.0 is in /usr/lib/loongarch64-linux-gnu  
I need to move all *.so to /usr/lib

## gtk-3

gnutls -> glib-networking ->libsoup -> rest -> gtk-3

## qt-4
no icu, postgresql
not internal webkit

## qt-5
error  
```
make[3]: *** [Makefile:28238: .obj/bignum.o] Error 1
make[3]: *** Waiting for unfinished jobs....
In file included from ../3rdparty/double-conversion/cached-powers.cc:32:
../3rdparty/double-conversion/include/double-conversion/utils.h:121:2: error: #error Target architecture was not detected as supported by Double-Conversion.
 #error Target architecture was not detected as supported by Double-Conversion.
  ^~~~~
make[3]: *** [Makefile:28246: .obj/bignum-dtoa.o] Error 1
make[3]: *** [Makefile:28252: .obj/cached-powers.o] Error 1
make[3]: Leaving directory '/sources/qt-everywhere-src-5.15.2/qtbase/src/corelib'
make[2]: *** [Makefile:198: sub-corelib-make_first] Error 2
make[2]: Leaving directory '/sources/qt-everywhere-src-5.15.2/qtbase/src'
make[1]: *** [Makefile:51: sub-src-make_first] Error 2
make[1]: Leaving directory '/sources/qt-everywhere-src-5.15.2/qtbase'
make: *** [Makefile:87: module-qtbase-make_first] Error 2

```
# change beyond script

See details in `https://github.com/xyq1113723547/aosc-os-abbs/tree/stable`
- tk
`chmod -v -x /usr/lib/libtkstub8.6.a`

- libproxy
`chmod -v +x $PKGDIR/usr/lib/libproxy.so.1.0.0`

- snappy

- mariadb

chmod -v +x /var/cache/acbs/build/acbs.0t04ndgh/mariadb-10.6.4/abdist/usr/lib/libmariadbd.so.19  
chmod -v +x /var/cache/acbs/build/acbs.0t04ndgh/mariadb-10.6.4/abdist/usr/lib/libmariadb.so.3

-libical
chmod -v +x $PKGDIR/usr/lib/libical.so.3.0.8
chmod -v +x $PKGDIR/usr/lib/libicalvcal.so.3.0.8
chmod -v +x $PKGDIR/usr/lib/libical_cxx.so.3.0.8
chmod -v +x $PKGDIR/usr/lib/libicalss.so.3.0.8
chmod -v +x $PKGDIR/usr/lib/libical-glib.so.3.0.8
chmod -v +x $PKGDIR/usr/lib/libicalss_cxx.so.3.0.8

- aom
- libcue
- libftdi
- sdl2
- vulkan-loader
- libldac
- openal-soft
- soxr

## use retro config to build

- mesa cairo change retro config

meson.build did not cover LoongArch, and fall into "none"

- gobject-introspection retro
- ghostscript 
- ijs


## change so permission

- libjpeg-turbo
- libcork
- libogg

## add custom build script

- x11-font  
build font-util manually, then change builddep and build x11-font
- libcork
- libipset
- libwatcom
use `LD_PRELOAD=$PWD/libwacom.so.2` ninja or manually install it and then use ACBS
- celt  
copy new config.*

# potential improvement to abbs tree
add pyyaml dependency to desktop-base


# useful environment varible
export CMAKE_LIBRARY_PATH=/usr/lib/loongarch64-linux-gnu/
export LD_LIBRARY_PATH=/usr/lib/loongarch64-linux-gnu/
export LD_PRELOAD=/usr/lib/nvidia-384/loongarch64-linux-gnu/

- tmp wayaround for gdk-pixbuf
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/sources/gdk-pixbuf-2.42.4/build/gdk-pixbuf/

otherwise:  
```
[117/150] Generating resources.c with a custom command
FAILED: tests/resources.c 
/bin/python3 /var/cache/acbs/build/acbs.ygcgz554/gdk-pixbuf-2.42.4/build-aux/gen-resources.py --glib-compile-resources=/bin/glib-compile-resources --pixdata=/var/cache/acbs/build/acbs.ygcgz554/gdk-pixbuf-2.42.4/abbuild/gdk-pixbuf/gdk-pixbuf-pixdata --loaders=/var/cache/acbs/build/acbs.ygcgz554/gdk-pixbuf-2.42.4/abbuild/gdk-pixbuf/loaders.cache --sourcedir=/var/cache/acbs/build/acbs.ygcgz554/gdk-pixbuf-2.42.4/tests --source ../tests/resources.gresource.xml tests/resources.c
/var/cache/acbs/build/acbs.ygcgz554/gdk-pixbuf-2.42.4/abbuild/gdk-pixbuf/gdk-pixbuf-pixdata: error while loading shared libraries: libgdk_pixbuf-2.0.so.0: cannot open shared object file: No such file or directory
```
# Warning
- libglvnd
```
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/EGL/egl.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/EGL/eglext.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/EGL/eglplatform.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GL/gl.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GL/glcorearb.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GL/glext.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GL/glx.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GL/glxext.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES/egl.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES/gl.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES/glext.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES/glplatform.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES2/gl2.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES2/gl2ext.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES2/gl2platform.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES3/gl3.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES3/gl31.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES3/gl32.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES3/gl3ext.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/GLES3/gl3platform.h', which is also in package mesa 1:21.1.4-2
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/KHR/khrplatform.h', which is also in package mesa 1:21.1.4-2
Setting up libglvnd (1.3.2-1) ...

```

- libgudev 
```
Unpacking libgudev (236) ...
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/gudev-1.0/gudev/gudev.h', which is also in package systemd 1:248.3-1
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/gudev-1.0/gudev/gudevclient.h', which is also in package systemd 1:248.3-1
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/gudev-1.0/gudev/gudevdevice.h', which is also in package systemd 1:248.3-1
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/gudev-1.0/gudev/gudevenumerator.h', which is also in package systemd 1:248.3-1
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/gudev-1.0/gudev/gudevenums.h', which is also in package systemd 1:248.3-1
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/gudev-1.0/gudev/gudevenumtypes.h', which is also in package systemd 1:248.3-1
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/gudev-1.0/gudev/gudevtypes.h', which is also in package systemd 1:248.3-1
Setting up libgudev (236) ...
```
# error

- pango
```
/bin/help2man --no-info --section=1 --help-option=--help '--name="Pango text viewer"' --output=utils/pango-view.1 /sources/pango-1.48.4/build/utils/pango-view
```
```
