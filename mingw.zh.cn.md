# MinGW 使用笔记
- 在 Windows 平台上启用 LTO 会报错，**部分**错误可以通过将 LTO 插件（`liblto_plugin-0.dll`，通常位于`libexec\gcc\x86_64-w64-mingw32\8.1.0`）复制到`lib\bfd-plugins`文件夹下解决（没有此文件夹就新建一个）
- AVX support is broken in 64-bit MinGW - https://gcc.gnu.org/bugzilla/show_bug.cgi?id=49001
- mingw32-make -jN，此处`N`为电脑核心数
- 运行时依赖项：`libgcc_s_seh-1.dll`，`libstdc++-6.dll`和`libwinpthread-1.dll`
- 推荐线程模型：POSIX，推荐异常处理：SEH
