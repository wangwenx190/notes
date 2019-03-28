# Qt 使用笔记
- Windows 平台尽量使用[`JOM`](http://download.qt.io/official_releases/jom/)编译，速度快很多，远远不是`NMAKE`或者`mingw32-make`能比得上的。
- Qt 国内镜像站：http://mirrors.ustc.edu.cn/qtproject/
- 在 Windows 平台上编译 Qt 时，编译`ANGLE`时需要一个叫`WindowsSdkVerBinPath`的环境变量，其路径指向`fxc.exe`(Microsoft Direct3D Shader Compiler)所在的文件夹，常见路径为`C:\Program Files (x86)\Windows Kits\10\bin\10.0.17763.0`
- 在 Windows 平台上，如果要编译`ANGLE`，需要安装[`DirectX SDK`](http://www.microsoft.com/en-us/download/details.aspx?id=6812)，新版 DX SDK 已经与[`Windows SDK`](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk)合并了。同时还需要[`Win flex-bison`](https://sourceforge.net/projects/winflexbison/)
- 最新版`Mesa 3D Library`（`opengl32sw.dll`）下载：https://github.com/pal1000/mesa-dist-win/releases
- 在 Linux 平台进行 Qt 开发需要安装额外的库：https://doc.qt.io/qt-5/linux.html
- 在 Linux 平台编译 Qt 需要安装额外的库：https://doc.qt.io/qt-5/linux-requirements.html
- `QWebEngine`模块不支持静态编译
- 在 Windows 平台上，`QWebEngine`模块只能使用最新版的`Visual Studio`编译，不支持其他一切编译器（目前，2019-03-27）
- 不要将 Qt 项目放在有非英文字符的路径下，否则会无法编译
- 添加删除源文件或者源文件改名后，要重新执行`qmake`然后重新构建，否则会有链接错误
- 编译器选项与 Qt 的`CONFIG`如何对应：`O3/O2`->`optimize_full`，`O1/Os/Oz`->`optimize_size`，`LTCG/LTO`->`ltcg`，`Qt Quick Compiler`->`qtquickcompiler`（Qt Quick 编译器在 5.11 后默认开启），`-MT/-MTd`->`static_runtime`
- `opengl32sw.dll`这个文件是用软件模拟的显卡，针对的是没有显卡的机器，所以只有在极少数情况下才会需要，发布 Qt 程序时不必带上此文件，能极大减小发布大小
- 发布 Windows 平台的 Qt 程序时可以使用 Qt 官方提供的`windeployqt`程序，这个小程序会自动检测并复制相关的 dll 到你的程序文件夹，非常方便。但它无法检测第三方库，必须自行查找并复制。而且这个工具会复制一些多余的 Qt 的 dll，但极难判断究竟哪些是真的无用，因此就不要管了。
- 使用`CONFIG += windeployqt` -> `nmake/jom windeployqt`来自动执行`windeployqt`程序
- 用命令行编译 Qt 工程：`qmake "example.pro" -spec win32-clang-g++ "CONFIG+=release"` -> `jom qmake_all` -> `jom` -> `jom install`。其中，`-spec`指定了`mkspec`，是不可缺少的参数，`CONFIG`指定了额外的配置选项，也不能缺少。这个命令行是全平台通用的。
- 可以使用[`Dependency Walker`](http://www.dependencywalker.com/)这个工具来检测你开发的程序具体依赖哪些 dll，非常好用
- 如果你开发的程序不是很大，不推荐使用 Qt 提供的`Installer Framework`(`IFW`)打包，因为这个`IFW`是 Qt 静态编译的，因此文件会比较大，哪怕什么文件都不打包，也会有**20MB**左右的大小，得不偿失。当然，如果你的程序很大，几百兆甚至更大，就可以用它了。小软件推荐：[NSIS](https://sourceforge.net/projects/nsis/)，[Inno Setup](http://jrsoftware.org/isinfo.php)，[WiX Toolset](http://wixtoolset.org/)（制作**MSI**安装包专用）。不推荐：Install Shield（授权费非常昂贵，软件非常臃肿，打包出的安装程序较大，界面不容易定制。不信自己装一个试试），Install Anywhere（与前者是同一个公司的，因此缺点也差不多），Advanced Installer（有授权费），Vice Installer（远古软件），Wise Installation System（远古软件），Smart Install Maker（基本就是个玩具）以及其他任何不知名小软件
- 尽量不要链接`ICU`(`International Components for Unicode`)，虽然它提供了对世界上大多数国家和地区的语言和文字支持，功能非常强大，但文件太大，裁剪前**20~30MB**左右，裁剪后也有**10MB**左右，实际上一般程序根本用不到这种变态级别的国际化支持，所以不推荐使用这个库。Qt 官方也早已去掉了对它的依赖。
- Qt 5.13系列添加了对`Schannel`的支持，可以在配置时通过`-schannel`来启用，以此去掉对`OpenSSL`的依赖
- `Qt Remote Objects`模块是用来做`进程间通信`（`IPC`）的，对于 Qt 程序来说非常好用
- 区分调试版本和发布版本：`CONFIG(debug, debug|release)`（debug时返回true），`CONFIG(release, debug|release)`（release时返回true），`contains(CONFIG, debug)`（debug时返回true），`contains(CONFIG, release)`（release时返回true），或者更简单的`debug:`和`release:`。在代码中，可以通过`#ifdef _DEBUG`来判断，但请注意，发布版本并没有`_RELEASE`这样的宏。
- 区分 Qt 是静态链接还是动态链接的：`CONFIG(static, static|shared)`，`CONFIG(shared, static|shared)`，`contains(CONFIG, static)`，`contains(CONFIG, shared)`，`static:`，`shared:`。在代码中，可以通过`#ifdef QT_STATIC`和`#ifdef QT_SHARED`来判断。
- 区分32位还是64位：`contains(QMAKE_TARGET.arch, x86_64)`，`contains(QMAKE_HOST.arch, x86_64)`，`contains(QT_ARCH, x86_64)`，以上三条语句在编译64位程序时返回true。其中`x86_64`替换为其他架构，例如`i386`，也是可行的，只不过判断的就不一定是64位了。在代码中，Windows 平台上可以通过`#ifdef WIN64`或`#ifdef _WIN64`来判断是不是64位，不要用`WIN32`来判断，因为`WIN32`这个会在两个架构上都有定义。
- 区分应用程序和库：`contains(TEMPLATE, app)`，`contains(TEMPLATE, lib)`
- Windows 平台上添加图标及属性信息：
   ```bash
   VERSION = 1.2.3 #设置版本，这个是全平台通用的。其中，只有 Windows 平台上的版本号是4位的，其他平台都是3位
   RC_ICONS = "../res/icon.ico" #设置图标
   RC_FILE = "../res/eg.rc" #设置资源文件。如果这个资源文件中包含图标或版本信息，会与此处其他语句冲突，请注意
   QMAKE_TARGET_PRODUCT = "My app name" #设置产品名称
   QMAKE_TARGET_DESCRIPTION = "My app description" #设置文件说明
   QMAKE_TARGET_COPYRIGHT = "My copyright info" #设置版权
   QMAKE_TARGET_COMPANY = "My company name" #设置公司
   ```
- 修改二进制文件输出目录：`DESTDIR = $$PWD/bin`，在 Windows 平台上，dll 文件专有一个`DLLDESTDIR`。
- `.qmake.conf`文件：将此文件放在`.pro`文件所在的文件夹中，qmake会自动加载这个文件，并且会在加载`.pro`文件前先加载它，因此可以将一些通用的配置写到这个文件中
- `qt.conf`文件：将此文件放在你开发的程序所在的文件夹中，可以修改 Qt 加载某些库和翻译文件（`.qm`文件）的路径，个别场景会用到这个文件，具体请查看 Qt 帮助文档。在代码中也可以动态获取，例如，可以使用`QLibraryInfo::location(QLibraryInfo::TranslationsPath)`来获取`qt.conf`文件中指定的翻译文件的路径，但就算文件中没有指定，或者这个文件根本不存在，Qt 自身也会提供一个默认路径，例如，插件默认为`plugins`文件夹，翻译文件默认为`translations`文件夹等。
- 经过我的实验，`QFile`无法在`%APPDATA%`（常见路径：`C:\Users\Administrator\AppData`）文件夹创建文件，如果文件路径中有不存在的文件夹，它也不能自行创建，但已有的文件可以修改，不知道具体的原因。因此，如果你要在这个文件夹记录调试日志，要先创建一个文件，再用`QFile`去修改它。奇怪的是，`QSettings`可以在这个文件夹创建文件和文件夹。
- 区分操作系统：编译时可以通过`Q_OS_WIN`，`Q_OS_LINUX`和`Q_OS_MAC`等宏来区分，运行时可以通过`QOperatingSystemVersion`这个类（Qt 5.9添加，可以直接获取到当前系统的可读版本，例如“Windows 8.1”）或`QSysInfo`这个类（只能获取到纯数字的版本号，例如“10.0.17763.152”，但能获取一些前者不能获取到的信息）来获取
- 区分编译器：编译时可以通过`Q_CC_MSVC`，`Q_CC_GNU`（GCC，G++，MinGW），`Q_CC_INTEL`（ICC），`Q_CC_CLANG`等宏来区分
- 交叉编译：`configure -xplatform win32-clang-g++ -device-option CROSS_COMPILE=x86_64-w64-mingw32-`，其中，`CROSS_COMPILE`参数前面没有`-`，它前面还要跟一个单独的`-device-option`，而且`x86_64-w64-mingw32-`末尾的`-`不能丢
- 在 Windows 平台上编译 Qt 时，如果没有显式的指定，所有第三方库都会用 Qt 自带的。而在 Linux 平台上相反，Qt 会自动使用系统中已经存在的第三方库，默认是不会使用自带的第三方库的。
- 编译完成后实现自定义动作：
   ```bash
   example.path = $$DESTDIR #这个只要没有真实的输出文件就随便填，但不能没有或者空着，否则报错
   example.commands = do_something.bat #这里写具体要执行的操作，为了方便，可以写到一个批处理里，然后在这里运行那个批处理
   #下面是把所有操作都写在.pro文件里的方法
   #example.commands += $$quote(copy /y a.dll b.dll) #这一句要用“+=”，并且“$$quote()”这个东西不能少，下面同理
   #example.commands += $$quote(move b.dll c.dat) #命令直接写在括号里就可以了，但一个括号里只能写一句，不止一句就分多行写
   #example.commands = $$join(example.commands,$$escape_expand(\\n\\t)) #这一句必须要用“=”，而且这一句不可缺少，否则报错
   QMAKE_EXTRA_TARGETS += example #这一句不能少，否则不会添加到 Makefile 里
   POST_TARGETDEPS += example #有了这一句才会在编译完成后自动调用
   #使用“PRE_TARGETDEPS”可以使命令在编译之前执行
   ```
- `qmake`判断文件是否存在：`exists($${ffmpeg_lib_dir}/*avcodec.lib)`。路径加不加双引号都无所谓。
- `qmake`判断变量是否为空或者存在：`isEmpty(ffmpeg_lib_dir)`。这里变量不用加`$$`这个前缀。
- `qmake`拼接字符串：`join(string1,,,string2)`。其中，`string1`和`string2`都可以是变量。例如：
   ```bash
   string1 = name
   string2 = $$join(string1,,,_new)
   #string2: name_new
   ```
- `qmake`替换字符串：`replace(string,to_be_replaced,replacer)`。例如：
   ```bash
   string1 = this is an example
   string2 = $$replace(string1,example,apple)
   #string2: this is an apple
   ```
- 子项目：
   ```bash
   TEMPLATE = subdirs #此句不可缺少
   CONFIG -= ordered #这一句看你项目的具体情况，“CONFIG += ordered”的意思是让qmake按照你在.pro文件中书写的顺序来生成项目
   project1.file = src/pro1.pro #指定子项目的路径
   project2.subdir = src/3rdparty/pro2 #这样的写法和上一句是等价的，但这样写的前提是这个文件夹中只有一个.pro文件
   project2.depends += project1 #此处为设置不同项目之间的依赖关系，被依赖的项目会首先生成
   SUBDIRS += project1 project2 #这一句是最终把子项目添加到总项目中
   ```
- 包含`.pri`文件：`include(../test.pri)`。理论上任何能写入`.pro`文件的内容都可以写到`.pri`文件中，但通常大家都是把公用的配置选项或者个别非常小的子项目写到`.pri`文件中。
- Qt 5.12系列添加了`lrelease`和`embed_translations`这两个`CONFIG`选项，前者可以使 Qt 在编译完成后自动调用`lrelease`程序，即编译完成后自动更新翻译，后者可以使 Qt 在编译完成后自动把翻译文件（`.qm`文件）嵌入到程序中，并添加`i18n`前缀，可以通过 Qt 的资源系统直接访问，例如，`:/i18n/qt_zh_CN.qm`或`qrc:///i18n/qtbase_uk.qm`。
- `versionAtLeast(QT_VERSION, 5.12.0)`和`versionAtMost(QT_VERSION, 5.14.2)`：Qt 5.9系列添加的比较版本号专用的函数，作用看函数名就明白了。直接把版本号写在逗号后面就可以，不用带双引号。`QT_VERSION`这个变量记录了 Qt 的版本号，可以用这个做一些判断，来实现一些特别的操作（仅限`qmake`）。
- 可以用`qtHaveModule`来判断 Qt 有没有安装某模块，例如：`qtHaveModule(winextras)`
- 可以用`qtConfig`来判断 Qt 在编译时有没有开启某特性，例如：`qtConfig(draganddrop)`。全部特性可以使用`configure -list-features`查看。
- 资源文件别名：`<file alias="cut-img.png">images/cut.png</file>`。可以通过`alias`来给`.qrc`文件中的资源文件起别名，优点在于可以不依赖于文件的实际路径。
- `qmake`获取 Qt 相关的文件夹路径：
   ```text
   QT_HOST_BINS - location of host executables
   QT_HOST_DATA - location of data for host executables used by qmake
   QT_HOST_PREFIX - default prefix for all host paths
   QT_INSTALL_ARCHDATA - location of general architecture-dependent Qt data
   QT_INSTALL_BINS - location of Qt binaries (tools and applications)
   QT_INSTALL_CONFIGURATION - location for Qt settings. Not applicable on Windows
   QT_INSTALL_DATA - location of general architecture-independent Qt data
   QT_INSTALL_DOCS - location of documentation
   QT_INSTALL_EXAMPLES - location of examples
   QT_INSTALL_HEADERS - location for all header files
   QT_INSTALL_IMPORTS - location of QML 1.x extensions
   QT_INSTALL_LIBEXECS - location of executables required by libraries at runtime
   QT_INSTALL_LIBS - location of libraries
   QT_INSTALL_PLUGINS - location of Qt plugins
   QT_INSTALL_PREFIX - default prefix for all paths
   QT_INSTALL_QML - location of QML 2.x extensions
   QT_INSTALL_TESTS - location of Qt test cases
   QT_INSTALL_TRANSLATIONS - location of translation information for Qt strings
   QT_SYSROOT - the sysroot used by the target build environment
   QT_VERSION - the Qt version. We recommend that you query Qt module specific version numbers by using $$QT.<module>.version variables instead.
   ```
   获取方法：`$$[var_name]`。例如：
   ```bash
   bin_dir = $$[QT_INSTALL_BINS] #一定不要忘了方括号
   #bin_dir: C:\Qt\Qt5.14.0\5.14.0\msvc2019_64\bin
   ```
   摘自：https://doc.qt.io/qt-5/qmake-environment-reference.html
- 在 Windows 平台上使用`MinGW`编译 Qt 时不能使用`JOM`，也不能开启 LTO，否则会报错，不清楚具体的原因
- 在 Windows 平台上如果想要嵌入 Manifest 文件，可以插入到资源文件中，但我试过几次，效果都不理想，可能是方法有问题
- 编译 Qt 时常用的参数：
   ```text
   -opensource：选择开源版 Qt，许可协议为 LGPLv3/GPLv3
   -confirm-license：同意许可协议
   -debug：生成调试版本
   -release：生成发布版本
   -debug-and-release：同时生成调试版本和发布版本
   -shared：生成动态库（.dll/.so）
   -static：生成静态库（.lib/.o）
   -static-runtime：静态链接运行时（仅限 MSVC，相当于 -MT/-MTd）
   -platform：选择目标平台
   -xplatform：选择目标平台（交叉编译时）
   -optimize-size：为大小优化（MSVC：O1，GCC：Os，Clang：Oz）
   -ltcg：启用 LTCG/LTO
   -silent：隐藏不必要的信息，仅输出警告和错误
   -nomake：不构建某部分，仅可在 libs，examples 和 tests 这三者中选择
   -skip：跳过某模块的构建，例如 qt3d，qtactiveqt 和 qtandroidextras 等
   -prefix：指定安装目录
   -linker：指定链接器，仅可在 bfd，gold 和 lld 这三者中选择（仅限 Linux 平台）
   -pch：启用预编译头
   -warnings-are-errors：把警告视为错误
   ```
