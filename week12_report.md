# BuildKit

# Weekly schedule
- Monday  
host's power shutdown

- Tuesday  
decide to create LoongArch64 BuildKit tarball, read aoscbootstrap-rs https://github.com/AOSC-Dev/aoscbootstrap and debootstrap recipe https://github.com/AOSC-Dev/scriptlets/tree/master/debootstrap

- Wednesday  
read aoscbootstrap-rs again and created a temporary LoongArch64 BuildKit tarball

- Thursday  
Tried QEMU-LoongArch64 https://github.com/loongson/qemu/pull/3 with BuildKit.  
As expected, it will not enter bash, just like tring chroot into the new CLFS on Loongnix 20.

- Friday
Thinking about ways to backup all the debs and try to upload them to my 5T onedrive student account by rclone.  
Compiling rclone should be easy on Loongnix, but there are still some go modules need to port plus modify the go.mod and proxy.  
I change the go proxy like this.  
`echo "export GOPROXY=https://goproxy.cn" >> ~/.profile && source ~/.profile`  
It seems that `bbolt` and `gopsutil` need to be ported, look at errors for more details.

# Errors
- `rclone`
```
../go/pkg/mod/go.etcd.io/bbolt@v1.3.5/bolt_unix.go:66:15: undefined: maxMapSize
../go/pkg/mod/go.etcd.io/bbolt@v1.3.5/db.go:127:13: undefined: maxMapSize
../go/pkg/mod/go.etcd.io/bbolt@v1.3.5/db.go:400:12: undefined: maxMapSize
../go/pkg/mod/go.etcd.io/bbolt@v1.3.5/db.go:418:10: undefined: maxMapSize
../go/pkg/mod/go.etcd.io/bbolt@v1.3.5/db.go:419:8: undefined: maxMapSize
../go/pkg/mod/go.etcd.io/bbolt@v1.3.5/tx.go:533:12: undefined: maxAllocSize
../go/pkg/mod/go.etcd.io/bbolt@v1.3.5/tx.go:534:10: undefined: maxAllocSize
../go/pkg/mod/go.etcd.io/bbolt@v1.3.5/unsafe.go:27:12: undefined: maxAllocSize

../go/pkg/mod/github.com/shirou/gopsutil/v3@v3.21.3/host/host_linux.go:98:22: undefined: sizeOfUtmp
../go/pkg/mod/github.com/shirou/gopsutil/v3@v3.21.3/host/host_linux.go:103:14: undefined: sizeOfUtmp
../go/pkg/mod/github.com/shirou/gopsutil/v3@v3.21.3/host/host_linux.go:105:9: undefined: utmp
```
- `qt-5`
```
In file included from ../3rdparty/double-conversion/bignum.h:31,
                 from ../3rdparty/double-conversion/bignum.cc:28:
../3rdparty/double-conversion/include/double-conversion/utils.h:121:2: error: #error Target architecture was not detected as supported by Double-Conversion.
 #error Target architecture was not detected as supported by Double-Conversion.
  ^~~~~
```

# experiments
I also use [xchroot](https://www.elstel.org/xchroot/#downloads) to enable BuildKit X11 forwarding to Yukoaioi and then forward to my laptop.  
![xterm-crop](https://user-images.githubusercontent.com/53088781/134063317-a79704e1-c92b-4df2-ae93-bad27d5bc8ba.png)

I also take a look at qt-5 risc-v patch on Debian, it seems that I do not need to actually write code for it, just register the architecture is enough.  
However, double-conversion needs to be ported, If I disable it, configure will say `Your C library does not provide sscanf_l or snprintf_l.`
