# ANGLE 使用笔记

- 最新版`ANGLE`下载：<https://circleci.com/gh/wang-bin/build_angle/20#artifacts/containers/0> （记得把`#artifacts`前面的`20`换成最后一次成功构建的代号）

  CMake脚本：<https://circleci.com/gh/wang-bin/build_angle>
- `ANGLE`（`libEGL.dll`和`libGLESv2.dll`）要搭配`d3dcompiler_XX.dll`一起使用，其中的`XX`一般为`47`，此文件位于`DirectX SDK`文件夹中，常见路径为`C:\Program Files (x86)\Windows Kits\10\Redist\D3D`
- 在 Windows 平台上编译 Qt 时，编译`ANGLE`时需要一个叫`WindowsSdkVerBinPath`的环境变量，其路径指向`fxc.exe`(Microsoft Direct3D Shader Compiler)所在的文件夹，常见路径为`C:\Program Files (x86)\Windows Kits\10\bin\10.0.17763.0`

  注：`10.0.17763.0`为你所安装的Win SDK的版本号。
- 在 Windows 平台上，如果要编译`ANGLE`，需要安装[`DirectX SDK`](http://www.microsoft.com/en-us/download/details.aspx?id=6812)，新版 DX SDK 已经与[`Windows SDK`](https://developer.microsoft.com/en-us/windows/downloads/sdk-archive)合并了。同时还需要[`Win flex-bison`](https://sourceforge.net/projects/winflexbison/)
