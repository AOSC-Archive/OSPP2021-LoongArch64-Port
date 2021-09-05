# DE progess
LXDE-base on loongarch64 is successfully built except for `brasero` and `gvfs` dependency.  
All `x11-*` are successfully built.  
GNOME need `qt-5`, but it requires porting to loongarch64, it is very likely that Loongson has successfully port `qt-5`, but not readily publically share the code.  


# ACBS tree change for loongarch64
https://github.com/xyq1113723547/aosc-os-abbs/commits/stable  
This repo contains temporary changes for build scripts or configure flags for loongarch64, it also contains beyond changes for execution bit in share objects for autobuild QA E324.  

# bootstrap

There are two ways to bootstrap AOSC OS, one offical way is using aoscbootstrap,  
but aoscbootstrap need rustc, and the rustc version in Loongnix 20 is older than requirement.  
Thus, I tried debootstrap with custom recipe here https://github.com/AOSC-Dev/scriptlets/tree/master/debootstrap  
I need to setup a APT repository at first, however, setup a formal APT repository on Loongnix is tedious and unnecessary in this stage. I tried using `local-apt-repository` at first and it did not work.  
Then I use `apt-ftparchive` suggested here https://wiki.debian.org/DebianRepository/Setup#Quick_instructions_to_create_a_trivial_local_archive_with_apt-ftparchive, it works.  
After I change the mirror in aosc custom recipe to local http APT repo, debootstrap seems not compatible with it nor print any logs or error.  
I decided to write my own bootstrap script here https://github.com/AOSC-Dev/scriptlets/blob/master/loongarch-bootstrap  
The main idea is to setup a working minimal root file system that others can chroot in and use APT inside.  
Stage1 means `ar` all packages and add configurations for the target on host machine.  
Stage2 means the actions needed in chroot environment.  

# errors
- gvfs

```
Dependency polkit-gobject-1 found: NO found 0.105 but need: '>= 0.114'
```

- shaderc
```
AILED: glslc/glslc 
: && /bin/c++ -Wimplicit-fallthrough -O3 -DNDEBUG -rdynamic glslc/CMakeFiles/glslc_exe.dir/src/main.cc.o -o glslc/glslc  glslc/libglslc.a  libshaderc_util/libshaderc_util.a  libshaderc/libshaderc.a  libshaderc_util/libshaderc_util.a  -lSPIRV-Tools-opt  -lSPIRV-Tools  -lHLSL  -lglslang  -lOSDependent  -lOGLCompiler  -lglslang  -lOSDependent  -lOGLCompiler  -lSPIRV  -lpthread && :
/bin/ld: glslc/CMakeFiles/glslc_exe.dir/src/main.cc.o: in function `.L741':
(.text.startup+0x2694): undefined reference to `spvTargetEnvDescription'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L86':
(.text+0x30c): undefined reference to `spvValidatorOptionsCreate'
/bin/ld: (.text+0x31c): undefined reference to `spvValidatorOptionsSetSkipBlockLayout'
/bin/ld: (.text+0x328): undefined reference to `spvValidatorOptionsSetRelaxLogicalPointer'
/bin/ld: (.text+0x334): undefined reference to `spvValidatorOptionsSetBeforeHlslLegalization'
/bin/ld: (.text+0x338): undefined reference to `spvOptimizerOptionsCreate'
/bin/ld: (.text+0x348): undefined reference to `spvOptimizerOptionsSetValidatorOptions'
/bin/ld: (.text+0x354): undefined reference to `spvOptimizerOptionsSetRunValidator'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L46':
(.text+0x37c): undefined reference to `spvtools::Optimizer::Optimizer(spv_target_env)'
/bin/ld: (.text+0x4c0): undefined reference to `spvtools::Optimizer::SetMessageConsumer(std::function<void (spv_message_level_t, char const*, spv_position_t const&, char const*)>)'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L57':
(.text+0x51c): undefined reference to `spvtools::CreateStripDebugInfoPass()'
/bin/ld: (.text+0x528): undefined reference to `spvtools::Optimizer::RegisterPass(spvtools::Optimizer::PassToken&&)'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L122':
(.text+0x530): undefined reference to `spvtools::Optimizer::PassToken::~PassToken()'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L63':
(.text+0x558): undefined reference to `spvtools::Optimizer::Run(unsigned int const*, unsigned long, std::vector<unsigned int, std::allocator<unsigned int> >*, spv_optimizer_options_t*) const'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L79':
(.text+0x5d0): undefined reference to `spvtools::Optimizer::~Optimizer()'
/bin/ld: (.text+0x5d8): undefined reference to `spvOptimizerOptionsDestroy'
/bin/ld: (.text+0x5e0): undefined reference to `spvValidatorOptionsDestroy'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L55':
(.text+0x5ec): undefined reference to `spvtools::CreateCompactIdsPass()'
/bin/ld: (.text+0x5f8): undefined reference to `spvtools::Optimizer::RegisterPass(spvtools::Optimizer::PassToken&&)'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L60':
(.text+0x604): undefined reference to `spvtools::Optimizer::RegisterLegalizationPasses()'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L58':
(.text+0x614): undefined reference to `spvtools::Optimizer::RegisterSizePasses()'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L59':
(.text+0x624): undefined reference to `spvtools::Optimizer::RegisterPerformancePasses()'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L123':
(.text+0x814): undefined reference to `spvtools::Optimizer::PassToken::~PassToken()'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L50':
(.text+0x824): undefined reference to `spvtools::Optimizer::~Optimizer()'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L84':
(.text+0x82c): undefined reference to `spvOptimizerOptionsDestroy'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L85':
(.text+0x834): undefined reference to `spvValidatorOptionsDestroy'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L136':
(.text+0x934): undefined reference to `spvtools::SpirvTools::SpirvTools(spv_target_env)'
/bin/ld: (.text+0xa60): undefined reference to `spvtools::SpirvTools::SetMessageConsumer(std::function<void (spv_message_level_t, char const*, spv_position_t const&, char const*)>)'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L141':
(.text+0xa8c): undefined reference to `spvtools::SpirvTools::Disassemble(std::vector<unsigned int, std::allocator<unsigned int> > const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*, unsigned int) const'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L157':
(.text+0xb8c): undefined reference to `spvtools::SpirvTools::~SpirvTools()'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L140':
(.text+0xcfc): undefined reference to `spvtools::SpirvTools::~SpirvTools()'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L192':
(.text+0xdc0): undefined reference to `spvContextCreate'
/bin/ld: (.text+0xdf0): undefined reference to `spvTextToBinary'
/bin/ld: libshaderc_util/libshaderc_util.a(spirv_tools_wrapper.cc.o): in function `.L193':
(.text+0x105c): undefined reference to `spvDiagnosticDestroy'
/bin/ld: (.text+0x1064): undefined reference to `spvContextDestroy'
/bin/ld: libshaderc/libshaderc.a(shaderc.cc.o): in function `shaderc_compilation_result_spv_binary::~shaderc_compilation_result_spv_binary()':
(.text._ZN37shaderc_compilation_result_spv_binaryD2Ev[_ZN37shaderc_compilation_result_spv_binaryD5Ev]+0x24): undefined reference to `spvBinaryDestroy'
/bin/ld: libshaderc/libshaderc.a(shaderc.cc.o): in function `shaderc_compilation_result_spv_binary::~shaderc_compilation_result_spv_binary()':
(.text._ZN37shaderc_compilation_result_spv_binaryD0Ev[_ZN37shaderc_compilation_result_spv_binaryD5Ev]+0x24): undefined reference to `spvBinaryDestroy'
collect2: error: ld returned 1 exit status
ninja: build stopped: subcommand failed.
```
