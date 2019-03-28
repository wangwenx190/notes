# Qt 使用笔记
- Windows 平台尽量使用`JOM`编译，速度快很多，远远不是`NMAKE`或者`mingw32-make`能比得上的。
- 不知道什么原因，在 Windows 平台上交叉编译 Qt 时不能使用`JOM`，否则会报错
- Qt 国内镜像站：http://mirrors.ustc.edu.cn/qtproject/
- 在 Windows 平台上编译 Qt 时，编译`ANGLE`时需要一个叫`WindowsSdkVerBinPath`的环境变量，其路径指向`fxc.exe`(Microsoft Direct3D Shader Compiler)所在的文件夹，常见路径为`C:\Program Files (x86)\Windows Kits\10\bin\10.0.17763.0`
- 在 Windows 平台上，如果要编译`ANGLE`，需要安装[`DirectX SDK`](http://www.microsoft.com/en-us/download/details.aspx?id=6812)，新版 DX SDK 已经与[`Windows SDK`](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk)合并了。同时还需要[`Win flex-bison`](https://sourceforge.net/projects/winflexbison/)
- 最新版`Mesa 3D Library`（`opengl32sw.dll`）下载：https://github.com/pal1000/mesa-dist-win/releases
- 在 Linux 平台进行 Qt 开发需要安装额外的库：https://doc.qt.io/qt-5/linux.html
- 在 Linux 平台编译 Qt 需要安装额外的库：https://doc.qt.io/qt-5/linux-requirements.html
- `QWebEngine`模块不支持静态编译
- 在 Windows 平台上，`QWebEngine`模块只能使用最新版的`Visual Studio`编译，不支持其他一切编译器（目前，2019-03-27）
- 不要将 Qt 项目放在有非英文字符的路径下，否则会无法编译
- 添加删除源文件后，要重启执行`qmake`，否则会有链接错误
- 编译器选项与 Qt 的`CONFIG`如何对应：`O3/O2`->`optimize_full`，`O1/Os/Oz`->`optimize_size`，`LTCG/LTO`->`ltcg`，`Qt Quick Compiler`->`qtquickcompiler`（Qt Quick 编译器在 5.11 后默认开启），`-MT/-MTd`->`static_runtime`
- `opengl32sw.dll`这个文件是软件模拟的显卡驱动，所以只有在极少数情况下才会需要，发布 Qt 程序时不必带上此文件
- 发布 Windows 平台的 Qt 程序时可以使用 Qt 官方提供的`windeployqt`程序，这个小程序会自动检测并复制相关的 dll 到你的程序文件夹，非常方便。但它无法检测第三方库，必须自行查找并复制。而且这个工具会复制一些多余的 Qt 的 dll，但极难判断究竟哪些是真的无用，因此就不要管了。
- 使用`CONFIG += windeployqt` -> `nmake/jom windeployqt`来自动执行`windeployqt`程序
- 用命令行编译 Qt 工程：`qmake "example.pro" -spec win32-clang-g++ "CONFIG+=release"` -> `jom qmake_all` -> `jom` -> `jom install`。其中，`-spec`指定了`mkspec`，是不可缺少的参数，`CONFIG`指定了额外的配置选项，也不能缺少。
- 可以使用[`Dependency Walker`](http://www.dependencywalker.com/)这个工具来检测你开发的程序具体依赖哪些 dll，非常好用
- 如果你开发的程序不是很大，不推荐使用 Qt 提供的`Installer Framework`(`IFW`)打包，因为这个`IFW`是 Qt 静态编译的，因此文件会比较大，哪怕什么文件都不打包，也会有**20MB**左右的大小，得不偿失。当然，如果你的程序很大，几百兆甚至更大，就可以用它了。小软件推荐：[NSIS](https://sourceforge.net/projects/nsis/)，[Inno Setup](http://jrsoftware.org/isinfo.php)，[WiX Toolset](http://wixtoolset.org/)（制作**MSI**安装包专用）。不推荐：Install Shield（授权费非常昂贵，软件非常臃肿，打包出的安装程序较大，界面不容易定制。不信自己装一个试试），Install Anywhere（与前者是同一个公司的，因此缺点也差不多），Advanced Installer（有授权费），Vice Installer（远古软件），Wise Installation System（远古软件），Smart Install Maker（基本就是个玩具）以及其他任何不知名小软件
- 尽量不要链接`ICU`(`International Components for Unicode`)，虽然它提供了对世界上大多数国家和地区的语言和文字支持，功能非常强大，但文件太大，裁剪前**20MB~30MB**左右，裁剪后也有**10MB**左右，实际上一般程序根本用不到这种变态级别的国际化支持，所以不推荐使用这个库。Qt 官方也早已去掉了对它的依赖。
- Qt 5.13系列添加了对`Schannel`的支持，可以在配置时通过`-schannel`来启用，以此去掉对`OpenSSL`的依赖
- `Qt Remote Objects`模块是用来做`进程间通信`（`IPC`）的，对于 Qt 程序来说非常好用
- 区分调试版本和发布版本：`CONFIG(debug, debug|release)`（debug时返回true），`CONFIG(release, debug|release)`（release时返回true），或者更简单的`debug:`和`release:`。在代码中，可以通过`#ifdef _DEBUG`来判断。
- 区分 Qt 是静态链接还是动态链接的：`CONFIG(static, static|shared)`，`CONFIG(shared, static|shared)`，`static:`，`shared:`。在代码中，可以通过`#ifdef QT_STATIC`来判断。
- 区分32位还是64位：`contains(QMAKE_TARGET.arch, x86_64)`，`contains(QMAKE_HOST.arch, x86_64)`，`contains(QT_ARCH, x86_64)`，以上三条语句在编译64位程序时返回true。其中`x86_64`替换为其他架构，例如`i386`，也是可行的，只不过判断的就不一定是64位了。在代码中，Windows 平台上可以通过`#ifdef WIN64`或`#ifdef _WIN64`来判断是不是64位，不要用`WIN32`来判断，因为`WIN32`这个会在两个架构上都有定义。
- 区分应用程序和库：`contains(TEMPLATE, app)`，`contains(TEMPLATE, lib)`
- Windows 平台上添加图标及属性信息：`VERSION = 1.2.3`（设置版本，全平台通用），`RC_ICONS = "../res/icon.ico"`（设置图标），`RC_FILE = "../res/eg.rc"`（设置资源文件），`QMAKE_TARGET_PRODUCT = "My app name"`（属性页，产品名称），`QMAKE_TARGET_DESCRIPTION = "My app description"`（属性页，文件说明），`QMAKE_TARGET_COPYRIGHT = "My copyright info"`（属性页，版权），`QMAKE_TARGET_COMPANY = "My company name"`（属性页，公司）
- 修改二进制文件输出目录：`DESTDIR = $$PWD/bin`，在 Windows 平台上，dll 文件专有一个`DLLDESTDIR`。
- `.qmake.conf`文件：将此文件放在`.pro`文件所在的文件夹中，qmake会自动加载这个文件，并且会在加载`.pro`文件前先加载它，因此可以将一些通用的配置写到这个文件中
- `qt.conf`文件：将此文件放在你开发的程序所在的文件夹中，可以修改 Qt 加载某些库和翻译文件（`.qm`文件）的路径，个别场景会用到这个文件，具体请查看 Qt 帮助文档。在代码中也可以动态获取，例如，可以使用`QLibraryInfo::location(QLibraryInfo::TranslationsPath)`来获取`qt.conf`文件中指定的翻译文件的路径，但就算文件中没有指定，或者这个文件根本不存在，Qt 自身也会提供一个默认路径，例如，插件默认为`plugins`文件夹，翻译文件默认为`translations`文件夹等。
- 经过我的实验，`QFile`无法在`%APPDATA%`（常见路径：`C:\Users\Administrator\AppData`）文件夹创建文件，如果文件路径中有不存在的文件夹，它也不能自行创建，但已有的文件可以修改，不知道具体的原因。因此，如果你要在这个文件夹记录调试日志，要先创建一个文件，再用`QFile`去修改它。奇怪的是，`QSettings`可以在这个文件夹创建文件和文件夹。
- 区分操作系统：编译时可以通过`Q_OS_WIN`，`Q_OS_LINUX`和`Q_OS_MAC`等宏来区分，运行时可以通过`QOperatingSystemVersion`这个类来获取
- 区分编译器：编译时可以通过`Q_CC_MSVC`，`Q_CC_GNU`（GCC，G++，MinGW），`Q_CC_INTEL`（ICC），`Q_CC_CLANG`等宏来区分
- 交叉编译：`configure -xplatform win32-clang-g++ -device-option CROSS_COMPILE=x86_64-w64-mingw32-`，其中，`CROSS_COMPILE`参数前面没有`-`，它前面还要跟一个单独的`-device-option`，而且`x86_64-w64-mingw32-`末尾的`-`不能丢
- 在 Windows 平台上编译 Qt 时，如果没有显式的指定，所有第三方库都会用 Qt 自带的。而在 Linux 平台上相反，Qt 会自动使用系统中已经存在的第三方库，默认是不会使用自带的第三方库的。
- 编译完成后实现自定义动作：
   ```bash
   example.path = $$DESTDIR #这个只要没有真实的输出文件就随便填，但不能没有或者空着，否则报错
   example.commands = do_something.bat #这里写具体要执行的操作，为了方便，可以写到一个批处理里，然后在这里运行那个批处理
   QMAKE_EXTRA_TARGETS += example #这一行不能少，否则不会添加到 Makefile 里
   POST_TARGETDEPS += example #有了这一行才会在编译完成后自动调用
   ```
