# MinGW 使用笔记
- 在 Windows 平台上启用 LTO 会报错，**部分**错误可以通过将 LTO 插件（`liblto_plugin-0.dll`，通常位于`libexec\gcc\x86_64-w64-mingw32\8.1.0`）复制到`lib\bfd-plugins`文件夹下解决（没有此文件夹就新建一个）
- AVX support is broken in 64-bit MinGW - https://gcc.gnu.org/bugzilla/show_bug.cgi?id=49001
- mingw32-make/make -jN，此处`N`为电脑核心数
- 运行时依赖项：`libgcc_s_seh-1.dll`，`libstdc++-6.dll`和`libwinpthread-1.dll`
- 推荐线程模型：POSIX，推荐异常处理：SEH（64位）/SJLJ（32位）
- 在 Linux 平台上使用 MinGW 交叉编译时可以启用 LTO，但要注意支不支持`-fno-fat-lto-objects`的问题，如果支持，就要使用`-fuse-linker-plugin`（这个参数要传给链接器）启用链接器插件
