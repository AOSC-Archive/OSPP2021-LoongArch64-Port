# fail to build packages in buildkit requirement

## NO LLVM and/or Rust

- fcron postfix <- postgresql <- llvm-runtime
- polkit no js-78 <- no llvm rustc
- atm need llvm rustc

# need port LoongArch source code
- liburcu -> multipath-tools
- libunwind -> kernel-tools

## postpone build for dependency
- p7zip no wxgtk3
- bluez <- cups <- avahi <- gobject-introspection <- cairo
- networkmanager <- modemmanager <- libmbim, libgudev <- gobject-introspection
- bind <- geoip-api-c

## didn't find source code link

- geoip-api-c


## build error

- nghttp2 <- jemalloc  
```
src/arena.c:2022:34: error: request for member 'bins_large' in something not a structure or union
        tcache->bins_small, tcache->bins_large);
                                  ^~
In file included from include/jemalloc/internal/ql.h:4,
                 from include/jemalloc/internal/tsd.h:8,
                 from include/jemalloc/internal/mutex.h:6,
                 from include/jemalloc/internal/extent_structs.h:6,
                 from include/jemalloc/internal/jemalloc_internal_includes.h:54,
                 from src/arena.c:3:
src/arena.c:2024:15: error: request for member 'cache_bin_array_descriptor' in something not a structure or union
        &tcache->cache_bin_array_descriptor, link);
               ^~
include/jemalloc/internal/qr.h:22:3: note: in definition of macro 'qr_before_insert'
  (a_qr)->a_field.qre_prev = (a_qrelm)->a_field.qre_prev;  \
   ^~~~
src/arena.c:2023:4: note: in expansion of macro 'ql_tail_insert'
    ql_tail_insert(&arena->cache_bin_array_descriptor_ql,
    ^~~~~~~~~~~~~~
src/arena.c:2024:15: error: request for member 'cache_bin_array_descriptor' in something not a structure or union
        &tcache->cache_bin_array_descriptor, link);
               ^~
include/jemalloc/internal/qr.h:23:3: note: in definition of macro 'qr_before_insert'
  (a_qr)->a_field.qre_next = (a_qrelm);    \
   ^~~~
src/arena.c:2023:4: note: in expansion of macro 'ql_tail_insert'
    ql_tail_insert(&arena->cache_bin_array_descriptor_ql,
    ^~~~~~~~~~~~~~
src/arena.c:2024:15: error: request for member 'cache_bin_array_descriptor' in something not a structure or union
        &tcache->cache_bin_array_descriptor, link);
               ^~
include/jemalloc/internal/qr.h:24:3: note: in definition of macro 'qr_before_insert'
  (a_qr)->a_field.qre_prev->a_field.qre_next = (a_qr);  \
   ^~~~
src/arena.c:2023:4: note: in expansion of macro 'ql_tail_insert'
    ql_tail_insert(&arena->cache_bin_array_descriptor_ql,
    ^~~~~~~~~~~~~~
src/arena.c:2024:15: error: request for member 'cache_bin_array_descriptor' in something not a structure or union
        &tcache->cache_bin_array_descriptor, link);
               ^~
include/jemalloc/internal/qr.h:24:48: note: in definition of macro 'qr_before_insert'
  (a_qr)->a_field.qre_prev->a_field.qre_next = (a_qr);  \
                                                ^~~~
src/arena.c:2023:4: note: in expansion of macro 'ql_tail_insert'
    ql_tail_insert(&arena->cache_bin_array_descriptor_ql,
    ^~~~~~~~~~~~~~
src/arena.c:2024:15: error: request for member 'cache_bin_array_descriptor' in something not a structure or union
        &tcache->cache_bin_array_descriptor, link);
               ^~
include/jemalloc/internal/qr.h:25:33: note: in definition of macro 'qr_before_insert'
  (a_qrelm)->a_field.qre_prev = (a_qr);    \
                                 ^~~~
src/arena.c:2023:4: note: in expansion of macro 'ql_tail_insert'
    ql_tail_insert(&arena->cache_bin_array_descriptor_ql,
    ^~~~~~~~~~~~~~
src/arena.c:2024:15: error: request for member 'cache_bin_array_descriptor' in something not a structure or union
        &tcache->cache_bin_array_descriptor, link);
               ^~
include/jemalloc/internal/qr.h:17:34: note: in definition of macro 'qr_next'
 #define qr_next(a_qr, a_field) ((a_qr)->a_field.qre_next)
                                  ^~~~
src/arena.c:2023:4: note: in expansion of macro 'ql_tail_insert'
    ql_tail_insert(&arena->cache_bin_array_descriptor_ql,
    ^~~~~~~~~~~~~~
In file included from include/jemalloc/internal/assert.h:2,
                 from include/jemalloc/internal/bit_util.h:4,
                 from include/jemalloc/internal/bitmap.h:5,
                 from include/jemalloc/internal/arena_structs_a.h:4,
                 from include/jemalloc/internal/jemalloc_internal_includes.h:53,
                 from src/jemalloc.c:3:
src/jemalloc.c: In function 'ixallocx_prof':
src/jemalloc.c:2796:40: error: 'LARGE_MAXCLASS' undeclared (first use in this function); did you mean 'PAGE_MASK'?
   assert(usize_max > 0 && usize_max <= LARGE_MAXCLASS);
                                        ^~~~~~~~~~~~~~
include/jemalloc/internal/util.h:33:43: note: in definition of macro 'unlikely'
 #  define unlikely(x) __builtin_expect(!!(x), 0)
                                           ^
src/jemalloc.c:2796:3: note: in expansion of macro 'assert'
   assert(usize_max > 0 && usize_max <= LARGE_MAXCLASS);
   ^~~~~~
src/jemalloc.c: In function 'xallocx':
src/jemalloc.c:2861:22: error: 'LARGE_MAXCLASS' undeclared (first use in this function); did you mean 'PAGE_MASK'?
  if (unlikely(size > LARGE_MAXCLASS)) {
                      ^~~~~~~~~~~~~~
include/jemalloc/internal/util.h:33:43: note: in definition of macro 'unlikely'
 #  define unlikely(x) __builtin_expect(!!(x), 0)
                                           ^
src/jemalloc.c: In function 'dallocx':
src/jemalloc.c:2930:2: error: unknown type name 'tcache_t'; did you mean 'tcaches_t'?
  tcache_t *tcache;
  ^~~~~~~~
  tcaches_t
src/jemalloc.c: In function 'sdallocx':
src/jemalloc.c:2992:2: error: unknown type name 'tcache_t'; did you mean 'tcaches_t'?
  tcache_t *tcache;
  ^~~~~~~~
  tcaches_t
src/jemalloc.c:3017:3: warning: implicit declaration of function 'isfree'; did you mean 'free'? [-Wimplicit-function-declaration]
   isfree(tsd, ptr, usize, tcache, false);
   ^~~~~~
   free
In file included from include/jemalloc/internal/jemalloc_internal_includes.h:86,
                 from src/arena.c:3:
include/jemalloc/internal/extent_inlines.h: In function 'extent_nfree_get':
include/jemalloc/internal/extent_inlines.h:113:1: warning: control reaches end of non-void function [-Wreturn-type]
 }
 ^
include/jemalloc/internal/extent_inlines.h: In function 'extent_sn_get':
include/jemalloc/internal/extent_inlines.h:76:1: warning: control reaches end of non-void function [-Wreturn-type]
 }
 ^
In file included from include/jemalloc/internal/assert.h:2,
                 from include/jemalloc/internal/bit_util.h:4,
                 from include/jemalloc/internal/bitmap.h:5,
                 from include/jemalloc/internal/arena_structs_a.h:4,
                 from include/jemalloc/internal/jemalloc_internal_includes.h:53,
                 from src/jemalloc.c:3:
src/jemalloc.c: In function 'nallocx':
src/jemalloc.c:3043:23: error: 'LARGE_MAXCLASS' undeclared (first use in this function); did you mean 'PAGE_MASK'?
  if (unlikely(usize > LARGE_MAXCLASS)) {
                       ^~~~~~~~~~~~~~
include/jemalloc/internal/util.h:33:43: note: in definition of macro 'unlikely'
 #  define unlikely(x) __builtin_expect(!!(x), 0)
                                           ^
At top level:
src/arena.c:1433:1: warning: 'arena_prof_demote' defined but not used [-Wunused-function]
 arena_prof_demote(tsdn_t *tsdn, extent_t *extent, const void *ptr) {
 ^~~~~~~~~~~~~~~~~
src/arena.c:43:19: warning: 'arena_binind_div_info' defined but not used [-Wunused-variable]
 static div_info_t arena_binind_div_info[NBINS];
                   ^~~~~~~~~~~~~~~~~~~~~
make: *** [Makefile:349: src/arena.sym.o] Error 1
make: *** [Makefile:349: src/jemalloc.sym.o] Error 1
[ERROR]: Build error detected ^o^
In file included from /bin/autobuild:64
                 from /usr/lib/autobuild3/lib/base.sh:118
                 from /usr/lib/autobuild3/lib/base.sh:122
                 from /usr/lib/autobuild3/proc/50-build_exec.sh:27
/usr/lib/autobuild3/build/10-autotools.sh:82: In function (unknown):
/usr/lib/autobuild3/build/10-autotools.sh:82: error: command exited with 2
>  82 | 		|| abdie "Failed to build binaries: $?."

autobuild encountered an error and couldn't continue.
Failed to build binaries: 2.
------------------------------autobuild 3------------------------------
Go to ‘http://github.com/AOSC-Dev/autobuild3’ for more information on this error.
[ERROR]: Error when building jemalloc.

```
- libcork compile error -> shadowsocks-libev
```
sources/libcork/docs/old/dllist.rst:50: WARNING: Type must be either just a name or a typedef-like declaration.
If just a name:
  Error in declarator or parameters
  Invalid C declaration: Expected identifier in nested name, got keyword: struct [error at 6]
    struct cork_dllist_item
    ------^
If typedef-like declaration:
  Error in declarator or parameters
  Invalid C declaration: Expected identifier in nested name. [error at 23]
    struct cork_dllist_item
    -----------------------^

/sources/libcork/docs/old/dllist.rst:55: WARNING: Error in declarator or parameters
Invalid C declaration: Expected identifier in nested name. [error at 24]
  struct cork_dllist_item \*next
  ------------------------^
/sources/libcork/docs/old/dllist.rst:55: WARNING: Error in declarator or parameters
Invalid C declaration: Expected identifier in nested name. [error at 24]
  struct cork_dllist_item \*prev
  ------------------------^
/sources/libcork/docs/old/dllist.rst:63: WARNING: Error in declarator or parameters
Invalid C declaration: Expecting "," or ")" in parameters, got "\". [error at 41]
  void cork_dllist_init(struct cork_dllist \*list)
  -----------------------------------------^

Exception occurred:
  File "/usr/lib/python3.8/site-packages/sphinx/domains/c.py", line 3266, in handle_signature
    assert symbol.siblingAbove.siblingBelow is None
AssertionError
The full traceback has been saved in /tmp/sphinx-err-pgl1beun.log, if you want to report the issue to the developers.

```
- genfstab installed but no pacman error  

```
Package pacman is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'pacman' has no installation candidate
E: Unable to locate package archlinux-keyring
[ERROR]: Build error detected ^o^

```

# my change to local ACBS
## chmod +x *.so 
- idnkit chmod +x /usr/lib/libidnkit.so /usr/lib/libidnkitlite.so
- ...

## location change
xfsprogs <- inith -> `/usr/lib/loongarch64.../*.so` change to `/usr/lib/*.so`  

## update version and link
- file
- dpkg
- debianutils
- config
- ...

## potential ACBS improvement

- breathe change to noarch
- config package update

## experimental tweak
gobject-introspection build on loongnix and change DESTDIR, NO deb yet  

## important warning

- quota-tools warning  
```
[INFO]:  Installing dpkg package(s) ...
(Reading database ... 71231 files and directories currently installed.)
Preparing to unpack quota-tools_4.06-0_loongarch64.deb ...
Unpacking quota-tools (4.06) ...
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rquota.h', which is also in package rpcsvc-proto 1.4.2-1
dpkg: warning: overriding problem because --force enabled:
dpkg: warning: trying to overwrite '/usr/include/rpcsvc/rquota.x', which is also in package rpcsvc-proto 1.4.2-1

```
