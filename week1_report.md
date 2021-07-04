# Week 1 report

## 系统环境

近日， linux kernel LoongArch 源码已经上传到了 [git.kernel.org](https://git.kernel.org/pub/scm/linux/kernel/git/chenhuacai/linux-loongson.git/?h=loongarch-next)，虽然目前还没有合并上游，不过至少说明kernel基本可用。  
目前 AOSC 购置的 3A5000 主机用的是 [Loongnix](os.loongnix.cn) LoongArch rc1 版，不幸的是，于7月4日发现[官方软件源](http://pkg.loongnix.cn:8080/loongnix/)已无法连接。 据龙芯某群友透露，应当是分发了侵权软件，其中有敏感的SIMD指令，导致的撤回行为。  
目前情况基本意味着，下游非官方开发者在一段时间内须自行发展其软件生态，其中包括 OSPP 2021 AOSC 开展的独立系统移植计划。

## 编译环境

因为龙芯公司的决策，敏感时期以及知识产权纠纷问题，目前网络上已无公开的关于 LoongArch 的 GCC，Binutils，Glibc 的完整源码包。 目前本人所使用的工具链也是基于龙芯群友小范围分享的源码包所构建。不过常用的检验及编译系统软件，如 file, config.guess, meson, automake 等，已经合并到了上游，详见 https://loongarch.dev。 这让系统编译工作减少了许多不必要的手动干预。

## Day 1

因为上传的软件包用的压缩包。先遇到的是Loongnix的p7zip的error，*Can't load '/usr/lib/p7zip/7z.dll'* ，于是手动编译了[p7zip](https://github.com/jinfeihan57/p7zip)。  
之后便开始正式编译 LFS。 首先遇到了 binutils 的一个bug，https://sourceware.org/bugzilla/show_bug.cgi?id=22618。 apt install flex 后，make clean 然后再 make 就行。  
因为用的是binutils-gdb 2.36，依赖于GMP，所以出现了 *configure: error: GMP is missing or unusable* 所以刚开始先跳过了，用的是Loongnix泄露的binutils 2.31 LoongArch64 源码包。
然后再编译GCC 11。  
编译GCC时，需要把GMP的源码包解压到GCC源码包，会有如下error，  

```
checking build system type... Invalid configuration `loongarch64-unknown-linux-gnu': machine `loongarch64-unknown' not recognized
```

解决方案是先 `apt install libgmp-dev libmpc-dev libmpfr-dev`, 再用 `--with-gmp-include` 等参数来链接库，解决 GMP 依赖问题。  
然而并没有成功编译GCC，出现了interal compiler error, 原因在于binutils 2.31 和 GCC 11.1 版本差别过大，并不兼容。 于是又回头再次编译 binutils 2.36， 同样采用 `--with-gmp-include=PATH --with-gmp-lib=PATH` 来跳过configure。
之后又有GCC error，如下：
```
sources/gcc/build1/loongarch64-lfs-linux-gnu/libgcc':
configure: error: cannot compute suffix of object files: cannot compile。
```
解决方案是 在～/.bashrc 里面加 `LD_LIBRARY_PATH=/usr/lib/loongarch64-linux-gnu/` 之后GCC pass 1 编译成功。

## Day 2

编译 Glibc 2.33 发现了两个bug，一个是GCC上游已经记录，https://gcc.gnu.org/bugzilla/show_bug.cgi?id=98512 然而于 https://gcc.gnu.org/git/gitweb.cgi?p=gcc.git;h=6feb628a706e86eb3f303aff388c74bdb29e7381 的patch并不能用  
原因是那个patch是针对上游 git 的 GCC，patch哪怕只有一部分失败，也要记得需要用`patch -R` 来撤销,之后果然就有奇怪的编译error源自于此，之后才意识到， 重新编译了GCC和Glibc， 消耗了许多时间。  
之后选择的则只是 Glibc 的一个下游 work around patch，http://git.yoctoproject.org/cgit/cgit.cgi/poky/tree/meta/recipes-core/glibc/glibc/0032-string-Work-around-GCC-PR-98512-in-rawmemchr.patch?id=43ca8ad614cf245c1824d05d850da51f0cb29d11  

## Day 3 & 4
然而还有另外的一个bug 在 Glibc make install 阶段里面，于 https://yhbt.net/lore/all/7462f741-d7d8-57f2-0fe9-c21b4b2829eb@synopsys.com/T/#m74fcb70dc0e4985e4b3f83dae4a3019b31bafb24 发现，关键评论 `FWIW regen-ulps simply barfs so these need to be hand edited`，error如下
```
make[2]: Entering directory '/mnt/lfs/sources/glibc/manual'
pwd=`pwd`; \
python3 -B ../math/gen-libm-test.py -s $pwd/.. -m /mnt/lfs/sources/glibc/build/manual/libm-err-tmp
Traceback (most recent call last):
  File "../math/gen-libm-test.py", line 686, in <module>
    main()
  File "../math/gen-libm-test.py", line 674, in main
    all_ulps = read_all_ulps(args.srcdir)
  File "../math/gen-libm-test.py", line 262, in read_all_ulps
    all_ulps[name].read(os.path.join(dirpath, 'libm-test-ulps'))
  File "../math/gen-libm-test.py", line 166, in read
    raise ValueError('bad ulps line: %s' % line)
ValueError: bad ulps line: ifloat: 1
```
需要手动修改源代码，此时通过导师帮助，用`sed -e '/^idouble:/d' -e '/^ifloat:/d' -e '/^ildouble/d' -i sysdeps/loongarch/lp64/libm-test-ulps` 解决。  
之后在 LFS readelf dummy.c 里面出现，`[Requesting program interpreter: /liblp64/ld.so.1]`，这个liblp64 是 loongarch64 的定义，但是一般来说改成 lib64 才符合POSIX规范。  
是群友的GCC port不完全，有个泄露的patch没有打上，打上之后便是跟Loongnix中一样：`[Requesting program interpreter: /lib64/ld.so.1]`。
编译m4, ncurses等一系列软件的时候config.guess 版本过老，于是直接在`build-aux`或者其他装有config.guess和config.sub的folder里面去替代, 如下。
```
wget -O config.guess 'https://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess;hb=HEAD'
wget -O config.sub 'https://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.sub;hb=HEAD' 
```
在 Binutils-gdb 2.36 pass 2 里面编译gdb-server 的时候有 `cannot find linux-loongarch-low.o`, 之后 用`--withoutgdb --withoutgdb-server` 先跳过，这个阶段gdb并不重要。
然而之后又有LD链接库时找不到文件，导师用symbolic link 变相修改了 lib path，编译就通过了。

在GCC pass 2 遇到了比较严重的问题，下周准备解决，或者考虑用官方的二进制build-tools https://github.com/loongson/build-tools。
![Screenshot from 2021-07-04 11-39-42](https://user-images.githubusercontent.com/53088781/124392859-a739ec00-dd08-11eb-992e-605a6c9e3d63.png)
