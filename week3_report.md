# GCC status

All patches in GCC are applied successfully.  
However, based on current status, I need to maintain a somewhat separate fork for GCC LoonArch port since I modified source code a little.

# Errors and solutions

1. MULTILIB_ISA_DEFAULT is undefined.  
I manually deleted line 331 in gcc/config/loongarch.h, where `#if LARCH_ISA_DEFAULT == 65` seems useless, and LARCH_ISA_DEFAULT is not defined in current header, which causes error for not define MULTILIB_ISA_DEFAULT.

2. segmentation fault in gcc/testsuite/selftests  
Add --with-abi=lp64, --with-arch=loongarch64 flags in configure suggested by flygoat

3. `Configuring in loongarch64-lfs-linux-gnu/libada-sjlj  
configure: error: cannot find sources (Makefile.in) in ../../../libada-sjlj
make[1]: *** [Makefile:13081: configure-target-libada-sjlj] Error 1`  
Add --disable-libada in configure

4. 
```
/mnt/lfs/usr/include/asm/ptrace.h:29:8: note:   candidate expects 0 arguments, 1 provided
/mnt/lfs/usr/include/asm/ptrace.h:29:8: note: candidate: 'constexpr pt_regs::pt_regs(const pt_regs&)'
/mnt/lfs/usr/include/asm/ptrace.h:29:8: note:   no known conversion for argument 1 from 'int' to 'const pt_regs&'
/mnt/lfs/usr/include/asm/ptrace.h:29:8: note: candidate: 'constexpr pt_regs::pt_regs(pt_regs&&)'
/mnt/lfs/usr/include/asm/ptrace.h:29:8: note:   no known conversion for argument 1 from 'int' to 'pt_regs&&'
make[2]: *** [Makefile:1641: loongarch-linux-watch.o] Error 1
make[2]: *** Waiting for unfinished jobs....
make[2]: *** [Makefile:1626: loongarch-linux-nat.o] Error 1
make[2]: Leaving directory '/mnt/lfs/sources/binutils-2.31.1/build2/gdb'
make[1]: *** [Makefile:10421: all-gdb] Error 2
```  
Add --disable-gdb --disable-gdb-server in binutils configure

5.
![Screenshot from 2021-07-16 21-26-00](https://user-images.githubusercontent.com/53088781/126033119-18d294ec-937b-4f7c-8f28-841e2d93c8cc.png)

Based on https://www.mail-archive.com/gcc-bugs@gcc.gnu.org/msg548526.html given by instructor, I manually comment out the functions that are redefined.

6.
```
make subdir=posix -C ../posix ..=../ objdir=/mnt/lfs/sources/glibc/glibc-2.28/build -f Makefile -f ../elf/rtld-Rules rtld-all rtld-modules='rtld-uname.os rtld-_exit.os rtld-getpid.os rtld-environ.os'
ldconfig.c:84:19: error: redefinition of ‘system_dirs’
 static const char system_dirs[] = SYSTEM_DIRS;
                   ^~~~~~~~~~~
ldconfig.c:69:19: note: previous definition of ‘system_dirs’ was here
 static const char system_dirs[] = SYSTEM_DIRS;
                   ^~~~~~~~~~~
ldconfig.c:85:21: error: redefinition of ‘system_dirs_len’
 static const size_t system_dirs_len[] =
                     ^~~~~~~~~~~~~~~
ldconfig.c:70:21: note: previous definition of ‘system_dirs_len’ was here
 static const size_t system_dirs_len[] =
                     ^~~~~~~~~~~~~~~
ldconfig.c:70:21: error: ‘system_dirs_len’ defined but not used [-Werror=unused-const-variable=]
ldconfig.c:69:19: error: ‘system_dirs’ defined but not used [-Werror=unused-const-variable=]
 static const char system_dirs[] = SYSTEM_DIRS;
                   ^~~~~~~~~~~
cc1: all warnings being treated as errors
make[2]: *** [../o-iterator.mk:9：/mnt/lfs/sources/glibc/glibc-2.28/build/elf/ldconfig.o] 错误 1
```
I manually comment out the duplicate variable definitions.

