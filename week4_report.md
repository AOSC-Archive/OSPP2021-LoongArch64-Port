# GCC status
这周得到了龙芯电报群友的帮助，其中 phorcys 提出了关于 `ABI_SPEC` 的 bug，此 bug 会导致出现 `/liblp64`， 解决其 bug 的 patch 如下：  
```
--- a/gcc/config/loongarch/loongarch.h	2021-07-22 10:53:41.270874923 +0400
+++ b/gcc/config/loongarch/loongarch.h	2021-07-22 10:53:17.130875822 +0400
@@ -463,8 +463,8 @@
 
 /* from N_LARCH */
 #define ABI_SPEC	\
-  "%{mabi=lp32:}"	\
-  "%{mabi=lp64:64}"	\
+  "%{mabi=lp32:lp32}"	\
+  "%{mabi=lp64:lp64}"	\
 
 #define STARTFILE_PREFIX_SPEC	\
   "/lib" ABI_SPEC "/ "		\
```
另外本人也制作了关于 `MULTILIB_ISA_DEFAULT` 的 patch，具体出现的编译问题在 week3_report 有记录。patch 如下：
```
--- a/gcc/config/loongarch/loongarch.h	2021-07-22 00:01:29.486851650 +0400
+++ b/gcc/config/loongarch/loongarch.h	2021-07-22 00:03:11.696855769 +0400
@@ -328,10 +328,8 @@
 #endif
 
 #ifndef MULTILIB_ISA_DEFAULT
-#if LARCH_ISA_DEFAULT == 65
 #define MULTILIB_ISA_DEFAULT "loongarch64"
 #endif
-#endif
 
 #ifndef LARCH_ABI_DEFAULT
 #define LARCH_ABI_DEFAULT ABILP32
 ```
 
 # CLFS status
 本周，在群友的讨论下，发现了孙海勇老师的 CLFS-for_LoongArch，链接：https://github.com/sunhaiyong1978/CLFS-for-LoongArch  
 本人也做了一些小修改，port 链接：https://github.com/xyq1113723547/CLFS-for-LoongArch  
 于是乎，本人便先放弃了 LFS Development 的构建，转用 CLFS，用的自己的 x64 笔记本构建 cross-compiler 。  
 得益于本人 x64 CPU 的性能，编译速度很快，CLFS 也在 3A5000 机器上面成功运行。  
 当然，也因为编译环境和 configure 的问题，也有软件包没有成功交叉编译，导致有 x64 的 ELF binary，目前已在 3A5000 再次编译过这些软件，并覆盖安装。  
 
 # Error and solutions
1. 在 CLFS GCC pass 3 中会有如下错误：`make[1]: *** [Makefile:13338: configure-target-libgfortran] Error 1`  
 所以在 enable-languages 只写有 c,c++  
2. 在 CLFS 交叉编译 m4 时会报错，error: 
 ` 'struct vma_struct' has no member named 'is_near_this'
 : vma.is_near_this (addr, &vma))`
 解决方法就是 3A5000 本地编译
3. 本地编译 m4 也出现了错误，`error "Assumed value of MB_LEN_MAX wrong"` 在 LFS 里面有 FAQ 来解决，是我在用孙老师的 CLFS 没有注意到要更新 headers 导致的
 [LFS FAQ](https://www.linuxfromscratch.org/lfs/faq.html#m4-mb-len-max-wrong)  
4. 在完成编译 OpenSSL 之后，编译 Wget 会有找不到 pthread 和 openssl 的动态库的情况，导师提出在 configure 添加`ssl=openssl LIBS="-lpthread"`，编译完成
5. 在编译 Cmake 时，也会有跟编译 Wget 差不多的报错，也是链接不到 pthreads 的动态库 大概如下：
 ```
 /bin/ld: warning: libpthread.so.0, needed by /lib64/../lib/librt.so, not found (try using -rpath or -rpath-link)
 /bin/ld: /lib64/../lib/librt.so: undefined reference to `pthread_cond_timedwait@GLIBC_2.3.2'
 /bin/ld: /lib64/../lib/librt.so: undefined reference to `__pthread_barrier_init@GLIBC_PRIVATE'
 /bin/ld: /lib64/../lib/librt.so: undefined reference to `pthread_cond_signal@GLIBC_2.3.2'
 ```
 尝试了几种方式，如 `CFLAGS=“ -lpthread”`，`CXXFLAGS=“ -lpthread”`，可以解决 configure 问题，之后又发现 make 时候没有找到 libcurl.so, 最终解决方案是  
 ```
 export LD_LIBRARY_PATH=/lib
 ```
 # general workflow and plan
  编译 Perl，pkg-config 以支持 dpkg，编译 libffi 以支持 Python3, 编译 Python3 以支持 ACBS，其中因为 libffi 有 LoongArch port patch，本人已融合上游做了 port：https://github.com/xyq1113723547/libffi  
  Python3 编译完成。  
  下一步则是打 core 包，手动解基本系统包依赖，其中 Binutils, GCC, Glibc, 都是有 LoongArch port，但是版本号大概是两年前，跟 Debian 10 保持一样。同时因为敏感原因，不便再公开分享。  
  Linux kernel 5.13 则是有初步的公开官方 port，详见 https://github.com/loongson/linux  
  后续应该会有不少软件需要架构移植，以及移植软件版本太老，移植不完整，魔改太多，没有开源等问题，可能会导致会和现在 ABBS tree 有较大区别，所以考虑先 port ABBS tree，修改关于 LoongArch64 的编译选项，然后再打 core 包。  
  同时，详细查看 ACBS 等工具，考虑自动化解析依赖并按其顺序自动打 LoongArch64 的包。  
  Bootstrap buildkit 之类操作也需要工具，需要跟导师沟通确定之后方案。
  
# 沟通
 在 CLFS 构建过程中，以及之前 LFS 构建中，由于沟通时间或频率不够，导师可能会觉得我工作时间不够，或者是目标不明确，在做无用功。  
 所以目前导师要求每日做报告。
 
 # 工作时间
 自己要求自己一周30小时，周末写文档，去 Discord 交流。

 
