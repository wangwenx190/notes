# Qt 使用笔记

- Windows 平台尽量使用[`JOM`](http://download.qt.io/official_releases/jom/)编译，速度快很多，远远不是`NMake`或者`mingw32-make`能比得上的。

  注：JOM仅支持原生Windows环境，任何Unix或类Unix环境（例如MinGW）均不支持。
- Qt官方下载地址：
  - Qt 官方下载站：<http://download.qt.io/>
  - Qt 国内镜像站（有好多个，这只是其中一个）：<http://mirrors.ustc.edu.cn/qtproject/>
- 在 Windows 平台上编译 Qt 时，编译`ANGLE`时需要一个叫`WindowsSdkVerBinPath`的环境变量，其路径指向`fxc.exe`(Microsoft Direct3D Shader Compiler)所在的文件夹，常见路径为`C:\Program Files (x86)\Windows Kits\10\bin\10.0.17763.0`

  注：`10.0.17763.0`为你所安装的Win SDK的版本号。
- 在 Windows 平台上，如果要编译`ANGLE`，需要安装[`DirectX SDK`](http://www.microsoft.com/en-us/download/details.aspx?id=6812)，新版 DX SDK 已经与[`Windows SDK`](https://developer.microsoft.com/en-us/windows/downloads/sdk-archive)合并了。同时还需要[`Win flex-bison`](https://sourceforge.net/projects/winflexbison/)
- 最新版`Mesa llvmpipe`（`opengl32sw.dll`）下载：<https://github.com/pal1000/mesa-dist-win/releases>
- 在 Linux 平台进行 Qt 开发需要安装额外的库：<https://doc.qt.io/qt-5/linux.html> 。其中，在 Ubuntu 平台上进行开发的官方 Wiki：<https://wiki.qt.io/Install_Qt_5_on_Ubuntu>
- 在 Linux 平台编译 Qt 需要安装额外的库：<https://doc.qt.io/qt-5/linux-requirements.html>
- `QWebEngine`模块不支持静态编译（但静态链接运行时是可以的）
- 在 Windows 平台上，`QWebEngine`模块只能使用最新版的`Visual Studio`编译，不支持其他一切编译器（截至2019-03-27）
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
  | 禁用RTTI | `/GR-`（或留空，不加任何参数） | `-fno-rtti`（clang-cl：与MSVC相同） | `-fno-rtti` | `-fno-rtti`（icl：与MSVC相同） | `CONFIG += rtti_off` |
  | 最高警告级别 | - | - | - | - | `CONFIG += warn_on` |
  | 关闭警告 | - | - | - | - | `CONFIG += warn_off` |
  | 关闭C语言编译器扩展 | - | - | - | - | `CONFIG += strict_c` |
  | 关闭C++语言编译器扩展 | - | - | - | - | `CONFIG += strict_c++` |
  | 开启UTF-8支持 | `/utf-8` | `-finput-charset=UTF-8 -fexec-charset=UTF-8`（clang-cl：与MSVC相同） | `-finput-charset=UTF-8 -fexec-charset=UTF-8` | `-option,cpp,--unicode_source_kind,UTF-8`（icl：`/Qoption,cpp,--unicode_source_kind,UTF-8`） | - |
- `opengl32sw.dll`这个文件是用软件模拟的显卡，针对的是没有显卡的机器，所以只有在极少数情况下才会需要，发布 Qt 程序时不必带上此文件，能极大减小发布大小
- 发布 Windows 平台的 Qt 程序时可以使用 Qt 官方提供的`windeployqt`程序，这个小程序会自动检测并复制相关的 dll 到你的程序文件夹，非常方便。但它无法检测第三方库，必须自行查找并复制。而且这个工具会复制一些多余的 Qt 的 dll，但极难判断究竟哪些是真的无用，因此就不要管了。

  常用参数：

  | 参数 | 附加参数 | 描述 |
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
- QMake用命令行编译 Qt 工程：

  ```bash
  qmake "example.pro" -spec win32-clang-g++ "CONFIG+=release"
  jom qmake_all
  jom
  jom install
  ```

  其中，`-spec`指定了`mkspec`，缺少的话会使用平台默认参数，`CONFIG`指定了额外的配置选项，缺少的话也是使用平台默认参数。

  这个命令行是全平台通用的，只不过最后的`make`过程要根据你的平台修改，例如MinGW使用`mingw32-make`，Unix使用`make`。
- Windows平台可以使用[`Dependency Walker`](http://www.dependencywalker.com/)这个工具来检测你开发的程序具体依赖哪些 dll，非常好用
- 如果你开发的程序不是很大，不推荐使用 Qt 提供的`Installer Framework`(`IFW`)打包，因为这个`IFW`是 Qt 静态编译的，因此文件会比较大，哪怕什么文件都不打包，也会有**20MB**左右的大小，得不偿失。当然，如果你的程序很大，几百兆甚至更大，就可以用它了。小软件推荐：[NSIS](https://sourceforge.net/projects/nsis/)，[Inno Setup](http://jrsoftware.org/isinfo.php)，[WiX Toolset](http://wixtoolset.org/)（制作**MSI**安装包专用）。不推荐：Install Shield（授权费非常昂贵，软件非常臃肿，打包出的安装程序较大，界面不容易定制。不信自己装一个试试），Install Anywhere（与前者是同一个公司的，因此缺点也差不多），Advanced Installer（有授权费），Vice Installer（远古软件），Wise Installation System（远古软件），Smart Install Maker（基本就是个玩具）以及其他任何不知名小软件
- 尽量不要链接`ICU`(`International Components for Unicode`)，虽然它提供了对世界上大多数国家和地区的语言和文字支持，功能非常强大，但文件太大，裁剪前**20~30MB**左右，裁剪后也有**10MB**左右，实际上一般程序根本用不到这种变态级别的国际化支持，所以不推荐使用这个库。Qt 官方也早已去掉了对它的依赖。在配置时给一个`-no-icu`参数就可以禁用`ICU`支持。
- Qt自从5.13版本开始添加了对`Schannel`的支持，可以在配置时通过`-schannel`来启用，以此去掉对`OpenSSL`的依赖（如果完全不需要`OpenSSL`的支持，需要在配置时给一个`-no-openssl`参数来禁用`OpenSSL`）
- QMake区分调试版本和发布版本：`CONFIG(debug, debug|release)`（debug时返回true），`CONFIG(release, debug|release)`（release时返回true），`contains(CONFIG, debug)`（debug时返回true），`contains(CONFIG, release)`（release时返回true），或者更简单的`debug:`和`release:`。在代码中，可以通过`#ifdef _DEBUG`来判断，但请注意，发布版本并没有`_RELEASE`这样的宏。
- QMake区分 Qt 是静态链接还是动态链接的：`CONFIG(static, static|shared)`，`CONFIG(shared, static|shared)`，`contains(CONFIG, static)`，`contains(CONFIG, shared)`，`static:`，`shared:`。在代码中，可以通过`#ifdef QT_STATIC`和`#ifdef QT_SHARED`来判断。
- QMake区分32位还是64位：`contains(QMAKE_TARGET.arch, x86_64)`，`contains(QMAKE_HOST.arch, x86_64)`，`contains(QT_ARCH, x86_64)`，以上三条语句在编译64位程序时返回true。其中`x86_64`替换为其他架构，例如`i386`，也是可行的，只不过判断的就不一定是64位了。在代码中，Windows 平台上可以通过`#ifdef WIN64`或`#ifdef _WIN64`来判断是不是64位，不要用`WIN32`来判断，因为`WIN32`这个会在两个架构上都有定义。
- QMake区分应用程序和库：`contains(TEMPLATE, app)`，`contains(TEMPLATE, lib)`
- Windows 平台上添加图标及属性信息：
  - QMake：

     ```text
     # 设置版本。只有这个变量是全平台通用的。其中，只有 Windows 平台上的版本号是4位的，其他平台都是3位
     VERSION = 1.2.3
     # 设置图标。必须为.ico格式，且最大 256x256
     RC_ICONS = "../res/icon.ico"
     # 设置代码页
     #RC_CODEPAGE = ""
     # 设置语言。具体的值请参考：https://docs.microsoft.com/en-us/windows/win32/intl/language-identifier-constants-and-strings
     RC_LANG = 0x0004
     # 设置产品名称
     QMAKE_TARGET_PRODUCT = "My app name"
     # 设置文件说明
     QMAKE_TARGET_DESCRIPTION = "My app description"
     # 设置版权
     QMAKE_TARGET_COPYRIGHT = "My copyright info"
     # 设置公司
     QMAKE_TARGET_COMPANY = "My company name"
     ```

     如果你有一个已经编写好的资源脚本（.rc文件），可以用以下语句使用：

     ```text
     RC_FILE = "../res/eg.rc"
     ```

     注意：这两者只能二选一，设置了资源脚本就不能再单独设置图标或其他属性信息了，这两者是冲突的。

  - CMake：

    在`add_executable`或`add_library`（仅限动态库）时将.rc文件作为源文件一起加入即可。

   注意：同一个exe文件最多只能有一个资源脚本，如果要嵌入多个文件作为资源到二进制文件中，要将它们写入到同一个资源脚本中。
- QMake修改二进制文件输出目录：`DESTDIR = $$PWD/bin`，在 Windows 平台上，dll 文件专有一个`DLLDESTDIR`。
- `.qmake.conf`文件：将此文件放在`.pro`文件所在的文件夹中，qmake会自动加载这个文件，并且会在加载`.pro`文件前先加载它，因此可以将一些通用的配置写到这个文件中
- `qt.conf`文件：将此文件放在你开发的程序所在的文件夹中，可以修改 Qt 加载某些库和翻译文件（`.qm`文件）的路径，个别场景会用到这个文件，具体请查看 Qt 帮助文档。在代码中也可以动态获取，例如，可以使用`QLibraryInfo::location(QLibraryInfo::TranslationsPath)`来获取`qt.conf`文件中指定的翻译文件的路径，但就算文件中没有指定，或者这个文件根本不存在，Qt 自身也会提供一个默认路径，例如，插件默认为`plugins`文件夹，翻译文件默认为`translations`文件夹等。**注意：如果你是在Qt Creator中运行你的Qt程序（例如你正在调试你的程序），那么`QLibraryInfo::location()`返回的就是当前正在使用的Qt Kit的相关路径，而不是你自己在qt.conf中所设置的路径。换句话说，在这个时候这个函数不会返回你应用程序所在文件夹的路径了。但只要不是用Qt Creator运行，就没有这个问题。**
- `QFile`无法在不存在的文件夹中创建文件，只能在已经存在的路径中新建或修改文件。如果路径不存在，可以使用`bool QDir::mkpath(const QString &dirPath) const`创建，使用这个API，路径中任何不存在的文件夹都会被创建。
- 区分操作系统：编译时可以通过`Q_OS_WINDOWS`（Qt 5.14 添加，早期版本请使用`Q_OS_WIN`代替），`Q_OS_WINRT`，`Q_OS_LINUX`，`Q_OS_MACOS`（不要使用`Q_OS_MAC`，已废弃），`Q_OS_MINGW`，`Q_OS_ANDROID`和`Q_OS_IOS`等宏来区分，运行时可以通过`QOperatingSystemVersion`或`QSysInfo`这两个类来获取
- 区分编译器：编译时可以通过`Q_CC_MSVC`，`Q_CC_GNU`（GCC，G++，MinGW），`Q_CC_INTEL`（ICC），`Q_CC_CLANG`等宏来区分
- 交叉编译：`configure -xplatform win32-clang-g++ -device-option CROSS_COMPILE=x86_64-w64-mingw32-`，其中，`CROSS_COMPILE`参数前面没有`-`，它前面还要跟一个单独的`-device-option`，而且`x86_64-w64-mingw32-`末尾的`-`不能丢
- 在 Windows 平台上编译 Qt 时，如果没有显式的指定，所有第三方库都会用 Qt 自带的。而在 Linux 平台上相反，Qt 会自动使用系统中已经存在的第三方库，默认是不会使用自带的第三方库的。
- QMake编译完成后实现自定义动作：

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

- QMake子项目：

   ```text
   TEMPLATE = subdirs #此句不可缺少
   CONFIG -= ordered #这一句看你项目的具体情况，“CONFIG += ordered”的意思是让qmake按照你在.pro文件中书写的顺序来生成项目
   project1.file = src/pro1.pro #指定子项目的路径
   project2.subdir = src/3rdparty/pro2 #这样的写法和上一句是等价的，但这样写的前提是这个文件夹中只有一个.pro文件
   project2.depends += project1 #此处为设置不同项目之间的依赖关系，被依赖的项目会首先生成
   SUBDIRS += project1 project2 #这一句是最终把子项目添加到总项目中
   ```

- QMake包含`.pri`文件：`include(../test.pri)`。理论上任何能写入`.pro`文件的内容都可以写到`.pri`文件中，但通常大家都是把公用的配置选项或者个别非常小的子项目写到`.pri`文件中。
- [QMake] Qt 5.12系列添加了`lrelease`和`embed_translations`这两个`CONFIG`选项，前者可以使 Qt 在编译完成后自动调用`lrelease`程序，即编译完成后自动更新翻译，后者可以使 Qt 在编译完成后自动把翻译文件（`.qm`文件）嵌入到程序中，并添加`i18n`前缀，可以通过 Qt 的资源系统直接访问，例如，`:/i18n/qt_zh_CN.qm`或`qrc:///i18n/qtbase_uk.qm`。
- [QMake] `versionAtLeast(QT_VERSION, 5.12.0)`和`versionAtMost(QT_VERSION, 5.14.2)`：Qt 5.9系列添加的比较版本号专用的函数，作用看函数名就明白了。直接把版本号写在逗号后面就可以，不用带双引号。`QT_VERSION`这个变量记录了 Qt 的版本号，可以用这个做一些判断，来实现一些特别的操作。
- QMake可以用`qtHaveModule`来判断 Qt 有没有安装某模块，例如：`qtHaveModule(winextras)`
- QMake可以用`qtConfig`来判断 Qt 在编译时有没有开启某特性，例如：`qtConfig(draganddrop)`。全部特性可以使用`configure -list-features`查看。
- QRC资源文件别名：`<file alias="cut-img.png">images/cut.png</file>`。可以通过`alias`来给`.qrc`文件中的资源文件起别名，优点在于可以不依赖于文件的实际路径。
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
   | QT_VERSION | the Qt version. We recommend that you query Qt module specific version numbers by using $$QT.\<module\>.version variables instead. |

   获取方法：`$$[var_name]`。例如：

   ```bash
   bin_dir = $$[QT_INSTALL_BINS] #一定不要忘了方括号
   #bin_dir: C:\Qt\Qt5.14.0\5.14.0\msvc2019_64\bin
   ```

   摘自：<https://doc.qt.io/qt-5/qmake-environment-reference.html>
- 在 Windows 平台上使用`MinGW`编译 Qt 时不能使用`JOM`，也不能开启 LTO，否则会报错，不清楚具体的原因
- 在 Windows 平台上如果想要嵌入 Manifest 文件，可以插入到资源文件中，但我试过几次，效果都不理想，可能是方法有问题
- 编译 Qt 时常用的参数：

   | 参数 | 作用 |
   | --- | --- |
   | `-opensource` | 编译开源版本，许可协议为 LGPLv3/GPLv3 |
   | `-confirm-license` | 同意许可协议。加上这个参数会跳过手动同意许可协议这个步骤。 |
   | `-debug` | 编译调试版本 |
   | `-release` | 编译发布版本 |
   | `-debug-and-release` | 同时编译调试版本和发布版本 |
   | `-force-debug-info` | 即使是发布版本也生成调试信息 |
   | `-shared` | 编译为动态库（.dll/.so）（默认） |
   | `-static` | 编译为静态库（.lib/.a） |
   | `-static-runtime` | 静态链接运行时（仅限 MSVC，相当于 -MT/-MTd） |
   | `-platform <platform>` | 设置目标平台（Windows默认MSVC，Linux默认G++） |
   | `-xplatform <platform>` | 设置目标平台（交叉编译时） |
   | `-optimize-size` | 为大小优化（MSVC：O1，GCC：Os，Clang：Oz） |
   | `-ltcg` | 启用 LTCG/LTO |
   | `-silent` | 隐藏不必要的信息，仅输出警告和错误 |
   | `-nomake <part>` | 不构建某部分，仅可在 libs，tools，examples 和 tests 这四者中选择 |
   | `-skip <repository>` | 跳过某模块的构建，例如 qt3d，qtactiveqt 和 qtandroidextras 等 |
   | `-no-feature-<feature>` | Qt Lite Project的功能，去掉某特性的支持，可以进一步裁剪Qt的大小，例如 `-no-feature-clipboard` |
   | `-prefix <path>` | 指定安装目录 |
   | `-linker <linker>` | 指定链接器，仅可在 bfd，gold 和 lld 这三者中选择（仅限 Linux 平台） |
   | `-pch` | 启用预编译头（自动） |
   | `-warnings-are-errors` | 把警告视为错误 |
   | `-opengl <api>` | 选择默认的OpenGL版本。仅可在 es2，desktop 和 dynamic 这三者中选择，其中，Windows 平台默认为 dynamic，Linux 平台默认为 desktop |
- 在 Linux 平台上交叉编译 Windows 版 Qt 时，需要安装`glibc`。Ubuntu 平台上的包名为`libc6-dev-i386`，`libc6-dev-amd64`，`libc6-dev-i386-cross`，`libc6-dev-amd64-cross`，`libc6-dev-i386-amd64-cross`和`libc6-dev-amd64-i386-cross`（写了这么多是因为我不知道具体是哪（几）个，只好都列出来）。
- 在 Linux 平台上使用 MinGW 交叉编译 Windows 版 Qt 时，可以启用 LTO，但`-fno-fat-lto-objects`参数会导致报错（原因未知），因此要修改特定的`mkspec`的部分条目，去掉这个参数
- 在Ubuntu系统上用Clang为Windows平台交叉编译（mkpsec：win32-clang-g++）时，如果编译静态版Qt，配置时不要添加`-static-runtime`，但编译自己的Qt工程时`CONFIG`条目要添加`static_runtime`（或在`QMAKE_LFLAGS`里添加`-static`），否则会提示找不到符号，原因暂时未知
- 在Ubuntu系统上，使用`win32-clang-g++`这个`mkspec`时，编译静态版Qt可以同时开启`-optimize-size`和`-ltcg`，编译动态版Qt不能开启LTO，原因暂时未知
- 如何使控制台输出的调试信息着色：

  ```bash
  # https://bugreports.qt.io/browse/QTBUG-75415
  export QT_MESSAGE_PATTERN="$(echo '[%{time boot}] %{if-warning}\e[1;33m%{endif}%{if-fatal}\e[31m%{endif}%{if-critical}\e[31m%{endif}%{appname}(%{pid} %{threadid})(%{function})\e[0m:%{if-category} %{category}:%{endif}\t%{message}')"
  ```

  在Qt中使用`void qSetMessagePattern(const QString &pattern)`也可以设置输出模式，但要注意以下几点：

  - 经过我在 Ubuntu 19.04 上的测试，`$(echo ...)`不是必需的，不加它仍然可以改变控制台字体的颜色，加了它反而会在控制台把它作为文字输出
  - **【待确认】**在 Ubuntu 19.04 上测试，【如果使用`qSetMessagePattern()`设置消息模式，要直接使用`Raw String`，不要使用`QStringLiteral`或者`QLatin1String`包裹，否则那几个转义字符会被转换成一般的文字而被输出而不是作为控制字符起作用】，但第二次测试时又不会被`QStringLiteral`和`QLatin1String`影响了，非常奇怪，不知道为什么，以后自己使用时多加注意
- Qt创建快捷方式（Windows）或符号链接（UNIX）：

   ```cpp
   // 创建一个名为 linkName 的快捷方式/软链接，目标文件指向 fileName()
   bool QFile::link(const QString &linkName);
   // 特别的，在 Windows 平台上创建快捷方式时，必须以 .lnk 作为文件的后缀名
   ```

- 包含`QScopedPointer`/`std::unique_ptr`的类不能使其析构函数内联，例如`~MyClass() override = default;`或`~MyClass() override { ...; }`之类的，否则会无法编译。
- 修改鼠标指针：

  ```text
  [static] void QGuiApplication::setOverrideCursor(const QCursor &cursor);
  [static] void QGuiApplication::restoreOverrideCursor();
  // 请参考 Qt::CursorShape 这个枚举来获取更多鼠标指针的不同形状
  // 特别的，设置为“Qt::BlankCursor”可以隐藏鼠标指针。
  ```

- 如何将`std::cout`、`std::cerr`、`qDebug`、`qWarning`、`qCritical`和`qInfo`输出的调试信息重定向到某一个Widget中：

  参考：<https://stackoverflow.com/questions/46927087/redirecting-stdcout-from-dll-in-a-separate-thread-to-qtextedit>
- 判断、比较版本号：推荐使用Qt提供的`QVersionNumber`类，直接把版本号的完整字符串传过去，它就能自行识别主版本号、次版本号、修订号和一些其他字符串后缀，也能方便的比较版本号的高低，非常方便好用。
- Qt 6 因为性能原因计划废弃`QStringList`等`QList`类，尽量使用C语言风格的数组（例如`const QString []`）或`QVector`代替。
- Qt 6 计划废弃`QScopedPointer`等`STL`的替代品（即`QTL`），以后尽量使用`STL`提供的类（例如`std::unique_ptr`等）。
- 输出调试信息有两种方式（以`qDebug`为例）：

  ```cpp
  qDebug().noquote() << "Application version:" << qApp->applicationVersion();
  qDebug("Application version: %ls", qUtf16Printable(qApp->applicationVersion()));
  ```

  两者基本只是形式上有所不同，用起来大差不差，具体使用哪种方式，请自行取舍。
- Qt5将Qt默认的日志输出函数（`qInfo`、`qDebug`、`qWarning`、`qCritical`以及`qFatal`）设置为自己的函数：`QtMessageHandler qInstallMessageHandler(QtMessageHandler handler);`。其中，`handler`为一个全局函数的指针，返回值为当前回调函数的指针。使用`qInstallMessageHandler(nullptr)`来恢复Qt默认的回调函数。

  ```cpp
  #include <qapplication.h>
  #include <stdio.h>
  #include <stdlib.h>

  void myMessageOutput(QtMsgType type, const QMessageLogContext &context, const QString &msg)
  {
      QByteArray localMsg = msg.toLocal8Bit();
      const char *file = context.file ? context.file : "";
      const char *function = context.function ? context.function : "";
      switch (type) {
      case QtDebugMsg:
          fprintf(stderr, "Debug: %s (%s:%u, %s)\n", localMsg.constData(), file, context.line, function);
          break;
      case QtInfoMsg:
          fprintf(stderr, "Info: %s (%s:%u, %s)\n", localMsg.constData(), file, context.line, function);
          break;
      case QtWarningMsg:
          fprintf(stderr, "Warning: %s (%s:%u, %s)\n", localMsg.constData(), file, context.line, function);
          break;
      case QtCriticalMsg:
          fprintf(stderr, "Critical: %s (%s:%u, %s)\n", localMsg.constData(), file, context.line, function);
          break;
      case QtFatalMsg:
          fprintf(stderr, "Fatal: %s (%s:%u, %s)\n", localMsg.constData(), file, context.line, function);
          break;
      }
  }

  int main(int argc, char *argv[])
  {
      qInstallMessageHandler(myMessageOutput);
      QApplication application(argc, argv);
      ...
      return QApplication::exec();
  }
  ```

  这个函数的作用是可以修改日志输出的格式，但它最大的用途是可以利用自定义的回调函数将日志信息写入到文件。
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

- QMake如何使Qt编译生成的带版本信息的动态库，文件名末尾不带主版本号：

  ```text
  CONFIG += skip_target_version_ext
  ```

- QMake如何使QML插件支持静态加载（只是简单的能静态编译是不行的）：

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

- QMake如何为目标文件名自动添加对应的后缀（Windows：d，Linux：_debug）：

  ```text
  TARGET = $$qtLibraryTarget(目标文件名)
  ```

  `qtLibraryTarget`这个qmake函数会自动添加平台对应的调试版后缀（发布版本不会添加后缀），如果是Linux平台，这个函数还会添加lib前缀。不要用`qt5LibraryTarget`，这个函数除了添加后缀，还会添加`Qt5`这个前缀，是Qt自己的库才需要的函数。
- Qt计划废弃`QStringLiteral`，以后尽量使用`u"..." (→ QStringView)`或者`QLatin1String`代替。其中，`QLatin1String`已经支持`.arg(arg1, ..., argN)`了，已经不存在不使用它的理由了。推荐使用`QLatin1String`的原因是因为它仅仅是对`const char *`的简单封装，性能仅次于直接使用`const char *`。`QString::fromLatin1()`=`QLatin1String`，不推荐使用前者是因为前者打字比较多。`QStringLiteral`性能也不错，但它完整的构建了一个`QString`对象，因此性能也比`QLatin1String`差不少。除非是对性能不敏感的项目，否则一定不要直接用`QString`，这个形式性能最差。如果是对性能不敏感的项目，那么就推荐统一无脑使用`QString`，性能虽然不行，但格式统一，方便管理和理解。

  顾名思义，`QLatin1String`针对的是拉丁字符，如果是非拉丁字符，是不能用这个宏的。
- 使用*QString*时尽量使用*multiArgs*，即`.arg(arg1, ..., argN)`，以前那种分开的写法已经废弃了，不要再用了。`QLatin1String`和`QStringView`也支持这个特性。
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
  #if (QT_VERSION >= QT_VERSION_CHECK(5, 0, 0))
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
- QMake使程序默认请求管理员权限（Windows平台）

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
- 使用`inherits`函数判断是否属于某个类

  ```cpp
  QTimer *timer = new QTimer(this);
  timer->inherits("QTimer");          // returns true
  timer->inherits("QObject");         // returns true
  timer->inherits("QAbstractButton"); // returns false
  ```

- 如何解决`Z-order assignment: " is not a valid widget.`这样的错误：

  用记事本打开对应的.ui文件，找到`<zorder></zorder>`为空的地方，删除即可。
- `QComboBox`的`addItem`方法的第二个参数可以给对应的item添加一个数据，并可以用`itemData`这个方法将之取出。

  ```cpp
  void QComboBox::addItem(const QString &text, const QVariant &userData = QVariant());
  QVariant QComboBox::itemData(int index, int role = Qt::UserRole) const;
  ```

- 如果用了`QWebEngine`模块，发布程序的时候必须带上**QtWebEngineProcess.exe**，**translations文件夹**以及**resources文件夹**。
- 如何使每个控件都拥有独立的句柄：

  ```cpp
  // 下面两个方法都可以，选一个即可
  QCoreApplication::setAttribute(Qt::AA_NativeWindows);
  QWidget::setAttribute(Qt::WA_NativeWindow);
  ```

- Qt Creator的配置文件存放在`%APPDATA%\QtProject`，如果发现Qt Creator出问题了，可以将这个文件夹删除，然后重新打开Qt Creator即可。
- `QMediaPlayer`依赖本地解码器，默认状态下仅支持非常有限的格式，Windows平台上下载[*K-Lite Codec Pack*](http://www.codecguide.com/download_k-lite_codec_pack_mega.htm)或者[*LAV Filters*](https://github.com/Nevcairiel/LAVFilters/releases/latest)安装即可解决。但`QMediaPlayer`官方没有提供开启硬件解码的接口，默认全部软件解码，对CPU的占用较高。
- 如何使`QPushButton`的文字左对齐：

  设置样式表`QPushButton{text-align:left;}`。
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

- 如何解决`Fault tolerant heap shim applied to current process. This is usually due to previous crashes.`这样的错误：

  打开注册表，找到`HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers`，选中`Layers`键值，从右侧列表中删除自己的那个程序路径即可。
- 获取系统窗口标题栏高度：

  ```cpp
  int QStyle::pixelMetric(QStyle::PM_TitleBarHeight);
  ```

  注：`PM_TitleBarHeight`是`QStyle::PixelMetric`这个枚举里的一个，这个枚举里还包含很多系统标准控件的尺寸，都可以用`int QStyle::pixelMetric(QStyle::PixelMetric metric, const QStyleOption *option = nullptr, const QWidget *widget = nullptr) const;`这个函数获取。
- 获取桌面的总尺寸以及可用尺寸（即去掉任务栏后的尺寸）：

  ```cpp
  QRect QWidget::screen()->geometry() const; // 总尺寸
  QRect QWidget::screen()->availableGeometry() const; // 可用尺寸
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

  注意事项：由于是遍历文件夹下的所有文件（包括所有子文件夹），因此当文件数量很多时，函数执行的时间会比较长，进而导致用户体验不好，所以尽量在协程或多线程中使用这个函数，尽量不要直接在UI线程中阻塞式的调用。
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
  // 相当于qMax(min, qMin(val, max))，即：如果val在min和max之间，则返回其本身，如果val超过了max，则返回max，如果val小于min，则返回min。
  const T &qBound(const T &min, const T &val, const T &max);
  ```

- 设置Qt默认的图形引擎

  ```cpp
  // 使用 OpenGL（调用桌面版 OpenGL，性能仅次于调用 ANGLE）
  QCoreApplication::setAttribute(Qt::AA_UseDesktopOpenGL);
  // 使用 OpenGL ES（调用 ANGLE，性能最好）
  QCoreApplication::setAttribute(Qt::AA_UseOpenGLES);
  // 使用 Mesa llvmpipe（软件模拟显卡，性能很差，正常情况下尽量不要使用）
  QCoreApplication::setAttribute(Qt::AA_UseSoftwareOpenGL);
  ```

  注：

  - 以上代码必须在`Q(Core|Gui)Application`构造前使用，否则不会生效。
  - 也可以通过设置环境变量来实现这个功能（即不写任何代码）：将`QT_OPENGL`设置为`desktop`、`angle`或`software`。当使用ANGLE时，设置`QT_ANGLE_PLATFORM`这个环境变量为`d3d11`、`d3d9`或`warp`可以修改ANGLE默认使用的DirectX版本。
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

  注：以上代码必须在`Q(Core|Gui)Application`构造前使用，否则不会生效。【？？？待确认？？？】
- 如何使能进行翻译的字符串都被`tr`函数包裹，不被遗漏：

  在全局定义`QT_NO_CAST_FROM_ASCII`这个宏，之后在编译时任何没有被`QLatin1String`包裹的字符串都会被提示出来（或直接报错？不太确定）。
- 在Linux平台发布Qt程序：
  - 将所有用到的动态库都复制到二进制文件所在的文件夹。可以使用一个叫`linuxdeployqt`的第三方小工具（Qt官方没有提供类似的工具）：<https://github.com/probonopd/linuxdeployqt>
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
  // 生成一个32位随机数（正整数）
  const quint32 value32 = QRandomGenerator::global()->generate();
  // 生成一个64位随机数（正整数）
  const quint64 value64 = QRandomGenerator::global()->generate64();
  // 生成一个小于highest的随机数（整数）
  const int value = QRandomGenerator::global()->bounded(int highest);
  // 生成一个在lowest和highest之间的随机数（整数）
  const int value = QRandomGenerator::global()->bounded(int lowest, int highest);
  // 生成一个大于等于0且小于highest的随机数（正整数）
  const quint32 value = QRandomGenerator::global()->bounded(quint32 highest);
  // 生成一个在lowest和highest之间的随机数（正整数）
  const quint32 value = QRandomGenerator::global()->bounded(quint32 lowest, quint32 highest);
  ```

- 无边框窗口：自定义标题栏+8向拉伸+窗口阴影
  - 支持`QMainWindow`（稍微改一下也能支持`QWidget`），原生Windows+macOS效果，不支持Linux：<https://github.com/Bringer-of-Light/Qt-Nice-Frameless-Window>
  - 支持`QMainWindow`、`QWidget`以及Qt Quick，仅支持原生Windows效果，不能跨平台：<https://github.com/qtdevs/FramelessHelper>
  - 支持Qt大多数的类，跨平台，但不支持任何系统原生效果：<https://github.com/feiyangqingyun/QWidgetDemo/tree/master/framelesswidget>
- Qt Quick获取正在显示QML文档的系统窗口

  ```cpp
  QQmlApplicationEngine qmlApplicationEngine;
  qmlApplicationEngine.load(QUrl(QLatin1String("qrc:///qml/main.qml")));
  auto *window = qobject_cast<QWindow *>(qmlApplicationEngine.rootObjects().at(0));
  ```

  注：在使用`QQuickView`时，由于`QQuickView`本身就是继承自`QWindow`，因此可以直接对其本身进行操作。
- 限制程序只运行一个实例：
  - `QtSingleApplication`（*Qt Solutions*是Qt4时代的商用模块，后来开源，但已不再维护，用起来还是没什么问题的，Qt Creator就一直在用这个）：<https://github.com/qtproject/qt-solutions/tree/master/qtsingleapplication>

    原理是使用`Qt Network`模块的`QLocalServer`以及`QLocalSocket`建立一个本地服务器，实现进程间通信。由于`Qt Network`模块是跨平台的，所以这个项目理论上也是能跨平台的。
  - `SingleApplication`（基于`QtSingleApplication`修改而来，做了很多改进，还在活跃开发中）：<https://github.com/itay-grudev/SingleApplication>

    使用了TCP/IP以及共享内存等多种技术，理论上也是跨平台的。
- 进程间通信（IPC）：7种方式
  - 4种跨平台方式：
    - Qt Remote Objects（QtRO）：使用`Qt Remote Objects`模块实现
    - TCP/IP：使用`Qt Network`模块的`QLocalServer`以及`QLocalSocket`实现
    - 共享内存：使用`Qt Core`模块的`QSharedMemory`或`QSystemSemaphore`/`QSemaphore`实现
    - QProcess
  - 3种平台独有方式：
    - Windows：消息

      发送消息：`LRESULT SendMessage(HWND hWnd, UINT Msg, WPARAM wParam, LPARAM lParam);`

      ```cpp
      // 头文件：winuser.h (include Windows.h)
      // 库文件：User32.lib（User32.dll）
      // 此处的“Demo program”为程序窗口的标题，注意标题一定不能变，否则会获取不到窗口的句柄
      const HWND hwnd = FindWindow(nullptr, TEXT("Demo program"));
      if (IsWindow(hwnd)) {
        const QString message = QLatin1String("My message");
        const QByteArray data = message.toUtf8();
        COPYDATASTRUCT copydata;
        // 此处的“10000”为用户自定义的消息类型，可以为其他数值，但要注意一定不要与系统或其他程序的消息类型重复了。
        copydata.dwData = ULONG_PTR(10000);
        copydata.lpData = data.data();
        copydata.cbData = data.size();
        SendMessage(hwnd, WM_COPYDATA, reinterpret_cast<WPARAM>(effectiveWinId()), reinterpret_cast<LPARAM>(&copydata));
      }
      ```

      接收消息：重写`[virtual protected] bool QWidget::nativeEvent(const QByteArray &eventType, void *message, long *result);`函数

      ```cpp
      bool nativeEvent(const QByteArray &eventType, void *message, long *result) {
        const auto *param = static_cast<MSG *>(message);
        switch (param->message) {
          case WM_COPYDATA: {
            const auto *cds = reinterpret_cast<COPYDATASTRUCT *>(param->lParam);
            // 此处的“10000”为用户自定义的消息类型，可以为其他数值，但要注意一定要与发送消息时函数所用的数值相同。
            if (cds->dwData == ULONG_PTR(10000)) {
              const QString strMessage = QString::fromUtf8(reinterpret_cast<char *>(cds->lpData), cds->cbData);
              QMessageBox::information(this, tr("Received message"), strMessage);
              *result = 1;
              return true;
            }
          }
        }
        return QWidget::nativeEvent(eventType, message, result);
      }
      ```

    - Linux：D-Bus
    - Linux：Session Management
- 下载文件

  ```cpp
  QNetworkAccessManager manager;
  connect(&manager, &QNetworkAccessManager::finished, this, [](QNetworkReply *reply) {
    // 重定向不会导致错误的发生
    if (reply->error() != QNetworkReply::NoError) {
      // 处理错误
    } else {
      // 暂时还不知道在下载文件时怎么处理重定向，先跳过这种情况
      const int statusCode = reply->attribute(QNetworkRequest::HttpStatusCodeAttribute).toInt();
      if ((statusCode == 301) || (statusCode == 302) || (statusCode == 303) || (statusCode == 305) || (statusCode == 307) || (statusCode == 308)) {
        // 处理重定向
        QVariant target = reply->attribute(QNetworkRequest::RedirectionTargetAttribute);
        if (target.isValid()) {
          QUrl redirectUrl = target.toUrl();
          if (redirectUrl.isRelative()) {
            QUrl requestUrl = reply->request().url();
            redirectUrl = requestUrl.resolved(redirectUrl);
          }
          QTextStream(stderr) << tr("Redirected to: ") << redirectUrl.toDisplayString() << endl;
        }
      } else {
        QFile file(QLatin1String("D:/test.dat"));
        file.open(QFile::WriteOnly);
        file.write(reply->readAll());
        file.close();
      }
    }
  });
  const QUrl url = QUrl::fromEncoded("https://download.qt.io/balabalabala.dat");
  QNetworkRequest request(url);
  manager.get(request);
  ```

- 计算文件哈希值：`QCryptographicHash`

  ```cpp
  QFile file(QLatin1String("D:/setup.exe"));
  file.open(QFile::ReadOnly);
  // 其他算法请参考 QCryptographicHash::Algorithm 这个枚举。
  QCryptographicHash cryptographicHash(QCryptographicHash::Sha256);
  cryptographicHash.addData(file.readAll());
  file.close();
  const QString hash = QLatin1String(cryptographicHash.result().toHex());
  ```

- Qt框架下的服务程序（Windows services与Unix daemons）

  请参考：<https://github.com/qtproject/qt-solutions/tree/master/qtservice>

  支持 Windows + Unix 平台，但早已经停止维护了（2011年左右），是一个比较老的代码库。用还是能用的，只不过可能用了不少现在看来已经非常过时的技术。
- 获取部分硬件信息
- 获取操作系统的详细信息

  ```text
  // 返回Qt的完整架构。例如：i386-little_endian-ilp32
  [static] QString QSysInfo::buildAbi();
  // 返回Qt面向的CPU的完整架构。
  [static] QString QSysInfo::buildCpuArchitecture();
  // 返回当前正在运行的CPU的完整架构。
  [static] QString QSysInfo::currentCpuArchitecture();
  // 返回Qt面向的操作系统的内核类型（同时也是当前操作系统的内核类型）
  [static] QString QSysInfo::kernelType();
  // 返回当前操作系统的内核版本。
  [static] QString QSysInfo::kernelVersion();
  // 返回当前计算机的主机名
  [static] QString QSysInfo::machineHostName();
  // 返回更加易读的产品名（内核类型+内核版本，但会更易读。例如：Windows 10 Version 1903）
  [static] QString QSysInfo::prettyProductName();
  // 返回当前操作系统的产品名。例如：android、osx、ios、tvos、watchos、darwin、debian、winrt或windows
  [static] QString QSysInfo::productType();
  // 返回当前操作系统的产品版本。例如：16.10（Ubuntu 16.10）
  [static] QString QSysInfo::productVersion();
  ```

- 修改`qInfo`、`qDebug`、`qWarning`、`qCritical`以及`qFatal`输出信息时的默认格式：`void qSetMessagePattern(const QString &pattern);`

  | 占位符 | 描述 |
  | ----- | ---- |
  | `%{appname}` | `QCoreApplication::applicationName()` |
  | `%{category}` | 日志类别 |
  | `%{file}` | 源文件路径 |
  | `%{function}` | 函数名 |
  | `%{line}` | 行号 |
  | `%{message}` | 日志实际的消息 |
  | `%{pid}` | `QCoreApplication::applicationPid()` |
  | `%{threadid}` | The system-wide ID of current thread (if it can be obtained) |
  | `%{qthreadptr}` | 当前`QThread`的指针（`QThread::currentThread()`的返回值） |
  | `%{type}` | 日志类型：“debug”，“warning”，“critical”或“fatal” |
  | `%{time process}` | 输出日志时的时间，以进程启动以来的秒数为基准 |
  | `%{time boot}` | 输出日志时的时间，以系统启动以来的秒数为基准 |
  | `%{time [format]}` | 输出日志时的系统时间，以`format`为输出格式 |
  | `%{backtrace [depth=N] [separator="..."]}` | 暂时不太了解 |
- 读写XML文件

  读取XML：`QXmlStreamReader`

  ```cpp
  QXmlStreamReader xml;
  while (xml.readNextStartElement()) {
    if (xml.name() == QLatin1String("folder")) {
      const bool folded = (xml.attributes().value(QLatin1String("folded")) != QLatin1String("no"));
      const QString title = xml.readElementText();
      // ...
    }
    else if (xml.name() == QLatin1String("bookmark"))
      // ...
    else if (xml.name() == QLatin1String("separator"))
      // ...
    else
      xml.skipCurrentElement();
  }
  ```

  写入XML：`QXmlStreamWriter`

  ```cpp
  // 默认的文本编码是 UTF-8
  QXmlStreamWriter stream(&output);
  // 开启自动格式化：自动缩进之类的。默认是关闭的。建议开启。
  stream.setAutoFormatting(true);
  stream.writeStartDocument();
  // ...
  stream.writeStartElement(QLatin1String("bookmark"));
  stream.writeAttribute(QLatin1String("href"), QLatin1String("http://qt-project.org/"));
  stream.writeTextElement(QLatin1String("title"), QLatin1String("Qt Project"));
  stream.writeEndElement(); // bookmark
  // ...
  stream.writeEndDocument();
  ```

  注意：`QXmlStreamReader`和`QXmlStreamWriter`的性能优化的很好，Qt官方也推荐在操作XML文件时使用这两个类，但使用它们有一个不能忽略的前提条件，那就是被操作的XML文件一定要有良好的格式，例如有正确的缩进以及完好的标签（tag）等，文档本身一定不能是损坏的。
- 读写JSON文件

  读取：

  ```cpp
  QFile file(QLatin1String("D:/test.json"));
  file.open(QFile::ReadOnly | QFile::Text);
  QJsonDocument jsonDocument(QJsonDocument::fromJson(file.readAll()));
  file.close();
  QJsonObject mainJsonObject = jsonDocument.object();
  if (mainJsonObject.contains(QLatin1String("shouldDelete")) && mainJsonObject[QLatin1String("shouldDelete")].isBool()) {
    const bool shouldDelete = mainJsonObject[QLatin1String("shouldDelete")].toBool();
  }
  if (mainJsonObject.contains(QLatin1String("authors")) && mainJsonObject[QLatin1String("authors")].isArray()) {
    QJsonArray jsonArray = mainJsonObject[QLatin1String("authors")].toArray();
    // ...
  }
  ```

  写入：

  ```cpp
  QJsonObject mainJsonObject;
  mainJsonObject[QLatin1String("path")] = QLatin1String("D:/sssss");
  mainJsonObject[QLatin1String("length")] = 256;
  mainJsonObject[QLatin1String("shouldDelete")] = false;
  QJsonArray jsonArray;
  for (int i = 0; i != 20; ++i) {
    QJsonObject jsonObject;
    jsonObject[QLatin1String("author")] = QLatin1String("wangwenx190");
    jsonObject[QLatin1String("index")] = i;
  }
  mainJsonObject[QLatin1String("authors")] = jsonArray;
  QFile file(QLatin1String("D:/test.json"));
  file.open(QFile::WriteOnly | QFile::Text | QFile::Truncate);
  QJsonDocument jsonDocument(mainJsonObject);
  // 默认编码为 UTF-8
  file.write(jsonDocument.toJson());
  file.close();
  ```

- 读写INI文件

  ```cpp
  // 读写一个已有的ini文件。Windows 和 Unix 平台上通用。
  QSettings settings("/home/petra/misc/myapp.ini", QSettings::IniFormat);
  // 在macOS和iOS平台上，plist文件相当于ini文件，但属于原生格式，因此要用QSettings::NativeFormat。
  QSettings settings("/Users/petra/misc/myapp.plist", QSettings::NativeFormat);
  // 让Qt自动创建一个ini文件
  QSettings settings(QSettings::IniFormat, QSettings::UserScope, QCoreApplication::organizationName(), QCoreApplication::applicationName());
  // 写入：任何QVariant支持的类型都可以写入。
  settings.setValue("mainwindow/size", win->size());
  settings.setValue("mainwindow/fullScreen", win->isFullScreen());
  settings.setValue("outputpanel/visible", panel->isVisible());
  // 以下代码等价于上面的三行代码：“void QSettings::beginGroup(const QString &prefix);”与“void QSettings::endGroup();”的用法
  settings.beginGroup("mainwindow");
  settings.setValue("size", win->size());
  settings.setValue("fullScreen", win->isFullScreen());
  settings.endGroup();
  settings.beginGroup("outputpanel");
  settings.setValue("visible", panel->isVisible());
  settings.endGroup();
  // 注：层级的嵌套是允许的。
  // 读取：QVariant QSettings::value(const QString &key, const QVariant &defaultValue = QVariant()) const;
  int margin = settings.value("editor/wrapMargin", 80).toInt();
  // 写入数组
  struct Login {
    QString userName;
    QString password;
  };
  QList<Login> logins;
  QSettings settings;
  settings.beginWriteArray("logins");
  for (int i = 0; i < logins.size(); ++i) {
    settings.setArrayIndex(i);
    settings.setValue("userName", list.at(i).userName);
    settings.setValue("password", list.at(i).password);
  }
  settings.endArray();
  // 读取数组
  struct Login {
      QString userName;
      QString password;
  };
  QList<Login> logins;
  QSettings settings;
  int size = settings.beginReadArray("logins");
  for (int i = 0; i < size; ++i) {
    settings.setArrayIndex(i);
    Login login;
    login.userName = settings.value("userName").toString();
    login.password = settings.value("password").toString();
    logins.append(login);
  }
  settings.endArray();
  // 获取所有子键（遍历每一个子键）
  QStringList QSettings::allKeys() const;
  // 获取当前节点下的所有子键（只获取顶级子键，不会遍历子键的子键，也不会获取当前节点下的数组）
  QStringList QSettings::childKeys() const;
  // 获取当前节点下的所有数组（只获取顶级数组，不会遍历到底）
  QStringList QSettings::childGroups() const;
  // 删除某一个子键
  void QSettings::remove(const QString &key);
  // 删除QSettings对象的所有内容
  void QSettings::clear();
  ```

- 读写注册表：`QSettings settings("HKEY_CURRENT_USER\\Software\\Microsoft\\Office", QSettings::NativeFormat);`

  注：某些特殊的或重要的键值没有管理员权限无法修改，例如`HKEY_LOCAL_MACHINE`等，如果发现无法写入或修改注册表的某个键值，先看是不是权限的原因，不是的话再去看是不是程序本身有bug，实在找不到问题根源再去怀疑是不是Qt本身的bug。
- Windows：任务栏进度条，图标+任务栏小按钮+任务栏任务列表（最近打开，常用任务等）
- lupdate：将源码中能进行翻译的字符串制作为Qt Linguist专用的.ts翻译文件

  用法：`lupdate [options] [project-file]...`或`lupdate [options] [source-file|path|@lst-file]... -ts ts-files|@lst-file`

  常用参数：

  | 参数 | 描述 |
  | --- | ---- |
  | `-no-obsolete` | 去除所有废弃的和消失的翻译文本 |
  | `-no-ui-lines` | 不要记录待翻译文本在.ui文件里的行号 |
  | `-ts <ts-file>...` | 指定输出文件。允许有多个输出文件 |

  ```bash
  # 扫描QMake工程文件（.pro）。不推荐，因为当处理大型项目时速度特别慢。
  lupdate myproject.pro
  # 扫描单个源码文件
  lupdate main.qml -ts main_en.ts
  # 扫描rcc资源脚本文件（.qrc）
  lupdate application.qrc -ts myapp_en.ts
  # 扫描所有扩展名为.qml的文件
  lupdate -extensions qml -ts myapp_en.ts
  # 扫描多个文件
  lupdate qml.qrc filevalidator.cpp -ts myapp_en.ts
  # 生成多个翻译文件
  lupdate qml.qrc filevalidator.cpp -ts myapp_en.ts myapp_fr.ts
  ```

- lrelease：将.ts文件编译为.qm文件，起到压缩和加密的作用。

  用法：`lrelease [options] -project project-file`或`lrelease [options] ts-files [-qm qm-file]`

  常用参数：

  | 参数 | 描述 |
  | --- | --- |
  | `-idbased` | 使用ID而不是源码文本作为消息索引 |
  | `-compress` | 压缩.qm文件 |
  | `-nounfinished` | 去除未完成的翻译文本 |
  | `-removeidentical` | 去除与源码文本完全相同的翻译文本（即翻译了和没翻译一样的文本） |

  ```bash
  # 扫描QMake工程文件（.pro）。不推荐，因为当处理大型项目时速度特别慢。
  lrelease myproject.pro
  # 扫描单个翻译文件
  lrelease.exe main_en.ts
  # 扫描多个翻译文件
  lrelease.exe main_en.ts languages\main_fr.ts
  ```

- lconvert：将多个.qm文件合并为一个（重复的条目会自动合并）。

  用法：`lconvert [options] <infile> [<infile>...]`

  常用参数：

  | 参数 | 描述 |
  | --- | --- |
  | `-i <infile>`/`-input-file <infile>` | 指定输入文件（这个参数可以有多个，意为有多个输入文件） |
  | `-o <outfile>`/`-output-file <outfile>` | 指定输出文件 |
  | `-no-obsolete` | 去除废弃的翻译文本 |
  | `-no-untranslated` | 去除未翻译的文本 |
- rcc：Qt专用的资源编译器

  用法：`rcc [options] <inputs>`

  常用参数：

  | 参数 | 描述 |
  | --- | ---- |
  | `-o, --output <file>` | 指定输出文件 |
  | `--name <name>` | 使用`name`创建一个外部的初始化函数 |
  | `--root <path>` | 设置资源文件的根节点（添加到资源文件自己的根节点之前，如果有的话） |
  | `--compress-algo <algo>` | 设置压缩算法，可选值为`zstd`（推荐，三者中压缩率和解压速度最优）、`zlib`和`none` |
  | `--compress <level>` | 设置压缩级别。zstd：1\~19，zlib：1\~9。数字越大，压缩率越高 |
  | `--threshold <level>` | 设置压缩阈值（临界值），可取值为1~100，只有文件大小的减小量超过原文件大小的`level %`，rcc才会压缩它，否则rcc会直接存储而不进行压缩。默认值为70 |
  | `--binary` | 输出一个二进制文件以便动态加载（可以视为经过压缩和加密的外部资源文件） |
- 显示托盘图标+自定义托盘菜单（使用`QWidget`而不是`QMenu`）+弹出消息
- 多线程：3种方式
  - Qt Concurrent
  - `void QObject::moveToThread(QThread *targetThread);`
  - 重写`QThread`的`run`函数：`[virtual protected] void QThread::run();`
- Qt Creator添加自定义注释/协议模板：

  打开 Qt Creator，菜单选择：工具 -> 选项 -> 文本编辑器 -> 片段，点击“添加”按钮，添加新的“片段”。其中，“触发”为这个片段的触发条件，即当用户输入一个特定的字符串时提示是否插入这个片段的完整版。“触发种类”可以随便填写，这是为了方便用户记忆和区分而设置的。

  Qt Creator支持占位符自动替换：

  | 占位符 | 描述 |
  | ---- | ----- |
  | `%YEAR%` | 年 |
  | `%MONTH%` | 月 |
  | `%DAY%` | 日 |
  | `%DATE%` | 日期 |
  | `%USER%` | 用户名 |
  | `%FILENAME%` | 文件名 |
  | `%CLASS%` | 类名（如果可获得） |
  | `%$VARIABLE%` | 环境变量`VARIABLE`的内容 |
- 截图：
  - 截取程序自身的窗口：

    ```cpp
    QPixmap QWidget::grab(const QRect &rectangle = QRect(QPoint(0, 0), QSize(-1, -1)));
    ```

    `rectangle`有效时截取其指定范围内的图像，无效时截取整个窗口（默认）。
  - 截取其他程序的窗口或截屏：

    ```cpp
    // QPixmap QScreen::grabWindow(WId window, int x = 0, int y = 0, int width = -1, int height = -1);
    QGuiApplication::primaryScreen()->grabWindow(nullptr);
    ```

    `grabWindow`函数第一个参数传一个窗口句柄`window`就能对此屏幕上的指定窗口进行截图，传一个空指针则对整个屏幕进行截图。后四个参数则能设置截图范围，若范围无效则对整个窗口或屏幕进行截图（默认）。
- Windows平台使GUI程序显示控制台窗口：
  - QMake：`CONFIG += cmdline`
  - CMake：在`add_executable`时不要添加`WIN32`，即要`add_executable(${PROJECT_NAME} ${HEADERS} ${SOURCES} ${FORMS})`而不要`add_executable(${PROJECT_NAME} WIN32 ${HEADERS} ${SOURCES} ${FORMS})`

  具体的原理是不要让程序的子系统为`WINDOWS`，也不要让程序的入口点为`(w)WinMainCRTStartup`。
- 当想要注释大段的内容时，建议用 `#if 0` 和 `#endif` 将代码块包起来，而不是将该段代码选中然后全部 `//` ，想要取消注释时只要把`0`改成`1`即可，效率大大提升。
- 在使用`QFile`的过程中，不建议频繁的打开文件写入然后再关闭文件，比如间隔5ms输出日志，IO性能瓶颈很大，这种情况建议先打开文件不要关闭，等待合适的时机比如析构函数中或者日期变了需要重新变换日志文件的时候关闭文件。不然短时间内大量的打开关闭文件会很卡，文件越大越卡。
- 有时候在界面上加了弹簧，需要动态改变弹簧对应的拉伸策略，对应方法为`changeSize`，很多人会选择使用`set`开头去找，找不到的。
- Qt中继承`QWidget`之后，样式表不起作用：在构造函数中调用`setAttribute(Qt::WA_StyledBackground);`即可解决
- Qt默认不支持大资源文件，需要的话要手动开启：
  - QMake：`CONFIG += resources_big`
  - CMake：`qt5_add_big_resources(<VAR> file1.qrc [file2.qrc ...] [OPTIONS ...])`

  具体的原理是处理小文件时rcc会将其翻译为C++代码，然后与项目其他的源文件一起参与编译和链接过程，而处理大文件时rcc会直接生成.obj文件，不参与编译过程，只参与最后的链接过程。
- 如果需要窗口无边框，但是又需要保留操作系统的边框特性，例如可以自由拉伸边框和窗口阴影等，可以使用 `setWindowFlags(Qt::Window | Qt::CustomizeWindowHint);`。注意一定要用`setWindowFlags`而不是`setWindowFlag`，因为`CustomizeWindowHint`这个Flag会与其他Flag冲突，这些Flag并存时会导致`CustomizeWindowHint`失效，用前者正好可以顺便清除其他Flag。

  注：`setWindowFlag(s)`是`QWidget`独有的函数，`QWindow`请使用`setFlag(s)`。
- Qt Quick在Linux平台无法播放视频：`sudo apt install libpulse-dev`即可解决
- 判断一个类是否是`QWidget`或`QWindow`（或它们的派生类）：

  ```cpp
  // 与inherits("QWidget")等效，但速度比其快得多
  bool QObject::isWidgetType() const;
  // 与inherits("QWindow")等效，但速度比其快得多
  bool QObject::isWindowType() const;
  ```

- 获取Qt版本：
  - 编译时版本：`QT_VERSION_STR`宏（这个宏返回的是编译程序时所链接的Qt库的版本，这个值在编译完成后永远不会改变）
  - 运行时版本：`const char *qVersion();`函数（这个函数返回的是当前加载的Qt库的版本，它可能会在程序运行期间发生改变）
- 将最小化或被其他窗口挡住的窗口移到最前：
  - Windows：Win32 API

    ```cpp
    // 头文件：winuser.h (include Windows.h)
    // 库文件：User32.lib（User32.dll）
    // 下面这一段代码要在较早的地方执行，例如在程序的 main 函数里。
    // 这一段代码是为了确保 SetForegroundWindow 能执行成功而设置的，在前置窗口前执行过一次就足够了（如果需要的话），不是每次前置窗口都要执行的。
    DWORD dwTimeout = -1;
    SystemParametersInfo(SPI_GETFOREGROUNDLOCKTIMEOUT, 0, &dwTimeout, 0);
    if (dwTimeout >= 100) {
      // 下面这一行语句因为要读写INI文件，因此可能会导致执行此语句时卡顿两三秒
      SystemParametersInfo(SPI_SETFOREGROUNDLOCKTIMEOUT, 0, 0, SPIF_SENDCHANGE | SPIF_UPDATEINIFILE);
    }
    // 上面的代码结束，下面的代码与上面的没有关系了
    // 下面这一段代码每次前置窗口时都要执行一次，与上面那一段不一样。
    HWND hWnd = reinterpret_cast<HWND>(effectiveWinId()); // 获取程序当前活动窗口的句柄
    HWND hForeWnd = GetForegroundWindow(); // 获取当前前置窗口的句柄
    DWORD dwForeID = GetWindowThreadProcessId(hForeWnd, nullptr); // 获取当前前置窗口的进程ID
    DWORD dwCurID = GetCurrentThreadId(); // 获取程序自身的进程ID
    // 如果窗口被隐藏了，先显示出来
    if (IsWindowVisible(hWnd) != TRUE) {
      // 此处一定要用 SW_SHOW，用其他的值会意外改变窗口的状态
      ShowWindow(hWnd, SW_SHOW);
    }
    WINDOWPLACEMENT wndpmt;
    if (GetWindowPlacement(hWnd, &wndpmt) != FALSE) {
      // 如果窗口被最小化了，还原回去
      if (wndpmt.showCmd == SW_SHOWMINIMIZED) {
        // 此处一定要用 SW_RESTORE，用 SW_SHOW 不能恢复被最小化的窗口，用 SW_SHOWNORMAL 会导致窗口被无条件的还原为原始大小，即使在最小化之前处于最大化的状态
        ShowWindow(hWnd, SW_RESTORE);
      }
    }
    AttachThreadInput(dwCurID, dwForeID, TRUE); // 连接两个进程的输入焦点
    // 经过我的测试，以下两行不是必需的，没有这两行也能达到预期效果，只不过加上会更加保险
    // SetWindowPos(hWnd, HWND_TOPMOST, 0, 0, 0, 0, SWP_NOSIZE | SWP_NOMOVE); // 修改窗口Z序，将窗口置顶（暂时）
    // SetWindowPos(hWnd, HWND_NOTOPMOST, 0, 0, 0, 0, SWP_NOSIZE | SWP_NOMOVE); // 取消置顶（为了防止影响到别的窗口的Z序；虽然已经取消了置顶，但已经跑到前面的窗口是不会再回到底下去了）
    SetForegroundWindow(hWnd); // 设置为前置的窗口
    AttachThreadInput(dwCurID, dwForeID, FALSE); // 断开两个进程的输入焦点
    ```

  - 其他平台：Qt提供的方法

    ```cpp
    // 如果窗口被隐藏了，先显示出来。
    // QWindow 没有 isHidden 函数，请使用 isVisible 函数代替。
    if (isHidden()) {
      show();
    }
    setWindowStates(windowStates() & ~Qt::WindowMinimized);
    // QWindow 没有 isActiveWindow 函数，请使用 isActive 函数代替。
    if (!isActiveWindow()) {
      raise();
      // QWindow 没有 activateWindow 函数，请使用 requestActivate 函数代替。
      activateWindow();
    }
    ```

  - 另一种思路：先将窗口置顶，这样一来窗口肯定会跑到最前端，然后再取消置顶，恢复默认的设置。置顶和取消置顶这两个操作要有一定的时间间隔，如果间隔太短，很可能会没有效果（因为来不及置顶，置顶稍微需要点时间）。

    按照我以前的经验，置顶和取消置顶都会导致窗口被隐藏，要手动重新将之显示出来（可能是Qt的bug？），所以用这个方法会导致窗口明显闪烁一到两次，体验不太好。如果没有这个问题，那么这个思路是最简单有效的方法。
- 窗口如何置顶？置顶后如何取消置顶？

  ```cpp
  // 置顶
  setWindowFlag(Qt::WindowStaysOnTopHint);
  // 取消置顶
  setWindowFlag(Qt::WindowStaysOnTopHint, false);
  // QWindow 没有 setWindowFlag 函数，请使用 setFlag 函数代替。
  ```

- 窗口置顶或置底/执行`setWindowState(s)`后窗口消失不见：可能是Qt的bug。

  解决方法：窗口只是单纯的不可见了，重新将窗口显示出来即可。

  ```cpp
  // QWindow 没有 isHidden 函数，请使用 isVisible 函数代替
  if (isHidden()) {
    show();
  }
  ```

- 窗口最小化后恢复原始大小，窗口假死：

  在窗口显示前激活窗口的`Qt::WA_Mapped`属性。

  ```cpp
  void QWidget::showEvent(QShowEvent *event) {
    setAttribute(Qt::WA_Mapped);
    QWidget::showEvent(event);
  }
  ```

- 连接信号和槽函数时，尽量使用函数指针

  ```cpp
  connect(ui->pushButton_close, &QPushButton::clicked, this, QWidget::close);
  // 信号或槽函数的参数需要重载时，请参考上面提到的 qOverload 函数。
  ```

  使用这种连接方式，有三个很明显的优点：
  1. 如果信号或槽函数的函数名打错了或函数签名不匹配，根本就不能通过编译，因此能在编译期就发现问题并加以解决。
  2. 使用的是函数指针，省却了根据字符串查找信号和槽函数的过程，运行速度有所提高。
  3. 使用的是函数指针而不是字符串，节约了部分内存。而且由于不再构建`QString`对象，性能也有所提高。
- 很多控件都带有`viewport`，比如`QTextEdit`/`QTableWidget`/`QScrollArea`，有时候对这些控件直接进行修改的时候发现不起作用，其实是需要对其`viewport`进行设置，比如设置滚动条区域背景透明，需要使用`scrollArea->viewport()->setStyleSheet("background-color:transparent;");`而不是`scrollArea->setStyleSheet("QScrollArea{background-color:transparent;}");`
- 启用了鼠标跟踪的时候（`setMouseTracking(true)`），如果该窗体上面还有其他控件，当鼠标移到其他控件上面的时候，父类的鼠标移动事件就会识别不到了，此时需要用到`HoverMove`事件，需要先设置`setAttribute(Qt::WA_Hover);`
- Qt封装的日期时间类`QDateTime`非常强大，可以在字符串和日期时间之间相互转换，也可以在（毫）秒数和日期时间之间相互转换，还可以在自1970-01-01T00:00:00.000经过的（毫）秒数和日期时间之间相互转换等。

  ```cpp
  QDateTime dateTime;
  // 获取当前日期和时间，并转换为字符串
  QString str1 = dateTime.currentDateTime().toString("yyyy-MM-dd hh:mm:ss");
  // 从字符串转换为毫秒（需完整的年月日时分秒）
  qint64 msecs = dateTime.fromString("2011-09-10 12:07:50:541", "yyyy-MM-dd hh:mm:ss:zzz").toMSecsSinceEpoch();
  // 从字符串转换为秒（需完整的年月日时分秒）
  qint64 secs = dateTime.fromString("2011-09-10 12:07:50:541", "yyyy-MM-dd hh:mm:ss:zzz").toSecsSinceEpoch();
  // 从毫秒转换到年月日时分秒（字符串）
  QString str2 = dateTime.fromMSecsSinceEpoch(1315193829218).toString("yyyy-MM-dd hh:mm:ss:zzz");
  // 从秒（若有zzz，则为000）转换到年月日时分秒（字符串）
  QString str3 = dateTime.fromSecsSinceEpoch(1315193829).toString("yyyy-MM-dd hh:mm:ss[:zzz]");
  ```

- 在使用`QList`、`QVector`以及`QByteArray`等链表或者数组的过程中，如果只需要取值不需要赋值，建议使用`const T &at(int i) const`而不是`[]`操作符，因为前者的速度远超后者（常数时间复杂度）。
- 在使用`QLineEdit`的时候，如果想要实现将输入的格式和内容限定为*IP地址*、*MAC地址*以及*序列号*等特殊需求，可以将`void QLineEdit::setInputMask(const QString &inputMask);`与`void QLineEdit::setValidator(const QValidator *v);`搭配使用，前者用来限定输入的格式，后者用来限定输入的内容，非常简单方便和高效。
- 尽量使用`QString QFileInfo::canonicalFilePath() const;`这个函数而不是`QString QFileInfo::absoluteFilePath() const;`或者`QString QFileInfo::filePath() const;`，因为`canonicalFilePath`这个函数会尽可能的解析路径，不会包含`.`、`..`或任何快捷方式/软链接，而且返回的是完整的绝对路径，而后两个函数不会解析的如此彻底。
- Qt界面文字乱码：
  - 不要直接在源码（C++和QML）中使用非英文半角字符（注释除外），非英文半角字符一定要用`tr`（QML：`qsTr`）函数包裹起来。如果实在不需要翻译，则用`QStringLiteral`包裹。
  - 编译器开启`UTF-8`支持。
- 修改程序字体

  ```cpp
  // const QFont &QWidget::font() const
  // [static] QFont QApplication::font()
  // [static] QFont QGuiApplication::font()
  QFont font = QGuiApplication::font(); // 获取程序的默认字体
  font.setFamily(QLatin1String("Times New Roman")); // 设置字体族
  font.setBold(true); // 加粗
  font.setItalic(true); // 斜体
  font.setPointSize(16); // 设置字号。setPointSize能自适应DPI，不要用setPixelSize，后者不能
  font.setUnderline(true); // 下划线
  // void QWidget::setFont(const QFont &)
  // [static] void QApplication::setFont(const QFont &font, const char *className = nullptr)
  // [static] void QGuiApplication::setFont(const QFont &font)
  QGuiApplication::setFont(font); // 应用修改后的字体
  ```

- 根据Qt的事件过滤器获取各种事件并进行相应的处理

  重载`[virtual] bool QObject::eventFilter(QObject *watched, QEvent *event)`函数

  ```cpp
  bool MainWindow::eventFilter(QObject *object, QEvent *event) {
    Q_UNUSED(object)
    switch (event->type()) {
    case QEvent::ApplicationFontChange:
      // 程序默认字体发生改变
      break;
    case QEvent::ApplicationStateChange:
      // 程序的状态发生改变：激活、未激活
      break;
    case QEvent::ApplicationWindowIconChange:
      // 程序的图标发生改变
      break;
    case QEvent::Clipboard:
      // 剪贴板的内容发生改变
      break;
    case QEvent::Close:
      // Widget 被关闭（QCloseEvent）
      break;
    case QEvent::CursorChange:
      // Widget 的鼠标指针发生改变
      break;
    case QEvent::DragEnter:
      // 在拖放过程中，鼠标进入 Widget（QDragEnterEvent）
      break;
    case QEvent::DragLeave:
      // 在拖放过程中，鼠标离开 Widget（QDragLeaveEvent）
      break;
    case QEvent::DragMove:
      // 正在拖放过程中（QDragMoveEvent）
      break;
    case QEvent::Drop:
      // 拖放过程已经完成（QDropEvent）
      break;
    case QEvent::Enter:
      // 鼠标进入 Widget 的边界（QEnterEvent）
      break;
    case QEvent::FileOpen:
      // 请求打开文件（QFileOpenEvent）
      break;
    case QEvent::FocusIn:
      // Widget 或 Window 获得键盘焦点（QFocusEvent）
      break;
    case QEvent::FocusOut:
      // Widget 或 Window 失去键盘焦点（QFocusEvent）
      break;
    case QEvent::FontChange:
      // Widget 的字体发生改变
      break;
    case QEvent::Hide:
      // Widget 被隐藏（QHideEvent）
      break;
    case QEvent::HoverEnter:
      // 鼠标进入了一个 hover widget（QHoverEvent）
      break;
    case QEvent::HoverLeave:
      // 鼠标离开了一个 hover widget（QHoverEvent）
      break;
    case QEvent::HoverMove:
      // 鼠标在一个 hover widget 内移动（QHoverEvent）
      break;
    case QEvent::KeyPress:
      // 按键按下（QKeyEvent）
      break;
    case QEvent::KeyRelease:
      // 按键释放（QKeyEvent）
      break;
    case QEvent::LanguageChange:
      // 程序的翻译发生改变
      break;
    case QEvent::Leave:
      // 鼠标离开 Widget 的边界
      break;
    case QEvent::LocaleChange:
      // 系统区域发生改变
      break;
    case QEvent::MouseButtonDblClick:
      // 鼠标双击【要注意判断是左键还是右键】（QMouseEvent）
      break;
    case QEvent::MouseButtonPress:
      // 鼠标按下【要注意判断是左键还是右键】（QMouseEvent）
      break;
    case QEvent::MouseButtonRelease:
      // 鼠标释放（QMouseEvent）
      break;
    case QEvent::MouseMove:
      // 鼠标移动（QMouseEvent）
      break;
    case QEvent::Move:
      // Widget 的位置发生改变（QMoveEvent）
      break;
    case QEvent::Resize:
      // Widget 的大小发生改变（QResizeEvent）
      break;
    case QEvent::Show:
      // Widget 被显示（QShowEvent）
      break;
    case QEvent::Timer:
      // 常规计时器事件（QTimerEvent）
      break;
    case QEvent::Wheel:
      // 鼠标滚轮发生滚动（QWheelEvent）
      break;
    case QEvent::WindowIconChange:
      // 窗口图标发生改变
      break;
    case QEvent::WindowStateChange:
      // 窗口状态发生改变【最大化、最小化、全屏、还原默认】（QWindowStateChangeEvent）
      break;
    case QEvent::WindowTitleChange:
      // 窗口标题发生改变
      break;
    default:
      // 还有很多其他的事件类型，请自行查阅
      break;
    }
    return QMainWindow::eventFilter(object, event);
  }
  ```

- 适合Qt项目的*Crash Handler*框架
