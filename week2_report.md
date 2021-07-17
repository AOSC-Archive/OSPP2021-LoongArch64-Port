# Personal affairs

This week, I am very busy with my course and writting long programs for assignment. Thus, I actually have taken 3 days off.   

# Previous GCC status  

The GCC 11.1 xen0n ported version, has some problems. What I know of is that it did not contain a patch for disable liblp64.  
Plus, I did not successfully compile GCC pass 2 in LFS.
xen0n gets warnings from Loongson for disclosing the source code. Also, I did not get any help from him after he remove his port on Github.  
Thus, I decide to use the old GCC, binutils and glibc to build toolchains, that were leaked in Loongnix 20 rc1 in previous month.

# Current GCC status
  
The GCC 8.3.0 source package, which Debian 10 uses, is heavily modified for LoongArch.   
The source package contains both old patches in Debian and new patches for LoongArch.   
All patches are located in `/usr/src/gcc-8/patches`, except for libffi patch, which are located in `/usr/src/gcc-8/` or `/usr/src/gcc-8/libffi`, both of them are the same.  
Though, not all changes are in patches, there are many files which are directly written and modified for LoongArch. 
The format for source package is 2.0 and .deb, which is old and can not be processed by dpkg-source. I need to apply patches manually.  
The sample patch script are as follows:  
```
for i in $(cat series); do patch -Np2 -d /usr/src/gcc-8/gcc-8.3.0 < $i; done
patch -Np1 -d /usr/src/glibc/glibc-2.28 < "patches"
```

# patch status

All GCC patches have successfully applied.
Two patches are FAILED in binutils, others already applied:  
```
0002-add-fcc-branch-pseudo-instruction-to-cooperate-with-.patch
VDP2.[]U.[]U-change-into-VDP2.[].[]U.patch
```
One patch in glibc can be applied but comment out in series, others already applied:  
```
loongarch/0001-add-loongarch-process_elf_file-in-readelflib.c.patch
```


# Trials and failures  

Before the project offically begin, I first applied some patches directly without sorting. This is not right. **Applying multiple patches needs order!**  
Even just trying one patch file, which seems to have small series number and older timestamp, is not guranteed to work as intended.   
Since there may be other patches that current patch file is dependent on.  
  
There are more systematic tools, like quilt, to apply patches in a VCS way. However I think that is over complicated just for applying the patches.  
Since what I get are debian source packages, the correct series are in `debian/patches`. The `series` file contains the correct order to apply patches.

# Achievement

Using the patch for libffi, I have successfully merged with the upstream on my repository of Github.  
https://github.com/xyq1113723547/libffi
