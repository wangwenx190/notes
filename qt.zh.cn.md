# Qt 使用笔记
- Windows 平台尽量使用[`JOM`](http://download.qt.io/official_releases/jom/)编译，速度快很多，远远不是`NMake`或者`mingw32-make`能比得上的。

  注：JOM仅支持原生Windows环境，任何Unix或类Unix环境（例如MinGW）均不支持。
- Qt 国内镜像站：http://mirrors.ustc.edu.cn/qtproject/
- 在 Windows 平台上编译 Qt 时，编译`ANGLE`时需要一个叫`WindowsSdkVerBinPath`的环境变量，其路径指向`fxc.exe`(Microsoft Direct3D Shader Compiler)所在的文件夹，常见路径为`C:\Program Files (x86)\Windows Kits\10\bin\10.0.17763.0`

  注：`10.0.17763.0`为你所安装的Win SDK的版本号。
- 在 Windows 平台上，如果要编译`ANGLE`，需要安装[`DirectX SDK`](http://www.microsoft.com/en-us/download/details.aspx?id=6812)，新版 DX SDK 已经与[`Windows SDK`](https://developer.microsoft.com/en-us/windows/downloads/sdk-archive)合并了。同时还需要[`Win flex-bison`](https://sourceforge.net/projects/winflexbison/)
- 最新版`Mesa llvmpipe`（`opengl32sw.dll`）下载：https://github.com/pal1000/mesa-dist-win/releases
- 在 Linux 平台进行 Qt 开发需要安装额外的库：https://doc.qt.io/qt-5/linux.html 。其中，在 Ubuntu 平台上进行开发的官方 Wiki：https://wiki.qt.io/Install_Qt_5_on_Ubuntu
- 在 Linux 平台编译 Qt 需要安装额外的库：https://doc.qt.io/qt-5/linux-requirements.html
- `QWebEngine`模块不支持静态编译
- 在 Windows 平台上，`QWebEngine`模块只能使用最新版的`Visual Studio`编译，不支持其他一切编译器（目前，2019-03-27）
- 不要将 Qt 项目放在有非英文字符的路径下，否则会无法编译
- 添加删除源文件或者源文件改名后，要重新执行`qmake`然后重新构建，否则会有链接错误
- 编译器选项与 Qt 的`CONFIG`如何对应：

  | 编译器选项 | MSVC参数 | Clang参数 | GCC参数 | ICC参数 | qmake |
  | --------- | -------- | -------- | ------- | ------- | ----- |
  | 为速度优化 | `/O2`    | `-Ofast`（clang-cl：`/clang:-Ofast`） | `-Ofast` | `-Ofast`（icl：`/O3`） | `CONFIG += optimize_full` |
  | 为大小优化 | `/O1`    | `-Oz`（clang-cl：`/clang:-Oz`） | `-Os` | `-Os`（icl：与MSVC相同） | `CONFIG += optimize_size` |
  | 启用链接时间代码生成 | `/GL`（编译器），`/LTCG`（连接器） | `-flto=thin`（clang-cl：`/clang:-flto=thin`；只有lld-link不需要额外的参数，其他任何连接器都要传这个参数） | `-flto -fno-fat-objects`（连接器还要多一个`-fuse-linker-plugin`参数） | `-ipo -fno-fat-objects`（icl：`/Qipo`） | `CONFIG += ltcg` |
  | 静态连接运行时 | `/MT`/`/MTd` | `-static`（clang-cl：与MSVC相同） | `-static` | `-static`（icl：与MSVC相同） | `CONFIG += static_runtime` |
  | 启用Quick编译器 | - | - | - | - | `CONFIG += qtquickcompiler` |
  | 启用异常处理 | `/EHsc` | `-fexceptions`（clang-cl：与MSVC相同） | `-fexceptions` | `-fexceptions`（icl：与MSVC相同） | `CONFIG += exceptions` |
  | 禁用异常处理 | `/EHs-c-`（或留空，不加任何参数） | `-fno-exceptions`（clang-cl：与MSVC相同） | `-fno-exceptions` | `-fno-exceptions`（icl：与MSVC相同） | `CONFIG += exceptions_off` |
  | 启用RTTI | `/GR` | `-frtti`（clang-cl：与MSVC相同） | `-frtti` | `-frtti`（icl：与MSVC相同） | `CONFIG += rtti` |
  | 禁用RTTI | `/GR-` | `-fno-rtti`（clang-cl：与MSVC相同） | `-fno-rtti` | `-fno-rtti`（icl：与MSVC相同） | `CONFIG += rtti_off` |
- `opengl32sw.dll`这个文件是用软件模拟的显卡，针对的是没有显卡的机器，所以只有在极少数情况下才会需要，发布 Qt 程序时不必带上此文件，能极大减小发布大小
- 发布 Windows 平台的 Qt 程序时可以使用 Qt 官方提供的`windeployqt`程序，这个小程序会自动检测并复制相关的 dll 到你的程序文件夹，非常方便。但它无法检测第三方库，必须自行查找并复制。而且这个工具会复制一些多余的 Qt 的 dll，但极难判断究竟哪些是真的无用，因此就不要管了。

  | 参数1 | 参数2 | 作用 |
  | ---- | ---- | ----- |
  | `--dir <directory>` | 目标文件夹路径 | 使用给定的文件夹（作为此次操作的根目录），而不是二进制文件所在的文件夹 |
  | `--libdir <path>` | 目标文件夹路径 | 将Qt库文件（Qt5XXX.dll或libQt5XXX.so）复制到给定的路径，而不是二进制文件所在的文件夹 |
  | `--plugindir <path>` | 目标文件夹路径 | 将Qt自身的插件复制到给定的路径，而不是直接放在二进制文件所在的文件夹 |
  | `--pdb` | - | 一同复制.PDB文件（仅限MSVC） |
  | `--force` | - | 强制更新文件 |
  | `--dry-run` | - | 模拟模式。正常执行操作，但不会复制或更新任何文件 |
  | `--qmldir <directory>` | **应用程序自身**的QML插件文件夹路径（此处用的是工程路径，不是发布后QML插件所在的路径） | 从给定的文件夹扫描.QML文件，来复制所用到的Qt Quick插件 |
  | `--no-translations` | - | 不复制Qt自身的翻译文件 |
  | `--no-system-d3d-compiler` | - | 不复制D3DCompiler_XX.dll |
  | `--no-virtualkeyboard` | - | 不复制虚拟键盘相关的文件 |
  | `--no-compiler-runtime` | - | 不复制编译器运行时相关的文件（例如vcredist_msvc2017_x64.exe） |
  | `--no-angle` | - | 不复制ANGLE相关的文件（libEGL.dll和libGLESv2.dll） |
  | `--no-opengl-sw` | - | 不复制Mesa llvmpipe相关的文件（opengl32sw.dll） |
  | `--json` | - | 将输出结果输出为JSON格式 |
  | `--list <option>` | `source`：源文件的绝对路径；`target`：目标文件的绝对路径；`relative`：目标文件的相对路径（与目标文件夹相对）；`mapping`：UWP平台的Appx专用 | 将复制的文件输出为列表文件 |
- 使用`CONFIG += windeployqt` -> `nmake/jom windeployqt`来自动执行`windeployqt`程序
- 用命令行编译 Qt 工程：`qmake "example.pro" -spec win32-clang-g++ "CONFIG+=release"` -> `jom qmake_all` -> `jom` -> `jom install`。其中，`-spec`指定了`mkspec`，是不可缺少的参数，`CONFIG`指定了额外的配置选项，也不能缺少。这个命令行是全平台通用的。
- 可以使用[`Dependency Walker`](http://www.dependencywalker.com/)这个工具来检测你开发的程序具体依赖哪些 dll，非常好用
- 如果你开发的程序不是很大，不推荐使用 Qt 提供的`Installer Framework`(`IFW`)打包，因为这个`IFW`是 Qt 静态编译的，因此文件会比较大，哪怕什么文件都不打包，也会有**20MB**左右的大小，得不偿失。当然，如果你的程序很大，几百兆甚至更大，就可以用它了。小软件推荐：[NSIS](https://sourceforge.net/projects/nsis/)，[Inno Setup](http://jrsoftware.org/isinfo.php)，[WiX Toolset](http://wixtoolset.org/)（制作**MSI**安装包专用）。不推荐：Install Shield（授权费非常昂贵，软件非常臃肿，打包出的安装程序较大，界面不容易定制。不信自己装一个试试），Install Anywhere（与前者是同一个公司的，因此缺点也差不多），Advanced Installer（有授权费），Vice Installer（远古软件），Wise Installation System（远古软件），Smart Install Maker（基本就是个玩具）以及其他任何不知名小软件
- 尽量不要链接`ICU`(`International Components for Unicode`)，虽然它提供了对世界上大多数国家和地区的语言和文字支持，功能非常强大，但文件太大，裁剪前**20~30MB**左右，裁剪后也有**10MB**左右，实际上一般程序根本用不到这种变态级别的国际化支持，所以不推荐使用这个库。Qt 官方也早已去掉了对它的依赖。在配置时给一个`-no-icu`参数就可以禁用`ICU`支持。
- Qt 5.13系列添加了对`Schannel`的支持，可以在配置时通过`-schannel`来启用，以此去掉对`OpenSSL`的依赖（如果完全不需要`OpenSSL`的支持，需要在配置时给一个`-no-openssl`参数来禁用`OpenSSL`）
- `Qt Remote Objects`模块是用来做`进程间通信`（`IPC`）的，对于 Qt 程序来说非常好用
- 区分调试版本和发布版本：`CONFIG(debug, debug|release)`（debug时返回true），`CONFIG(release, debug|release)`（release时返回true），`contains(CONFIG, debug)`（debug时返回true），`contains(CONFIG, release)`（release时返回true），或者更简单的`debug:`和`release:`。在代码中，可以通过`#ifdef _DEBUG`来判断，但请注意，发布版本并没有`_RELEASE`这样的宏。
- 区分 Qt 是静态链接还是动态链接的：`CONFIG(static, static|shared)`，`CONFIG(shared, static|shared)`，`contains(CONFIG, static)`，`contains(CONFIG, shared)`，`static:`，`shared:`。在代码中，可以通过`#ifdef QT_STATIC`和`#ifdef QT_SHARED`来判断。
- 区分32位还是64位：`contains(QMAKE_TARGET.arch, x86_64)`，`contains(QMAKE_HOST.arch, x86_64)`，`contains(QT_ARCH, x86_64)`，以上三条语句在编译64位程序时返回true。其中`x86_64`替换为其他架构，例如`i386`，也是可行的，只不过判断的就不一定是64位了。在代码中，Windows 平台上可以通过`#ifdef WIN64`或`#ifdef _WIN64`来判断是不是64位，不要用`WIN32`来判断，因为`WIN32`这个会在两个架构上都有定义。
- 区分应用程序和库：`contains(TEMPLATE, app)`，`contains(TEMPLATE, lib)`
- Windows 平台上添加图标及属性信息：
   ```bash
   VERSION = 1.2.3 #设置版本，这个是全平台通用的。其中，只有 Windows 平台上的版本号是4位的，其他平台都是3位
   RC_ICONS = "../res/icon.ico" #设置图标
   RC_FILE = "../res/eg.rc" #设置资源文件。此语句会与此处其他语句冲突，请注意
   QMAKE_TARGET_PRODUCT = "My app name" #设置产品名称
   QMAKE_TARGET_DESCRIPTION = "My app description" #设置文件说明
   QMAKE_TARGET_COPYRIGHT = "My copyright info" #设置版权
   QMAKE_TARGET_COMPANY = "My company name" #设置公司
   ```
- 修改二进制文件输出目录：`DESTDIR = $$PWD/bin`，在 Windows 平台上，dll 文件专有一个`DLLDESTDIR`。
- `.qmake.conf`文件：将此文件放在`.pro`文件所在的文件夹中，qmake会自动加载这个文件，并且会在加载`.pro`文件前先加载它，因此可以将一些通用的配置写到这个文件中
- `qt.conf`文件：将此文件放在你开发的程序所在的文件夹中，可以修改 Qt 加载某些库和翻译文件（`.qm`文件）的路径，个别场景会用到这个文件，具体请查看 Qt 帮助文档。在代码中也可以动态获取，例如，可以使用`QLibraryInfo::location(QLibraryInfo::TranslationsPath)`来获取`qt.conf`文件中指定的翻译文件的路径，但就算文件中没有指定，或者这个文件根本不存在，Qt 自身也会提供一个默认路径，例如，插件默认为`plugins`文件夹，翻译文件默认为`translations`文件夹等。**注意：如果你是在Qt Creator中运行你的Qt程序（例如你正在调试你的程序），那么`QLibraryInfo::location()`返回的就是当前正在使用的Qt Kit的相关路径，而不是你自己在qt.conf中所设置的路径。换句话说，在这个时候这个函数不会返回你应用程序所在文件夹的路径了。但只要不是用Qt Creator运行，就没有这个问题。**
- `QFile`无法在不存在的文件夹中创建文件，只能在已经存在的路径中新建或修改文件。如果路径不存在，可以使用`QDir::mkpath`创建，使用这个API，路径中任何不存在的文件夹都会被创建。
- 区分操作系统：编译时可以通过`Q_OS_WINDOWS`（Qt 5.14 添加，早期版本请使用`Q_OS_WIN`代替），`Q_OS_WINRT`，`Q_OS_LINUX`，`Q_OS_MACOS`（不要使用`Q_OS_MAC`，已废弃），`Q_OS_MINGW`，`Q_OS_CYGWIN`，`Q_OS_ANDROID`和`Q_OS_IOS`等宏来区分，运行时可以通过`QOperatingSystemVersion`这个类（Qt 5.9 添加，可以直接获取到当前系统的可读版本，例如“Windows 8.1”，也可以用具体的静态常量`QOperatingSystemVersion::Windows8_1`等来区分正在运行的系统，非常方便好用。官方推荐使用此类）或`QSysInfo`这个类（只能获取到纯数字的版本号，例如“10.0.17763.152”，但能获取一些前者不能获取到的信息。但尽量不要用这个类，官方推荐使用前者）来获取
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

   | 变量 | 作用 |
   | --- | --- |
   | QT_HOST_BINS | 主机的可执行程序所在的路径 |
   | QT_HOST_DATA | location of data for host executables used by qmake |
   | QT_HOST_PREFIX | 所有主机路径的前缀 |
   | QT_INSTALL_ARCHDATA | location of general architecture-dependent Qt data |
   | QT_INSTALL_BINS | Qt二进制文件（工具和应用）所在的路径 |
   | QT_INSTALL_CONFIGURATION | Qt配置文件所在的路径。不适用于Windows |
   | QT_INSTALL_DATA | location of general architecture-independent Qt data |
   | QT_INSTALL_DOCS | Qt文档所在的路径 |
   | QT_INSTALL_EXAMPLES | Qt示例项目所在的路径 |
   | QT_INSTALL_HEADERS | Qt头文件所在的路径 |
   | QT_INSTALL_LIBEXECS | location of executables required by libraries at runtime |
   | QT_INSTALL_LIBS | Qt库文件所在的路径（Windows是.lib所在的文件夹） |
   | QT_INSTALL_PLUGINS | Qt插件所在的路径 |
   | QT_INSTALL_PREFIX | 所有路径的默认前缀 |
   | QT_INSTALL_QML | QML 2.x 插件所在的路径 |
   | QT_INSTALL_TESTS | Qt测试用例所在的路径 |
   | QT_INSTALL_TRANSLATIONS | Qt翻译文件所在的路径 |
   | QT_SYSROOT | the sysroot used by the target build environment |
   | QT_VERSION | the Qt version. We recommend that you query Qt module specific version numbers by using $$QT.<module>.version variables instead. |

   获取方法：`$$[var_name]`。例如：
   ```bash
   bin_dir = $$[QT_INSTALL_BINS] #一定不要忘了方括号
   #bin_dir: C:\Qt\Qt5.14.0\5.14.0\msvc2019_64\bin
   ```
   摘自：https://doc.qt.io/qt-5/qmake-environment-reference.html
- 在 Windows 平台上使用`MinGW`编译 Qt 时不能使用`JOM`，也不能开启 LTO，否则会报错，不清楚具体的原因
- 在 Windows 平台上如果想要嵌入 Manifest 文件，可以插入到资源文件中，但我试过几次，效果都不理想，可能是方法有问题
- 编译 Qt 时常用的参数：

   | 参数 | 作用 |
   | --- | --- |
   | -opensource | 选择开源版 Qt，许可协议为 LGPLv3/GPLv3 |
   | -confirm-license | 同意许可协议。加上这个参数会跳过同意许可协议这个步骤。 |
   | -debug | 生成调试版本 |
   | -release | 生成发布版本 |
   | -debug-and-release | 同时生成调试版本和发布版本 |
   | -force-debug-info | 即使是发布版本也生成调试信息 |
   | -shared | 生成动态库（.dll/.so）（默认） |
   | -static | 生成静态库（.lib/.a） |
   | -static-runtime | 静态链接运行时（仅限 MSVC，相当于 -MT/-MTd） |
   | -platform \<platform> | 选择目标平台（Windows默认MSVC，Linux默认G++） |
   | -xplatform \<platform> | 选择目标平台（交叉编译时） |
   | -optimize-size | 为大小优化（MSVC：O1，GCC：Os，Clang：Oz） |
   | -ltcg | 启用 LTCG/LTO |
   | -silent | 隐藏不必要的信息，仅输出警告和错误 |
   | -nomake \<part> | 不构建某部分，仅可在 libs，tools，examples 和 tests 这四者中选择 |
   | -skip \<repository> | 跳过某模块的构建，例如 qt3d，qtactiveqt 和 qtandroidextras 等 |
   | -no-feature-\<feature> | Qt Lite Project的功能，去掉某特性的支持，可以进一步裁剪Qt的大小，例如 `-no-feature-clipboard` |
   | -prefix \<path> | 指定安装目录 |
   | -linker \<linker> | 指定链接器，仅可在 bfd，gold 和 lld 这三者中选择（仅限 Linux 平台） |
   | -pch | 启用预编译头（自动） |
   | -warnings-are-errors | 把警告视为错误 |
   | -opengl \<version> | 选择默认的OpenGL版本。仅可在 es2，desktop 和 dynamic 这三者中选择，其中，Windows 平台默认为 dynamic，Linux 平台默认为 desktop |
- 在 Linux 平台上交叉编译 Windows 版 Qt 时，需要安装`glibc`。Ubuntu 平台上的包名为`libc6-dev-i386`，`libc6-dev-amd64`，`libc6-dev-i386-cross`，`libc6-dev-amd64-cross`，`libc6-dev-i386-amd64-cross`和`libc6-dev-amd64-i386-cross`（写了这么多是因为我不知道具体是哪（几）个，只好都列出来）。
- <del>在 Linux 平台上交叉编译 Windows 版 Qt 时，如果报找不到`<EGL/egl.h>`的错误，需要安装额外的包。Ubuntu 平台上的包名为`freeglut3-dev`，`libgl1-mesa-glx`，`libglu1-mesa`，`libgles2-mesa`，`libgl1-mesa-dev`，`libglu1-mesa-dev`和`libgles2-mesa-dev`（写了这么多是因为我不知道具体是哪（几）个，只好都列出来）。</del>
- 在 Linux 平台上使用 MinGW 交叉编译 Windows 版 Qt 时，可以启用 LTO，但`-fno-fat-lto-objects`参数会导致报错（原因未知），因此要修改特定的`mkspec`的部分条目，去掉这个参数
- `QNetworkReply`可能无法处理`redirection`（重定向），原因未知
- 在Ubuntu系统上用Clang为Windows平台交叉编译（mkpsec：win32-clang-g++）时，如果编译静态版Qt，配置时不要添加`-static-runtime`，但编译自己的Qt工程时`CONFIG`条目要添加`static_runtime`（或在`QMAKE_LFLAGS`里添加`-static`），否则会提示找不到符号，原因暂时未知
- 在Ubuntu系统上，使用`win32-clang-g++`这个`mkspec`时，编译静态版Qt可以同时开启`-optimize-size`和`-ltcg`，编译动态版Qt不能开启LTO，原因暂时未知
- 如何使控制台输出的调试信息着色：
   ```bash
   # https://bugreports.qt.io/browse/QTBUG-75415
   export QT_MESSAGE_PATTERN="$(echo '[%{time boot}] %{if-warning}\e[1;33m%{endif}%{if-fatal}\e[31m%{endif}%{if-critical}\e[31m%{endif}%{appname}(%{pid} %{threadid})(%{function})\e[0m:%{if-category} %{category}:%{endif}\t%{message}')"
   ```
   在Qt中使用`void qSetMessagePattern(const QString &pattern)`也可以设置输出模式，但要注意以下几点：
   - 经过我在 Ubuntu 19.04 上的测试，`$(echo ...)`不是必要的，不加它仍然可以改变控制台字体的颜色，加不加完全不影响，加了它反而会在控制台把它作为文字输出
   - **【待确认】**在 Ubuntu 19.04 上测试，【如果使用`qSetMessagePattern()`设置消息模式，要直接使用`Raw String`，不要使用`QStringLiteral`或者`QLatin1String`包裹，否则那几个转义字符会被转换成一般的文字而被输出而不是作为控制字符起作用】，但第二次测试时又不会被`QStringLiteral`和`QLatin1String`影响了，非常奇怪，不知道为什么，以后自己使用时多加注意
- Qt创建快捷方式（Windows）或符号链接（UNIX）：
   ```cpp
   // Creates a link named linkName that points to the file currently specified by fileName().
   bool QFile::link(const QString &linkName);
   // Note: To create a valid link on Windows, linkName must have a .lnk file extension.
   ```
- 包含`QScopedPointer`的类不能使其析构函数内联，例如`~MyClass() override = default;`或`~MyClass() override { ...; }`之类的，否则会无法编译。这也是Qt文档中所提到的。
- 修改鼠标指针：
  ```text
  [static] void QGuiApplication::setOverrideCursor(const QCursor &cursor);
  [static] void QGuiApplication::restoreOverrideCursor();
  特别的，设置为“Qt::BlankCursor”可以隐藏鼠标指针。
  ```
- 如何将`std::cout`、`std::cerr`、`qDebug`、`qWarning`、`qCritical`和`qInfo`输出的调试信息重定向到某一个Widget中：

  参考：https://stackoverflow.com/questions/46927087/redirecting-stdcout-from-dll-in-a-separate-thread-to-qtextedit
- 判断、比较版本号：推荐使用Qt提供的`QVersionNumber`类，直接把版本号的完整字符串传过去，它就能自行识别主版本号、次版本号、修订号和一些其他字符串后缀，也能方便的比较版本号的高低，非常方便好用。
- Qt 6 因为性能原因计划废弃`QStringList`等`QList`类，尽量使用C语言风格的数组（例如`const QString []`）或`QVector`代替。
- Qt 6 计划废弃`QScopedPointer`等`STL`的替代品（即`QTL`），以后尽量使用`STL`提供的类（例如`std::unique_ptr`等）。
- 输出调试信息有两种方式（以`qDebug`为例）：
  ```cpp
  qDebug().noquote() << "Application version:" << qApp->applicationVersion();
  qDebug("Application version: %ls", qUtf16Printable(qApp->applicationVersion()));
  ```
  其中，第一种方式比较方便，但第二种方式性能更好（根据Qt官方开发人员所说），具体使用哪种方式，请自行取舍。
- Qt 5（说Qt5是因为不知道Qt6会不会改）在使用`qInstallMessageHandler`设置回调函数时，不能通过常规方法设置为类的成员函数，一般都是设置为全局函数，如果非要设置为一个类的成员函数，请参考以下示例：
  ```cpp
  // mylogger.h
  #include <QObject>
  class MyLogger : public QObject
  {
      Q_OBJECT
  public:
      explicit MyLogger(QObject *parent = nullptr);
      ~MyLogger() override = default;
      static void customLogger(QtMsgType type, const QMessageLogContext &context, const QString &message);
      void customLoggerImpl(QtMsgType type, const QMessageLogContext &context, const QString &message);
  };
  // mylogger.cpp
  #include "mylogger.h"
  static MyLogger *currentLogHandler = nullptr;
  MyLogger::MyLogger(QObject *parent) : QObject(parent)
  {
      currentLogHandler = this;
      Q_ASSERT(currentLogHandler != nullptr);
      qInstallMessageHandler(customLogger);
  }
  void MyLogger::customLogger(QtMsgType type, const QMessageLogContext &context, const QString &message)
  {
      currentLogHandler->customLoggerImpl(type, context, message);
  }
  void MyLogger::customLoggerImpl(QtMsgType type, const QMessageLogContext &context, const QString &message)
  {
      Q_UNUSED(type) // Q_UNUSED 宏自己带结尾的分号，Qt可能会在以后的版本中去掉结尾的分号，让各位开发者自己添加，请注意
      Q_UNUSED(context)
      Q_UNUSED(message)
  }
  ```
- 如何使Qt编译生成的带版本信息的动态库，文件名末尾不带主版本号：
  ```text
  CONFIG += skip_target_version_ext
  ```
- 如何使QML插件支持静态加载（只是简单的能静态编译是不行的）：
  ```text
  # 以此URI为例
  uri = wangwenx190.QuickMpv
  # Insert the plugins URI into its meta data to enable usage
  # of static plugins in QtDeclarative:
  QMAKE_MOC_OPTIONS += -Muri=$$uri
  # 静态版插件需要将所有资源打包进静态库中，动态链接库不需要
  static: CONFIG += builtin_resources
  else: CONFIG += install_qml_files
  # 资源文件里包含的是插件的qmldir文件和所有.qml文件
  # plugins.qmltypes文件仍然需要放在外面，不需要打包进去
  # 资源的前缀为“/qt-project.org/imports/插件URI（点“.”要换成斜杠“/”）”
  # 此处应为“/qt-project.org/imports/wangwenx190/QuickMpv”
  builtin_resources {
      static_plugin_resources.files = \
          qmldir \
          $$QML_FILES # QML_FILES = $$files(*.qml)
      static_plugin_resources.prefix = /qt-project.org/imports/$$replace(uri, \., /)
      RESOURCES += static_plugin_resources
  }
  # install_qml_files的情况就是简单的复制qmldir文件和.qml文件
  ```
- 如何为目标文件名自动添加对应的后缀（Windows：d，Linux：_debug）：
  ```text
  TARGET = $$qtLibraryTarget(目标文件名)
  ```
  `qtLibraryTarget`这个qmake函数会自动添加平台对应的调试版后缀（发布版本不会添加后缀）。不要用`qt5LibraryTarget`，这个函数除了添加后缀，还会添加`Qt5`这个前缀，是Qt自己的库才需要的函数。
- Qt计划废弃`QStringLiteral`，以后尽量使用`u"..." (→ QStringView)`或者`QLatin1String`代替。其中，`QLatin1String`已经支持`.arg(arg1, ..., argN)`了，已经不存在不使用它的理由了。推荐使用`QLatin1String`的原因是因为它仅仅是对`const char *`的简单封装，性能仅次于直接使用`const char *`。`QString::fromLatin1()`=`QLatin1String`，不推荐使用前者是因为前者打字比较多。`QStringLiteral`性能也不错，但它完整的构建了一个`QString`对象，因此性能也比`QLatin1String`差不少。除非是对性能不敏感的项目，否则一定不要直接用`QString`，这个形式性能最差。如果是对性能不敏感的项目，那么就推荐统一无脑使用`QString`，性能虽然不行，但格式统一，方便管理和理解。
- 使用*QString*时尽量使用*multiArgs*，即`.arg(arg1, ..., argN)`，以前那种分开的写法已经废弃了，不要再用了。`QLatin1String`和`QStringView`也支持这个特性。
- <del>如果在编译Qt或编译Qt的插件时开启`guard:cf`（MSVC专属特性，类MSVC编译器也都支持），Qt会无法加载动态插件（.dll），部分静态插件（.lib）也会因为这个特性而无法加载成功，导致使用Qt开发的程序在启动时崩溃，但在不涉及Qt插件的加载和使用时，编译出的exe大概率是能正常运行的，原因暂时未知，因此先不要启用这个特性。</del>

  <del>注：经观察，微软自己的大部分软件（例如：Office套件）以及火狐浏览器等用户众多的项目，均已开启此特性（exe和dll都启用了`guard:cf`），但仍能正常运行，不明白为什么Qt开启就会崩溃。</del>
- 编译Qt时，如果禁用异常处理，会导致*Qt 3D*，*Qt Location*，*Qt Quick 3D*和*Qt XmlPatterns*无法编译，这四个仓库在编译时都需要开启异常处理，请注意。如果必须禁用异常处理，可以在配置Qt时使用`-skip`参数来跳过这四个仓库。已经编译完成的Qt库不受影响，可以随意开启和关闭异常处理。
- 编译Qt时，如果使用了`Ofast`优化级别（Clang，GCC和ICC都支持，只有MSVC和类MSVC编译器才不支持），会导致Qt的*sqlite*插件无法编译。这个插件不支持高于`O3`的优化级别。然而这个是*QtCore*模块中无法禁用或者跳过的基础插件，如果编译出错就会中断整个Qt的编译，因此只能通过修改这个插件的.pro/.pri文件来规避这个问题（使用自定义的`QMAKE_CFLAGS_RELEASE`和`QMAKE_CXXFLAGS_RELEASE`）。已经编译完成的Qt库不受影响，可以随意调整优化级别。
- 在`Q(Core|Gui)Application`构造前，如何快速将`char **`类型的命令行参数转换为Qt自己的`QStringList`：
  ```cpp
  #include <algorithm>

  QStringList args;
  std::copy(argv + 1, argv + argc, std::back_inserter(args));
  ```
- Windows平台如何获取是否开启深色模式/浅色模式：
  ```cpp
  bool lightModeEnabled() const {
  // Qt 5.14 之前的版本请使用 Q_OS_WIN
  #ifdef Q_OS_WINDOWS
    const QSettings settings(QLatin1String("HKEY_CURRENT_USER\\Software\\Microsoft\\Windows\\CurrentVersion\\Themes\\Personalize"), QSettings::NativeFormat);
    const auto appsUseLightTheme = settings.value(QLatin1String("AppsUseLightTheme"));
    const auto systemUsesLightTheme = settings.value(QLatin1String("SystemUsesLightTheme"));
    return (appsUseLightTheme.isValid() && (appsUseLightTheme.toInt() != 0));
  #else
    return false;
  #endif
  }

  bool darkModeEnabled() const { return !lightModeEnabled(); }
  ```
- Windows平台如何将窗口设置为深色模式：
  ```cpp
  #include <uxtheme.h>
  // Lib 文件：UxTheme.lib，记得要引用此文件
  // 对应的 DLL：UxTheme.dll

  bool setWindowsExplorerDarkTheme(QWidget *widget) {
    if (widget == nullptr) {
      return false;
    }
  #ifdef Q_OS_WINDOWS
  #if QT_VERSION >= QT_VERSION_CHECK(5, 0, 0)
    const auto windowHandle = widget->windowHandle();
    if (windowHandle == nullptr) {
      return false;
    }
    const auto hwnd = reinterpret_cast<HWND>(windowHandle->winId());
  #else
    const auto hwnd = widget->winId();
  #endif
    if (hwnd == nullptr) {
      return false;
    }
    return SUCCEEDED(SetWindowTheme(hwnd, L"DarkMode_Explorer", nullptr));
  #else
    return false;
  #endif
  }
  ```
- Qt5如何从C++方面调用QML函数/信号？
  ```cpp
  QMetaObject::invokeMethod(rootObject, "methodName", Q_ARG(QVariant, methodParameter1), Q_ARG(QVariant, methodParameterN));
  ```
  注意：如果函数带参数，不管其参数都是什么类型，在调用时一定统一使用`QVariant`，否则调用无法成功。这不是Bug，这是Qt官方文档中所提到的注意事项。
- 使程序默认请求管理员权限
  ```text
  QMAKE_LFLAGS += /MANIFESTUAC:\"level=\'requireAdministrator\' uiAccess=\'false\'\"
  ```
- Qt5开启高DPI缩放支持（Qt6默认开启，不需要手动设置）
  ```cpp
  // 一定要在Q(Core|Gui)Application构造前执行，否则不会生效
  QCoreApplication::setAttribute(Qt::AA_EnableHighDpiScaling);
  QCoreApplication::setAttribute(Qt::AA_UseHighDpiPixmaps);
  ```
- 样式表更改：设置样式表、移除样式表、刷新样式表
  ```cpp
  // 使用setStyleSheet设置样式表，如果传一个空字符串，能移除样式表
  void QApplication::setStyleSheet(const QString &sheet);
  void QWidget::setStyleSheet(const QString &sheet);
  // 移除样式表
  void QStyle::unpolish(QWidget *widget);
  void QStyle::unpolish(QApplication *application);
  // 刷新样式表
  void QStyle::polish(QWidget *widget);
  void QStyle::polish(QApplication *application);
  void QStyle::polish(QPalette &palette);
  ```
- 使用Qt内置的标准图标
  ```cpp
  QIcon QStyle::standardIcon(QStyle::StandardPixmap standardIcon, const QStyleOption *option = ..., const QWidget *widget = ...) const
  ```
  注：`QStyle::StandardPixmap`这个枚举里包含很多很常用的标准图标，比如最小化按钮，最大化按钮以及关闭按钮等，尽量多利用，不要再费心费力去第三方网站上找或者自己绘制相关的图片素材了。
- 对`QLCDNumber`控件设置样式，需要将`QLCDNumber`的`segmentStyle`设置为`flat`（`void setSegmentStyle(QLCDNumber::SegmentStyle);`
）。
- 使用inherits判断是否属于某种类
  ```cpp
  QTimer *timer = new QTimer(this);
  timer->inherits("QTimer");          // returns true
  timer->inherits("QObject");         // returns true
  timer->inherits("QAbstractButton"); // returns false
  ```
- 如果出现`Z-order assignment: " is not a valid widget.`这样的错误提示，用记事本打开对应的.ui文件，找到`<zorder></zorder>`为空的地方，删除即可。
- `QComboBox`的`addItem`方法的第二个参数可以给对应的item添加一个数据，并可以用`itemData`这个方法将之取出。
  ```cpp
  void QComboBox::addItem(const QString &text, const QVariant &userData = QVariant());
  QVariant QComboBox::itemData(int index, int role = Qt::UserRole) const;
  ```
- 如果用了`QWebEngine`模块，发布程序的时候必须带上**QtWebEngineProcess.exe**，**translations文件夹**以及**resources文件夹**。
- `QCoreApplication::setAttribute(Qt::AA_NativeWindows);`或`QWidget::setAttribute(Qt::WA_NativeWindow);`可以让每个控件都拥有独立的句柄。
- Qt Creator的配置文件存放在`%APPDATA%\QtProject`，如果发现Qt Creator出问题了，可以将这个文件夹删除，然后重新打开Qt Creator即可。
- `QMediaPlayer`依赖本地解码器，默认状态下仅支持非常有限的格式，Windows平台上下载*K-Lite Codec Pack*或者*LAV Filters*安装即可解决。但`QMediaPlayer`官方没有提供开启硬件解码的接口，默认全部软件解码，对CPU的占用较高。
- `QPushButton`左对齐文字，需要设置样式表`QPushButton{text-align:left;}`。
- 使用SQLite数据库时如果不想产生数据库文件，可以创建内存数据库：
  ```cpp
  QSqlDatabase sqlDatabase = QSqlDatabase::addDatabase(QLatin1String("QSQLITE"));
  sqlDatabase.setDatabaseName(QLatin1String(":memory:"));
  ```
- Qt5提供了`QScroller`这个类直接将控件滚动：
  ```cpp
  ui->listWidget->setHorizontalScrollMode(QListWidget::ScrollPerPixel);
  QScroller::grabGesture(ui->listWidget, QScroller::LeftMouseButtonGesture);
  ```
- 如果运行程序出现`Fault tolerant heap shim applied to current process. This is usually due to previous crashes.`这个错误：

  打开注册表，找到`HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers`，选中`Layers`键值，从右侧列表中删除自己的那个程序路径即可。
- 获取系统窗口标题栏高度：
  ```cpp
  int QStyle::pixelMetric(QStyle::PM_TitleBarHeight);
  ```
  注：`PM_TitleBarHeight`是`QStyle::PixelMetric`这个枚举里的一个，这个枚举里还包含很多系统标准控件的尺寸，都可以用`int QStyle::pixelMetric(QStyle::PixelMetric metric, const QStyleOption *option = nullptr, const QWidget *widget = nullptr) const;`这个函数获取。
- 获取桌面的总尺寸以及可用尺寸（即去掉任务栏后的尺寸）：
  ```cpp
  QRect QWidget::screen()->geometry();
  QRect QWidget::screen()->availableGeometry();
  // 其他还有 QWindow 这个类也支持 QScreen *QWindow::screen() const 这个函数，除此之外再没有其他的类支持这个函数了。
  ```
  注：一定不要再用`QDesktopWidget`这个类了，这个类早就废弃了，会在以后的版本中移除。
- 调用系统默认的程序打开一个文件或网址：
  ```text
  // 如果url是一个本地文件，则调用对应的程序打开。例如，如果url指向了一个word文档，则调用Word或WPS打开它。
  // 如果url是一个网址，则调用默认浏览器打开。
  [static] bool QDesktopServices::openUrl(const QUrl &url);
  ```
- 在保存文件时，尽量使用`QSaveFile`这个类而不是传统的`QFile`，因为`QSaveFile`会在保存前创建一个临时文件，在保存前的一切操作都是作用于那个临时文件，只有写入操作正常完成了才会替换旧的文件，所以不会损坏当前的文件。而`QFile`是直接对当前文件进行操作，因此如果遇到突发情况（例如计算机突然断电等），可能会损坏当前文件。

  注意：`QSaveFile`的用法与`QFile`基本相同，但前者没有`void close()`函数，而是用`bool QSaveFile::commit()`这个函数代替了它。只有执行了`commit`这个函数，对文件所作的一切修改才会生效。
- 判断一个文件是否为快捷方式/软链接，并获取其所指向的目标：
  ```cpp
  // Windows
  bool QFileInfo::isShortcut() const;
  // Unix（包括macOS和iOS）
  bool QFileInfo::isSymLink() const;
  // 获取快捷方式/软链接指向的目标
  QString QFileInfo::symLinkTarget() const;
  ```
  注意：Windows平台尽量不要用`isSymLink`这个函数进行判断，虽然目前（5.14）暂时可以代替`isShortcut`，但此行为已被废弃，Qt会在以后的版本中去掉这个行为。
- 遍历文件夹下的所有文件：
  ```cpp
  QVector<QString> Widget::getFolderContents(const QString &folderPath) const {
      if (folderPath.isEmpty() || !QFileInfo::exists(folderPath) || !QFileInfo(folderPath).isDir()) {
          return {};
      }
      const QFileInfo dirInfo(folderPath);
      const QDir dir(dirInfo.isSymLink() ? dirInfo.symLinkTarget() : dirInfo.canonicalFilePath());
      const auto fileInfoList = dir.entryInfoList(QDir::Files | QDir::Readable | QDir::Hidden | QDir::System, QDir::Name);
      const auto folderInfoList = dir.entryInfoList(QDir::Dirs | QDir::Readable | QDir::Hidden | QDir::System | QDir::NoDotAndDotDot, QDir::Name);
      if (fileInfoList.isEmpty() && folderInfoList.isEmpty()) {
          return {};
      }
      QVector<QString> stringList = {};
      if (!fileInfoList.isEmpty()) {
          for (const auto &fileInfo : fileInfoList) {
              stringList.append(QDir::toNativeSeparators(fileInfo.isSymLink() ? fileInfo.symLinkTarget() : fileInfo.canonicalFilePath()));
          }
      }
      if (!folderInfoList.isEmpty()) {
          for (const auto &folderInfo : folderInfoList) {
              const QVector<QString> _fileList = getFolderContents(folderInfo.isSymLink() ? folderInfo.symLinkTarget() : folderInfo.canonicalFilePath());
              if (!_fileList.isEmpty()) {
                  stringList.append(_fileList);
              }
          }
      }
      return stringList;
  }
  ```
  注：尽量使用`QString QFileInfo::canonicalFilePath() const;`这个函数而不是`QString QFileInfo::absoluteFilePath() const;`或者`QString QFileInfo::filePath() const;`，因为`canonicalFilePath`这个函数会尽可能的解析路径，不会包含`.`、`..`或任何快捷方式/软链接，而且返回的是完整的绝对路径，而后两个函数不会解析的如此彻底。
- 连接信号和槽函数时参数重载：
  ```cpp
  connect(buttonGroup, qOverload<int>(&QButtonGroup::buttonClicked), [=](int id){ /* ... */ });
  connect(buttonGroup, qOverload<QAbstractButton *>(&QButtonGroup::buttonClicked), [=](QAbstractButton *button){ /* ... */ });
  connect(echoComboBox, qOverload<int>(&QComboBox::activated), this, &Window::echoChanged);
  ```
- Qt内置的常用数学函数
  ```cpp
  // 返回t的绝对值
  T qAbs(const T &t);
  // 返回两者中的最大值或最小值
  const T &qMin(const T &a, const T &b);
  const T &qMax(const T &a, const T &b);
  // 返回一个最接近的整数
  int qRound(...);
  qint64 qRound64(...); // 返回64位的整数
  // 返回一个不超过v的最大的整数
  int qFloor(qreal v);
  // 相当于qMax(min, qMin(val, max))，即将val限制在min和max之间
  const T &qBound(const T &min, const T &val, const T &max);
  ```
- 设置Qt默认的图形引擎
  ```cpp
  // 使用 OpenGL（调用桌面版 OpenGL，性能仅次于调用 ANGLE）
  QCoreApplication::setAttribute(Qt::AA_UseDesktopOpenGL);
  // 使用 OpenGL ES（调用 ANGLE，性能最好）
  QCoreApplication::setAttribute(Qt::AA_UseOpenGLES);
  // 使用 Mesa llvmpipe（软件模拟显卡，性能很差）
  QCoreApplication::setAttribute(Qt::AA_UseSoftwareOpenGL);
  ```
  注：

  - 以上代码必须在`Q(Core|Gui)Application`构造前使用，否则不会生效。
  - 也可以通过设置环境变量来实现这个功能（即不写任何代码）：将`QT_OPENGL`设置为`desktop`、`angle`或`software`。
- 设置Qt默认使用的OpenGL版本：
  ```cpp
  QSurfaceFormat surfaceFormat;
  // 此处以4.6版本为例
  surfaceFormat.setMajorVersion(4);
  surfaceFormat.setMinorVersion(6);
  // 使用 QSurfaceFormat::CoreProfile 来禁用老旧的或废弃的 API
  surfaceFormat.setProfile(QSurfaceFormat::CompatibilityProfile);
  QSurfaceFormat::setDefaultFormat(surfaceFormat);
  ```
  注：以上代码必须在`Q(Core|Gui)Application`构造前使用，否则不会生效。
- 如何使能进行翻译的字符串都被`tr`函数包裹，不被遗漏：

  在全局定义`QT_NO_CAST_FROM_ASCII`这个宏，之后在编译时任何没有被`QLatin1String`包裹的字符串都会被提示出来。
- 在Linux平台发布Qt程序：
  - 将所有用到的动态库都复制到二进制文件所在的文件夹（按照Windows平台的组织方式和结构）
  - 将以下脚本保存到二进制文件所在的文件夹下，并重命名为二进制文件的名字（不包括后缀名，脚本的后缀名一直都是.sh）：
    ```sh
    #!/bin/sh
    appname=`basename $0 | sed s,\.sh$,,`
    dirname=`dirname $0`
    tmp="${dirname#?}"
    if [ "${dirname%$tmp}" != "/" ]; then
      dirname=$PWD/$dirname
    fi
    LD_LIBRARY_PATH=$dirname
    export LD_LIBRARY_PATH
    $dirname/$appname "$@"
    ```
  - 每次运行程序都要改为执行这个脚本，不能直接运行二进制文件，否则无法正常启动程序。
- 如何动态的从外部文件加载资源（仅限使用*RCC*工具制作的资源文件）：
  ```text
  // 加载资源文件。其中 mapRoot 参数为资源加载后的根节点。
  [static] bool QResource::registerResource(const QString &rccFileName, const QString &mapRoot = QString());
  // 卸载资源文件
  [static] bool QResource::unregisterResource(const QString &rccFileName, const QString &mapRoot = QString());
  ```
- 如何生成随机数
  ```cpp
  const quint32 value32 = QRandomGenerator::global()->generate();
  const quint64 value64 = QRandomGenerator::global()->generate64();
  // 生成一个在lowest和highest之间的随机数
  const int value = QRandomGenerator::global()->bounded(int lowest, int highest);
  ```
- 无边框窗口
  - 支持`QMainWindow`（稍微改一下也能支持`QWidget`），原生Windows+macOS效果，不支持Linux：https://github.com/Bringer-of-Light/Qt-Nice-Frameless-Window
  - 支持`QMainWindow`、`QWidget`以及Qt Quick，仅支持原生Windows效果，不能跨平台：https://github.com/qtdevs/FramelessHelper
- Qt Quick获取窗口
  ```cpp
  QQmlApplicationEngine qmlApplicationEngine;
  qmlApplicationEngine.load(QUrl(QLatin1String("qrc:///qml/main.qml")));
  auto *window = qobject_cast<QWindow *>(qmlApplicationEngine.rootObjects().at(0));
  ```
  注：在使用`QQuickView`时，由于`QQuickView`本身就是继承自`QWindow`，因此可以直接对其本身进行操作。
- 限制程序只运行一个实例：
  - QtSingleApplication（Qt Solutions是Qt4时代的商用模块，后来开源，但已不再维护，用起来还是没什么问题的，Qt Creator就一直在用这个）：https://github.com/qtproject/qt-solutions/tree/master/qtsingleapplication
  - SingleApplication（基于`QtSingleApplication`修改而来，做了很多改进，还在活跃开发中）：https://github.com/itay-grudev/SingleApplication
