# GCC status
The GCC LoongArch port source code is finally publicly available at Github, along with Glibc and Binutils.  
https://github.com/loongson/gcc/tree/loongarch-12  
https://github.com/loongson/binutils-gdb/tree/loongarch-2_37  
https://github.com/loongson/glibc/tree/loongarch_2_33  

There is a  cross-compiler bug appears in GCC development version and git main branch.  
https://gcc.gnu.org/bugzilla//show_bug.cgi?id=100017  
The error appears the same in the log error in the link above.  
I copy the patch from comment 12, and finish compiling.

# ACBS build status

I finished building the deb packages for old GCC, binutils, glibc by using the old CLFS toolchain, but these packages maybe not be useful anymore.  
Besides, I am not aware that the ACBS will build packages and *install* them on the machine immediately.  
Even though I build glibc git main branch deb package, the old CLFS seems to be broken because of new libc.so.6.

# CLFS status
The CLFS for LoongArch64 still works, though I have to completely rebuild the toolchains and build basic tools from it.  
The reason for completely rework is that the ABI for LoongArch ld interpreter in Glibc is changed and not compatible with old one.  
like running the gcc on Loongnix compiled with the new toolchain will get looks like this:
```
root@yukoaioi:/mnt/lfs/bin# ./gcc -v
./gcc: /lib/libc.so.6: version `GLIBC_2.32' not found (required by ./gcc)
./gcc: /lib/libc.so.6: version `GLIBC_2.33' not found (required by ./gcc)
```

I have tried to let softwares compiled by the new toolchain to run on Loongnix like using `export LD_LIBRARY_PATH=/mnt/lfs/lib/ `, but it will cause segmentation fault.  

# cross-toolchain status

The cross-compile flags in CLFS still works, and I have successfully build cross-toolchain and a minimal clfs root file system on x86_64.  

# personal affairs

The final assignment and exam is coming, so I did not having much time to do porting and write report in time.
