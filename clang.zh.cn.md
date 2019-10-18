# Clang 使用笔记

- Clang 在 Windows 平台上默认目标为`x86_64-pc-windows-msvc`，因此要依赖 MSVC 的头文件和库文件。如果不想依赖 MSVC，可以指定目标为`x86_64-pc-windows-gnu`（或`x86_64-w64-mingw32`），具体的参数为`-target x86_64-pc-windows-gnu`或`--target=x86_64-pc-windows-gnu`，此参数也适用于 Linux 平台。修改`clang(++).exe`为`x86_64-w64-mingw32-clang(++).exe`也是可以的，因为 Clang 会通过判断它自己的文件名来自动切换目标。也可以通过修改这个参数实现不同平台交叉编译，前提是先要安装好对应的头文件和库文件。
- 启用 LTO 后，为速度优化时二进制文件会增大，为大小优化时二进制文件会减小（大体趋势是这样的，但也有不少例外，并不是一定就如此）
- Thin LTO(`-flto=thin`) 远比 Full LTO(`-flto`) 快，因此建议使用前者而不是后者
- 在 Linux 平台上，同时启用`-Oz`和`-flto(=thin)`会报错，但只要这二者中至少一个添加了`-Xclang`前缀，就可以解决。例如：`-Xclang -Oz -flto=thin`。
- LLD 在 Windows 平台上的版本为`lld-link.exe`，在 Linux 平台上的版本为`ld.lld`，在 macOS 平台上的版本为`ld64.lld`，不要直接调用`lld(.exe)`。
- Ubuntu 平台如何安装官方最新版：<http://apt.llvm.org/>
- Windows 平台如何下载官方最新版：<http://releases.llvm.org/>
- Clang 没有自己的运行时依赖，但要依赖目标的运行时，在 Windows 平台上默认依赖 MSVC 的运行时，但也可以通过修改目标取消对其的依赖，但相应的会依赖别的运行时，例如 MinGW 的运行时。
- 优化级别最高是`O3`（或`Ofast`），但生成的文件很大，非特殊情况不推荐此优化级别。一般推荐使用`Oz`优化，此级别为 Clang 独有，相当于`O2`级别的优化，但关闭了对大小影响较大的优化选项，因此可以以较小的文件大小实现尽量高的性能。
- Linux 平台上可以通过把`-fuse-ld=lld`参数传给`clang(++)`来更改默认链接器为 LLD
- 优化级别的参数要同时传给编译器和链接器。对链接器而言，一般情况下直接传参数就可以了，特殊情况下可以使用`-Wl`或`-Xlinker`来强制传给链接器
- 在使用 LLD 作为链接器时，如果启用了 LTO，可以不把 LTO 相关参数传给链接器，因为 LLD 能自动处理开启了 LTO 的文件，但编译器还是要传递相关参数的。
- 使用clang-cl时，要手动把`/OPT:REF,ICF,LBR`这个链接器参数传给clang-cl或lld-link，因为不知为何它不会自动添加这个参数
- 如何从源码编译 Clang （或整个 LLVM 套件）：
  1. 检出 LLVM Project 的源码：

     ```bash
     git clone https://github.com/llvm/llvm-project.git
     ```

     请自行安装 Git。
  2. 创建并进入构建文件夹：

     ```bash
     cd ~
     mkdir build_llvm && cd build_llvm
     ```

     此处是以 Linux 环境举例，Windows 系统下也要自己找个地方专门创建一个文件夹，不要直接在 LLVM 源码文件夹下构建。
  3. 使用 CMake 配置工程：

     ```bat
     cmake -DCMAKE_BUILD_TYPE=Release -DLLVM_USE_CRT_RELEASE=MT -DLLVM_USE_CRT_DEBUG=MTd -DLLVM_TARGETS_TO_BUILD="X86;PowerPC" -DLLVM_ENABLE_PROJECTS="clang;lldb" -DCMAKE_INSTALL_PREFIX=C:/LLVM -G"Visual Studio 16 2019" -Ax64 -Thost=x64 <llvm-project源码根目录>\llvm
     ```

     此处以使用 Visual Studio 2019 构建 64 位架构的 LLVM 为例。在运行CMake前，环境变量要先设置好，例如VS提供`vcvarsall.bat`，或者设置`CC`和`CXX`这两个环境变量。
     - CMAKE_BUILD_TYPE：构建类型，可选值为`Debug`，`Release`，`MinSizeRel`以及`RelWithDebInfo`。此参数不是必需的。
     - LLVM_TARGETS_TO_BUILD：要构建的架构，`all`（全部构建）或者以英文半角分号为分隔的列表，例如`X86;PowerPC`。此参数不是必需的。
     - LLVM_USE_CRT_RELEASE，LLVM_USE_CRT_DEBUG：连接动态还是静态C运行时，动态为`MD/MDd`，静态为`MT/MTd`，VS（以及clang-cl，icl）专用，其他编译器不支持此参数。此参数不是必需的。
     - LLVM_ENABLE_PROJECTS：要构建的项目，`all`（全部构建）或者以英文半角分号为分隔的列表，例如`clang;clang-tools-extra;lld`（其实就是各个项目文件夹的名字），要使用这个参数就不能将各个组件放到`llvm`项目的子文件夹中，例如不能将`clang`文件夹移动到`llvm\tools\clang`，要保持`llvm-project`这整个项目刚检出时的结构。此参数不是必需的。
     - CMAKE_INSTALL_PREFIX：安装路径，编译完成后执行`install`命令，会将开发者指定的文件复制到这个路径。此参数不是必需的。
     - -Ax64：构建64位LLVM。使用`-AWin32`来构建32位LLVM。VS专用参数，Ninja和其他编译器都不支持这个参数。
     - -Thost=x64：使用64位工具链，因为32位连接器会报错说内存不足（32位程序的限制，无解），必须用64位工具链。这里也顺便说一下，`-Thost=x86`是使用32位工具链的意思。VS专用参数，Ninja和其他编译器都不支持这个参数。
  4. 编译：

     ```bash
     cmake --build .
     ```

     或者打开CMake刚刚生成的工程文件自己编译。命令最后的那个点不要落掉，这个参数代表的是该项目的二进制目录，一个点代表当前文件夹，如果不是当前文件夹，要传真实的路径过去，例如`cmake --build "C:/llvm-build-dir/build"`。
     - 注：特别的，对于VS工程而言，可以使用`msbuild /p:Configuration=Release INSTALL.vcxproj`来开始构建并在构建完毕后进行安装。
  5. 安装：

     ```bash
     cmake --install .
     ```

     同样要注意那个点的问题。
