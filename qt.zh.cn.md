# Qt 使用笔记

## Qt 6

### 目标平台变更

Qt6 不再支持**32位**Windows系统，不再支持**Windows 7，Windows 8**和**Windows 8.1**，仅支持**64位**Windows 10 **1809**及后续版本。目前已经移除了**WinRT/UWP**支持。UNIX平台的变化请自行查看对应的JIRA ticket。

如果您的程序需要继续支持Windows 7/WinRT/UWP，请使用Qt 5.15；如果需要继续支持Windows 8.1，请使用Qt 5.12；如果需要32位版本，请继续使用Qt5或者自行编译Qt6。

参考：

- <https://bugreports.qt.io/browse/QTBUG-85851>
- <https://bugreports.qt.io/browse/QTBUG-85855>

### Qt 5 迁移指南

- 从QMake迁移到CMake：<https://wiki.qt.io/CMake_Port/Porting_Guide>

## Qt 5

- Windows 平台尽量使用[`JOM`](http://download.qt.io/official_releases/jom/)编译，速度快很多，远远不是`NMake`或者`mingw32-make`能比得上的。

  注：JOM仅支持原生Windows环境，任何Unix或类Unix环境（例如MinGW）均不支持。
- Qt官方下载地址：
  - Qt 官方下载站：<http://download.qt.io/>
  - Qt 国内镜像站（有好多个，这只是其中两个）：<https://mirrors.tuna.tsinghua.edu.cn/qt/>、<http://mirrors.ustc.edu.cn/qtproject/>
- 在 Windows 平台上编译 Qt 时，编译`ANGLE`时需要一个叫`WindowsSdkVerBinPath`的环境变量，其路径指向`fxc.exe`(Microsoft Direct3D Shader Compiler)所在的文件夹，常见路径为`C:\Program Files (x86)\Windows Kits\10\bin\10.0.17763.0`

  注：`10.0.17763.0`为你所安装的Win SDK的版本号。
- 在 Windows 平台上，如果要编译`ANGLE`，需要安装[`DirectX SDK`](http://www.microsoft.com/en-us/download/details.aspx?id=6812)，新版 DX SDK 已经与[`Windows SDK`](https://developer.microsoft.com/en-us/windows/downloads/sdk-archive)合并了。同时还需要[`Win flex-bison`](https://sourceforge.net/projects/winflexbison/)
- 最新版`Mesa llvmpipe`（`opengl32sw.dll`）下载：<https://github.com/pal1000/mesa-dist-win/releases>
- 在 Linux 平台进行 Qt 开发需要安装额外的库：<https://doc.qt.io/qt-5/linux.html> 。其中，在 Ubuntu 平台上进行开发的官方 Wiki：<https://wiki.qt.io/Install_Qt_5_on_Ubuntu>
- 在 Linux 平台编译 Qt 需要安装额外的库：<https://doc.qt.io/qt-5/linux-requirements.html>
- `QWebEngine`模块不支持静态编译（但静态链接运行时是可以的）
- 在 Windows 平台上，`QWebEngine`模块只能使用最新版的`Visual Studio 2017/2019`或`Clang-CL`（暂不清楚`Intel-CL`是否支持）编译，不支持其他一切编译器（截至2020-02-03）
- 不要将 Qt 项目放在有非英文字符的路径下，否则会无法编译
- 添加删除源文件或者源文件改名后，要重新执行`qmake`然后重新构建，否则会有链接错误
- 编译器选项与 Qt 的`CONFIG`如何对应：

  | 编译器选项 | MSVC参数 | Clang参数 | GCC参数 | ICC参数 | qmake |
  | --------- | -------- | -------- | ------- | ------- | ----- |
  | 为速度优化 | `/O2`    | `-O3`（clang-cl：`/clang:-O3`） | `-O3` | `-O3`（icl：`/O3`） | `CONFIG += optimize_full` |
  | 为大小优化 | `/O1`    | `-Oz`（clang-cl：`/clang:-Oz`） | `-Os` | `-Os`（icl：与MSVC相同） | `CONFIG += optimize_size` |
  | 启用链接时间代码生成 | `/GL`（编译器），`/LTCG`（连接器） | `-flto=thin`（clang-cl：`-flto=thin`；只有lld-link不需要额外的参数，其他任何连接器都要传这个参数） | `-flto -fno-fat-objects`（连接器还要多一个`-fuse-linker-plugin`参数） | `-ipo -fno-fat-objects`（icl：`/Qipo`） | `CONFIG += ltcg` |
  | 静态连接运行时 | `/MT`/`/MTd` | `-static`（clang-cl：与MSVC相同） | `-static` | `-static`（icl：与MSVC相同） | `CONFIG += static_runtime` |
  | 启用Quick编译器 | - | - | - | - | `CONFIG += qtquickcompiler` |
  | 启用异常处理 | `/EHsc` | `-fexceptions`（clang-cl：与MSVC相同） | `-fexceptions` | `-fexceptions`（icl：与MSVC相同） | `CONFIG += exceptions` |
  | 禁用异常处理 | `/EHs-c-` | `-fno-exceptions`（clang-cl：与MSVC相同） | `-fno-exceptions` | `-fno-exceptions`（icl：与MSVC相同） | `CONFIG += exceptions_off` |
  | 启用RTTI | `/GR` | `-frtti`（clang-cl：与MSVC相同） | `-frtti` | `-frtti`（icl：与MSVC相同） | `CONFIG += rtti` |
  | 禁用RTTI | `/GR-` | `-fno-rtti`（clang-cl：与MSVC相同） | `-fno-rtti` | `-fno-rtti`（icl：与MSVC相同） | `CONFIG += rtti_off` |
  | 开启所有警告 | `/Wall` | `-Weverything` | `-Wall -Wextra` | - | `CONFIG += warn_on` |
  | 将警告视为错误 | `/WX` | `-Werror` | `-Werror` | - | - |
  | 关闭所有警告 | `/w` | `-w` | `-w` | - | `CONFIG += warn_off` |
  | 关闭C语言编译器扩展 | `/Za` | - | - | - | `CONFIG += strict_c` |
  | 关闭C++语言编译器扩展 | `/permissive-` | - | - | - | `CONFIG += strict_c++` |
  | 开启UTF-8支持 | `/utf-8` | `-finput-charset=UTF-8 -fexec-charset=UTF-8`（clang-cl：与MSVC相同） | `-finput-charset=UTF-8 -fexec-charset=UTF-8` | `-option,cpp,--unicode_source_kind,UTF-8`（icl：`/Qoption,cpp,--unicode_source_kind,UTF-8`） | `CONFIG += utf8_source` |
  | 设置C语言标准 | `/std:c11` | `-std=c11`（clang-cl：与MSVC相同） | `-std=c11` | `-std=c11`（icl：`/Qstd=c11`） | `CONFIG += c11` |
  | 设置C++语言标准 | `/std:c++17` | `-std=c++1z`（clang-cl：与MSVC相同） | `-std=c++1z` | `-std=c++17`（icl：`/Qstd=c++17`） | `CONFIG += c++1z`/`CONFIG += c++17` |
  | 隐藏符号 | - | `-fvisibility=hidden -fvisibility-inlines-hidden` | `-fvisibility=hidden -fvisibility-inlines-hidden` | `-fvisibility=hidden -fvisibility-inlines-hidden` | `CONFIG += hide_symbols` |
- `opengl32sw.dll`这个文件是用软件模拟的显卡，针对的是没有显卡的机器，所以只有在极少数情况下才会需要，发布 Qt 程序时不必带上此文件，能极大减小发布大小
- 发布 Windows 平台的 Qt 程序时可以使用 Qt 官方提供的`windeployqt`程序，这个小程序会自动检测并复制相关的 dll 到你的程序文件夹，非常方便。但它无法检测第三方库，必须自行查找并复制。而且这个工具会复制一些多余的 Qt 的 dll，但极难判断究竟哪些是真的无用，因此就不要管了。

  - 常用参数：

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

  - 如何使QMake自动调用`windeployqt`工具

    在.pro文件里写入：

    ```text
    CONFIG += windeployqt
    ```

    然后使用命令行编译时：

    ```text
    jom/nmake/mingw32-make windeployqt
    ```

- QMake用命令行编译 Qt 工程：

  ```bash
  qmake "example.pro" -spec win32-clang-g++ "CONFIG+=release"
  jom qmake_all
  jom
  jom install
  ```

  其中，`-spec`指定了`mkspec`，缺少的话会使用平台默认参数，`CONFIG`指定了额外的配置选项，缺少的话也是使用平台默认参数。

  这个命令行是全平台通用的，只不过最后的`make`过程要根据你的平台修改，例如MinGW使用`mingw32-make`，Unix使用`make`。
- Windows平台可以使用[`Dependencies`](https://github.com/lucasg/Dependencies)这个工具来检测你开发的程序具体依赖哪些 dll，非常好用
- 如果你开发的程序不是很大，不推荐使用 Qt 提供的[`Installer Framework`(`IFW`)](http://download.qt.io/official_releases/qt-installer-framework/)打包，因为这个`IFW`是 Qt 静态编译的，因此文件会比较大，哪怕什么文件都不打包，也会有**20MB**左右的大小，得不偿失。当然，如果你的程序很大，几百兆甚至更大，就可以用它了。小软件推荐：[NSIS](https://sourceforge.net/projects/nsis/)，[Inno Setup](http://jrsoftware.org/isinfo.php)，[WiX Toolset](http://wixtoolset.org/)（制作**MSI**安装包专用）。不推荐：Install Shield（授权费非常昂贵，软件非常臃肿，打包出的安装程序较大，界面不容易定制。不信自己装一个试试），Install Anywhere（与前者是同一个公司的，因此缺点也差不多），Advanced Installer（制作MSI安装包专用，授权费较高），Vice Installer（远古软件），Wise Installation System（远古软件），Smart Install Maker（基本就是个玩具）以及其他任何不知名小软件
- 尽量不要链接[`ICU`(`International Components for Unicode`)](https://github.com/unicode-org/icu)，虽然它提供了对世界上大多数国家和地区的语言和文字支持，功能非常强大，但文件太大，裁剪前**20~30MB**左右，裁剪后也有**10MB**左右，实际上一般程序根本用不到这种变态级别的国际化支持，所以不推荐使用这个库。Qt 官方也早已去掉了对它的依赖。在配置时给一个`-no-icu`参数就可以禁用`ICU`支持。
- Qt自从5.13版本开始添加了对`Schannel`的支持，可以在配置时通过`-schannel`来启用，以此去掉对[`OpenSSL`](https://github.com/openssl/openssl)的依赖（如果完全不需要`OpenSSL`的支持，需要在配置时给一个`-no-openssl`参数来禁用`OpenSSL`）。

  注意：*Schannel*与*OpenSSL*这两个特性是互斥的，这两个中最多只能启用一个。
- QMake区分调试版本和发布版本：`CONFIG(debug, debug|release)`（debug时返回true），`CONFIG(release, debug|release)`（release时返回true），`contains(CONFIG, debug)`（debug时返回true），`contains(CONFIG, release)`（release时返回true），或者更简单的`debug:`和`release:`。在代码中，可以通过`#ifdef _DEBUG`来判断，但请注意，发布版本并没有`_RELEASE`这样的宏（某些构建系统和编译器可能会自动给发布版本添加`NDEBUG`的宏定义，但不具有通用性）。
- QMake区分 Qt 是静态链接还是动态链接的：`CONFIG(static, static|shared)`，`CONFIG(shared, static|shared)`，`contains(CONFIG, static)`，`contains(CONFIG, shared)`，`static:`，`shared:`。在代码中，可以通过`#ifdef QT_STATIC`和`#ifdef QT_SHARED`来判断。
- QMake区分32位还是64位：`contains(QMAKE_TARGET.arch, x86_64)`，`contains(QMAKE_HOST.arch, x86_64)`，`contains(QT_ARCH, x86_64)`，以上三条语句在编译64位程序时返回true。其中`x86_64`替换为其他架构，例如`i386`，也是可行的，只不过判断的就不一定是64位了。在代码中，可以通过`Q_PROCESSOR_X86_64`和`Q_PROCESSOR_X86_32`等Qt提供的宏来判断。Windows 平台上还可以通过`#ifdef WIN64`或`#ifdef _WIN64`来判断是不是64位，不要用`WIN32`来判断，因为`WIN32`这个会在两个架构上都有定义。
- QMake区分应用程序和库：`contains(TEMPLATE, app)`，`contains(TEMPLATE, lib)`
- Windows 平台上添加图标及属性信息：
  - QMake：

     ```text
     # 设置版本。只有这个变量是全平台通用的。其中，只有 Windows 平台上的版本号是4位的，其他平台都是3位
     VERSION = 1.2.3
     # 设置图标。必须为.ico格式，且最大 256x256
     RC_ICONS = "../res/icon.ico"
     # 设置代码页（此处以Unicode为例）
     RC_CODEPAGE = 1200
     # 设置语言（此处以简体中文为例）
     RC_LANG = 0x0804
     # 更多代码页以及语言的值请参考：https://docs.microsoft.com/en-us/windows/win32/menurc/versioninfo-resource
     # 设置产品名称
     QMAKE_TARGET_PRODUCT = "My app name"
     # 设置文件说明
     QMAKE_TARGET_DESCRIPTION = "My app description"
     # 设置版权
     QMAKE_TARGET_COPYRIGHT = "My copyright info"
     # 设置公司
     QMAKE_TARGET_COMPANY = "My company name"
     # 至于备注、原始文件名和商标等条目的设置是不支持的，QMake仅支持以上设置，其他剩余条目的设置只能通过rc文件实现
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
- 在 Windows 平台上使用`MinGW`编译 Qt 时不能开启 LTO，否则会报错，不清楚具体的原因
- Windows 平台嵌入清单文件（*.manifest）
  1. 禁止Qt自动嵌入默认的清单文件并添加我们自己的资源文件

     ```text
     # myapp.pro
     CONFIG -= embed_manifest_exe
     RC_FILE = myapp.rc # 清单文件只能通过这种方法间接嵌入
     ```

  2. 在资源脚本中嵌入清单文件

     ```text
     // myapp.rc
     // ...
     // 放在什么位置都行，为了省事可以直接附加到资源脚本的末尾
     // #define RT_MANIFEST 24
     // #define CREATEPROCESS_MANIFEST_RESOURCE_ID 1
     // 如果资源编译器无法找到“CREATEPROCESS_MANIFEST_RESOURCE_ID”和“RT_MANIFEST”
     // 的定义，就把上面两行取消注释，一起放入rc文件中，或者直接替换成对应的数值。
     CREATEPROCESS_MANIFEST_RESOURCE_ID RT_MANIFEST "myapp.manifest"
     // ...
     ```

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
   // 此函数不能创建指向网址的快捷方式（.url文件），那种快捷方式只能用Win32 API自行创建
   ```

- 包含`QScopedPointer`/`std::unique_ptr`的类不能使其析构函数内联，例如`~MyClass() override = default;`或`~MyClass() override { ...; }`之类的，否则会无法编译。
- 修改鼠标指针：

  ```text
  [static] void QGuiApplication::setOverrideCursor(const QCursor &cursor);
  [static] void QGuiApplication::restoreOverrideCursor();
  void QWidget::setCursor(const QCursor &);
  void QWidget::unsetCursor();
  void QWindow::setCursor(const QCursor &);
  void QWindow::unsetCursor();
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
- Qt 5在使用`qInstallMessageHandler`设置回调函数时，不能通过常规方法设置为类的成员函数，一般都是设置为全局函数，如果非要设置为一个类的成员函数，请参考以下示例：

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

- 使用`qInstallMessageHandler`设置了自己的回调函数后，`qSetMessagePattern`函数失效怎么办？在你的回调函数中使用`qFormatLogMessage`函数即可，这个函数可以获取格式化后的日志消息。
- QMake如何使Qt编译生成的带版本信息的动态库，文件名末尾不带主版本号：

  ```text
  CONFIG += skip_target_version_ext
  ```

- QMake如何使QML插件支持静态加载（只是简单的能静态编译是不行的）：

  ```text
  # Qt's QML plugins should be relocatable
  # 下面这个设置不管静态还是动态都是需要的
  CONFIG += relative_qt_rpath
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

- Windows平台如何获取是否开启深色模式/浅色模式：详见[win32.md：获取系统是否开启深色模式/浅色模式](/win32.zh.cn.md)
- Windows平台如何将窗口设置为深色模式：详见[win32.md：将窗口设置为深色模式](/win32.zh.cn.md)
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

  或设置环境变量`QT_ENABLE_HIGHDPI_SCALING`（布尔类型，Qt 5.14 引入）。

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

  注意事项
  - `QStyle::StandardPixmap`这个枚举里包含很多很常用的标准图标，比如最小化按钮，最大化按钮以及关闭按钮等，尽量多利用，不要再费心费力去第三方网站上找或者自己绘制相关的图片素材了。
  - 使用这个函数时一定要记得把一个`QStyleOption`的指针作为第二个参数传给它，有了这个参数后`QStyle`内部会根据系统的DPI自动进行尺寸换算，没有这个参数的话返回的图标是没有针对DPI进行调整的。

    ```cpp
    QStyleOption option;
    option.initFrom(this);
    const QIcon icon = style()->standardIcon(QStyle::StandardPixmap::SP_TitleBarMinButton, &option);
    ```

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
- `QMediaPlayer`依赖本地解码器，默认状态下仅支持非常有限的格式，Windows平台上下载[*K-Lite Codec Pack*](http://www.codecguide.com/download_k-lite_codec_pack_mega.htm)或者[*LAV Filters*](https://github.com/Nevcairiel/LAVFilters/releases/latest)安装即可解决。但`QMediaPlayer`官方没有提供开启硬件解码的接口，默认全部软件解码，对CPU和内存的占用较高，除非继承`QAbstractVideoFilter`这个类自己写一个解码插件，自己实现硬件解码（具体方法请查看Qt手册。这个对多媒体文件的编解码知识要求较高，我暂时还干不了这个）。
- 如何使`QPushButton`的文字左对齐：

  设置样式表`QPushButton{text-align:left;}`。
- 使用SQLite数据库时如果不想产生数据库文件，可以创建内存数据库：

  ```cpp
  QSqlDatabase sqlDatabase = QSqlDatabase::addDatabase(QLatin1String("QSQLITE"));
  sqlDatabase.setDatabaseName(QLatin1String(":memory:"));
  ```

- Qt5提供了`QScroller`这个类直接将控件滚动：

  ```cpp
  //禁用横向滚动条
  ui->listWidget->setHorizontalScrollBarPolicy(Qt::ScrollBarAlwaysOff);
  //禁用纵向滚动条
  ui->listWidget->setVerticalScrollBarPolicy(Qt::ScrollBarAlwaysOff);
  //设置横向按照像素值为单位滚动
  ui->listWidget->setHorizontalScrollMode(QListWidget::ScrollPerPixel);
  //设置纵向按照像素值为单位滚动
  ui->listWidget->setVerticalScrollMode(QListWidget::ScrollPerPixel);
  //设置滚动对象以及滚动方式为鼠标左键拉动滚动
  QScroller::grabGesture(ui->listWidget, QScroller::LeftMouseButtonGesture);
  //还有个QScrollerProperties可以设置滚动的一些参数
  ```

- 如何解决`Fault tolerant heap shim applied to current process. This is usually due to previous crashes.`这样的错误：

  打开注册表，找到`HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers`，选中`Layers`键值，从右侧列表中删除自己的那个程序路径即可。
- 获取系统窗口标题栏高度：

  ```text
  // 把 PM_TitleBarHeight 作为 pixelMetric 函数的第一个参数即可获取标题栏的高度
  [pure virtual] int QStyle::pixelMetric(QStyle::PixelMetric metric, const QStyleOption *option = nullptr, const QWidget *widget = nullptr) const
  ```

  注意事项
  - `PM_TitleBarHeight`是`QStyle::PixelMetric`这个枚举里的一个，这个枚举里还包含很多系统标准控件的尺寸，都可以通过这个函数获取。
  - 使用`pixelMetric`函数时一定要记得把一个`QStyleOption`的指针作为第二个参数传给它，有了这个参数后`pixelMetric`内部会根据系统的DPI自动进行换算，没有这个参数的话返回的是一个无视DPI的固定值。

    ```cpp
    QStyleOption option;
    option.initFrom(this);
    const int val = style()->pixelMetric(QStyle::PixelMetric::PM_TitleBarHeight, &option);
    ```

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
  bool QFileInfo::isSymbolicLink() const;
  // 获取快捷方式/软链接指向的目标
  QString QFileInfo::symLinkTarget() const;
  ```

  注意：还有一个叫`isSymLink`的函数与上面提到的`isSymbolicLink`函数名字很像，这两个函数的区别是，`isSymLink`函数对于Windows系统的快捷方式（.lnk文件）和Unix系统的软链接都会返回`true`，而`isSymbolicLink`函数只对Unix系统的软链接返回`true`。但以后尽量不要再用`isSymLink`这个函数了，Qt会在以后的版本中去掉它。
- 遍历文件夹下的所有文件：

  ```cpp
  // 匿名空间，其中的函数相当于以前的静态函数，只在当前的源文件中有效
  namespace {
  bool isLink(const QFileInfo &dir) {
  #ifdef Q_OS_WINDOWS
      return dir.isShortcut();
  #else
      return dir.isSymbolicLink();
  #endif
  }
  }

  QVector<QString> Widget::getFolderContents(const QString &folderPath) const {
      if (folderPath.isEmpty() || !QFileInfo::exists(folderPath) || !QFileInfo(folderPath).isDir()) {
          return {};
      }
      const QFileInfo dirInfo(folderPath);
      const QDir dir(isLink(dirInfo) ? dirInfo.symLinkTarget() : dirInfo.canonicalFilePath());
      const auto fileInfoList = dir.entryInfoList(QDir::Files | QDir::Readable | QDir::Hidden | QDir::System, QDir::Name);
      const auto folderInfoList = dir.entryInfoList(QDir::Dirs | QDir::Readable | QDir::Hidden | QDir::System | QDir::NoDotAndDotDot, QDir::Name);
      if (fileInfoList.isEmpty() && folderInfoList.isEmpty()) {
          return {};
      }
      QVector<QString> stringList = {};
      if (!fileInfoList.isEmpty()) {
          for (const auto &fileInfo : fileInfoList) {
              stringList.append(QDir::toNativeSeparators(isLink(fileInfo) ? fileInfo.symLinkTarget() : fileInfo.canonicalFilePath()));
          }
      }
      if (!folderInfoList.isEmpty()) {
          for (const auto &folderInfo : folderInfoList) {
              const QVector<QString> _fileList = getFolderContents(isLink(folderInfo) ? folderInfo.symLinkTarget() : folderInfo.canonicalFilePath());
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
  - 也可以通过设置环境变量来实现这个功能（即不写任何代码）：将`QT_OPENGL`设置为`desktop`、`angle`或`software`。当使用ANGLE时，设置`QT_ANGLE_PLATFORM`这个环境变量为`d3d11`、`d3d9`或`warp`可以修改ANGLE默认使用的DirectX版本。但请注意，这些环境变量的名字及其值都是大小写敏感的。
- 设置Qt默认使用的OpenGL版本：

  ```cpp
  // 先获取原本的设置
  QSurfaceFormat surfaceFormat = QSurfaceFormat::defaultFormat();
  // 使用 QSurfaceFormat::OpenGLES 可以切换到 ANGLE
  // 等价于 QCoreApplication::setAttribute(Qt::AA_UseOpenGLES)
  // 或者 QCoreApplication::setAttribute(Qt::AA_UseDesktopOpenGL)
  surfaceFormat.setRenderableType(QSurfaceFormat::OpenGL);
  // 此处以 4.6 版本为例，不进行此项设置的话默认是 2.0，版本很老
  surfaceFormat.setVersion(4, 6);
  // 使用 QSurfaceFormat::CoreProfile 来禁用老旧的或废弃的 API
  surfaceFormat.setProfile(QSurfaceFormat::CompatibilityProfile);
  QSurfaceFormat::setDefaultFormat(surfaceFormat);
  ```

  注：以上代码必须在第一次调用OpenGL前使用，否则不会生效。
- 如何使能进行翻译的字符串都被`tr`函数包裹，不被遗漏：

  在全局定义`QT_NO_CAST_FROM_ASCII`这个宏，之后在编译时任何没有被`QLatin1String`（其等价于`QString::fromLatin1()`）或`QString::fromUtf8()`包裹的字符串都会报错。

  注：使用`QT_NO_CAST_TO_ASCII`可以禁止`QString`到本地8位编码字符串（`char *`）的转换。
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

- 无边框窗口+自定义标题栏+拖动窗体+8向拉伸+窗口阴影：<https://github.com/wangwenx190/framelesshelper>
- Qt Quick获取正在显示QML文档的系统窗口

  ```cpp
  QQmlApplicationEngine qmlApplicationEngine;
  qmlApplicationEngine.load(QUrl(QLatin1String("qrc:///qml/main.qml")));
  auto *window = qobject_cast<QWindow *>(qmlApplicationEngine.rootObjects().at(0));
  ```

  注：在使用`QQuickView`时，由于`QQuickView`本身就是继承自`QWindow`，因此可以直接对其本身进行操作。
- 限制程序只运行一个实例：
  - `QtSingleApplication`（Qt Solutions Component: Single Application。*Qt Solutions*是Qt4时代的商用模块，后来开源，但已不再维护，用起来还是没什么问题的，Qt Creator就一直在用这个）：<https://github.com/qtproject/qt-solutions/tree/master/qtsingleapplication>

    原理是使用`Qt Network`模块的`QLocalServer`以及`QLocalSocket`建立一个本地服务器，实现进程间通信。由于`Qt Network`模块是跨平台的，所以这个项目理论上也是能跨平台的。
  - `SingleApplication`（基于`QtSingleApplication`修改而来，做了很多改进，还在活跃开发中）：<https://github.com/itay-grudev/SingleApplication>

    使用了TCP/IP以及共享内存等多种技术，理论上也是跨平台的。
- 进程间通信（IPC）：7种方式
  - 4种跨平台方式：
    - Qt Remote Objects（QtRO）：使用`Qt Remote Objects`模块实现。官方示例工程位于`C:\Qt\Examples\Qt-5.14.0\remoteobjects`。
    - TCP/IP：使用`Qt Network`模块的`QLocalServer`以及`QLocalSocket`实现。官方示例代码位于`C:\Qt\Examples\Qt-5.14.0\corelib\ipc\localfortuneserver`以及`C:\Qt\Examples\Qt-5.14.0\corelib\ipc\localfortuneclient`。
    - 共享内存：使用`Qt Core`模块的`QSharedMemory`或`QSystemSemaphore`/`QSemaphore`实现。官方示例代码位于`C:\Qt\Examples\Qt-5.14.0\corelib\ipc\sharedmemory`。
    - QProcess：`QProcess`是`QIODevice`的派生类，因此支持`read`和`write`等函数。当然也可以通过传递和读取命令行参数实现通信。
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

    - Linux：D-Bus。使用`Qt D-Bus`模块实现。用法较简单，请自行查阅Qt手册。
    - Linux：Session Management。暂不了解。
- 下载文件：由于代码较多，我放到了一个单独的仓库中 <https://github.com/wangwenx190/qdownloader>
- 计算文件哈希值：`QCryptographicHash`

  ```cpp
  QFile file(QLatin1String("D:/setup.exe"));
  file.open(QFile::ReadOnly);
  // 其他算法请参考 QCryptographicHash::Algorithm 这个枚举。
  QCryptographicHash cryptographicHash(QCryptographicHash::Sha256);
  cryptographicHash.addData(&file);
  const QString hash = QLatin1String(cryptographicHash.result().toHex());
  ```

  注：当数据量不大时，可以通过使用静态函数`[static] QByteArray QCryptographicHash::hash(const QByteArray &data, QCryptographicHash::Algorithm method)`直接计算哈希值。

- Qt框架下的服务程序（Windows services与Unix daemons）

  - `QtService`（Qt Solutions Component: Service）：<https://github.com/qtproject/qt-solutions/tree/master/qtservice>

    支持 Windows + Unix 平台，但早已经停止维护了（2011年左右），是一个比较老的代码库。用还是能用的，只不过可能用了不少现在看来已经非常过时的技术。
  - `QtService`：<https://github.com/Skycoder42/QtService>

    支持 Windows + Linux + macOS，代码库较新
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

  注：程序的目标架构还可以通过一些宏来进行判断，Qt提供的宏有`Q_PROCESSOR_X86`（x86架构，包含32位和64位）、`Q_PROCESSOR_X86_32`（32位x86架构）、`Q_PROCESSOR_X86_64`（64位x86架构）、`Q_PROCESSOR_IA64`（英特尔64位架构）、`Q_PROCESSOR_ARM`（`ARM`架构，目前包含`V5`、`V6`和`V7`）等，具体请查阅Qt手册。Windows平台还提供了`WIN64`/`_WIN64`等宏。

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
- Windows上启用*DWM*（*Desktop Window Manager*）特性：`Qt Windows Extras`模块（QMake：`QT += winextras`）
  - 任务栏进度条+角标

    ```cpp
    // 要想获取并操作任务栏进度条，必须先新建一个QWinTaskbarButton
    QWinTaskbarButton *winTaskbarButton = new QWinTaskbarButton(this);
    // 此处要传一个QWindow的指针。QWidget可以用windowHandle这个函数来获取
    winTaskbarButton->setWindow(windowHandle());
    // 设置任务栏图标的角标样式。此处使用了Qt提供的标准图标，您也可以使用自己的资源文件：setOverlayIcon(QIcon(":/loading.png"));
    // 角标也不是必须的，如果不设置角标，则不会显示角标
    winTaskbarButton->setOverlayIcon(style()->standardIcon(QStyle::SP_MediaPlay));
    // 获取任务栏进度条
    QWinTaskbarProgress *winTaskbarProgress = winTaskbarButton->progress();
    // 使用hide函数可以彻底隐藏进度条
    winTaskbarProgress->show();
    // 设置进度条的进度。使用setMaximum和setMinimum这两个函数或setRange这一个函数来设置进度条的进度范围，默认为0~100。
    // 使用reset函数可以将进度重置为最小值
    winTaskbarProgress->setValue(50);
    // 调用resume函数会使进度条显绿色，调用pause函数会使进度条显黄色，调用stop函数会使进度条显红色。
    winTaskbarProgress->resume();
    ```

  - 任务栏小按钮

    ```cpp
    // 要想创建任务栏小按钮，必须先新建一个QWinThumbnailToolBar
    QWinThumbnailToolBar *winThumbnailToolBar = new QWinThumbnailToolBar(this);
    winThumbnailToolBar->setWindow(windowHandle());
    QWinThumbnailToolButton *winThumbnailToolButton1 = new QWinThumbnailToolButton(winThumbnailToolBar);
    winThumbnailToolButton1->setToolTip(tr("Play"));
    // 此选项设置为true可以使预览窗口在按钮点击后关闭。默认值为false
    winThumbnailToolButton1->setDismissOnClick(true);
    winThumbnailToolButton1->setIcon(style()->standardIcon(QStyle::SP_MediaPlay));
    connect(winThumbnailToolButton1, &QWinThumbnailToolButton::clicked, this, [](){
      // Do something
    });
    // 将按钮添加到任务栏
    winThumbnailToolBar->addButton(winThumbnailToolButton1);
    ```

  - 任务列表

    ```cpp
    // QWinJumpListItem::Link 代表此item指向了一个应用程序
    // QWinJumpListItem::Destination 代表此item指向了一个此应用程序能打开的文件
    // QWinJumpListItem::Separator 代表此item为一个分隔符（一个横线）
    QWinJumpListItem *newProject = new QWinJumpListItem(QWinJumpListItem::Link);
    newProject->setTitle(tr("Create new project"));
    // 只有 QWinJumpListItem::Link 类型的item支持设置描述
    newProject->setDescription(tr("A shortcut to create a new project"));
    newProject->setIcon(QIcon(":/images/new_project.svg"));
    newProject->setFilePath(QDir::toNativeSeparators(QCoreApplication::applicationFilePath()));
    newProject->setWorkingDirectory("C:\\temp");
    newProject->setArguments(QStringList{"--new-project", "--no-update", "--single-instance"});
    QWinJumpList winJumpList;
    // 任务
    QWinJumpListCategory *tasks = winJumpList.tasks();
    tasks->addItem(newProject);
    // 添加一个分隔符。只有“任务”列表支持添加分隔符。
    tasks->addSeparator();
    // 使用 addLink 可以不像上面那样分开设置
    tasks->addLink(QIcon(":/images/sdk_manager.svg"), tr("Launch SDK Manager"), QDir::toNativeSeparators(QCoreApplication::applicationDirPath()) + "\\sdk-manager.exe", QStringList{"/WITHGUI", "/EXAMPLE"});
    tasks->setVisible(true);
    // 最近文件
    QWinJumpListCategory *recent = winJumpList.recent();
    recent->setVisible(true);
    // 常用文件
    QWinJumpListCategory *frequent = winJumpList.frequent();
    frequent->setVisible(true);
    ```

  注：任务栏进度条和任务栏小按钮都要使用`setWindow`函数设置一个有效的`QWindow`句柄，但这个句柄只有在`showEvent`执行完毕后（即窗口显示出来以后）才能获得（请参考：<https://codereview.qt-project.org/c/qt/qtwinextras/+/278355>）。

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
  | `-o <file>` | 指定输出文件 |
  | `-name <name>` | 使用`name`创建一个外部的初始化函数 |
  | `-root <path>` | 设置资源文件的根节点（添加到资源文件自己的根节点之前，如果有的话） |
  | `-compress-algo <algo>` | 设置压缩算法，可选值为`zstd`（推荐，三者中压缩率和解压速度最优）、`zlib`和`none` |
  | `-compress <level>` | 设置压缩级别。zstd：1\~19，zlib：1\~9。数字越大，压缩率越高 |
  | `-threshold <level>` | 设置压缩阈值（临界值），可取值为1~100，只有文件大小的减小量超过原文件大小的`level %`，rcc才会压缩它，否则rcc会直接存储而不进行压缩。默认值为70 |
  | `-binary` | 输出一个二进制文件以便动态加载（可以视为经过压缩和加密的外部资源文件） |
- 显示托盘图标+自定义托盘菜单（使用`QWidget`而不是`QMenu`）+弹出消息

  ```text
  // 返回当前操作系统是否支持显示托盘图标
  [static] bool QSystemTrayIcon::isSystemTrayAvailable();
  // 返回当前操作系统是否支持显示气泡消息
  [static] bool QSystemTrayIcon::supportsMessages();
  // 设置托盘菜单
  void QSystemTrayIcon::setContextMenu(QMenu *menu);
  ```

  技巧：
    1. `QMenu`是`QWidget`的派生类，因此，可以直接把一个`QWidget`传给`setContextMenu`，使用这个方法就可以不受`QMenu`的限制了，可以将任何`QWidget`设置为托盘菜单。
    2. 使用`setToolTip`函数设置托盘图标的提示信息
    3. 使用`setIcon`函数设置托盘图标的图标
    4. 使用`showMessage`函数来弹出气泡消息
    5. 使用`show`或`hide`函数来显示或隐藏托盘图标

- 多线程：3种方式
  - `Qt Concurrent`模块。如果需要获取线程函数的返回值，或者线程的执行状态和进度，需要搭配`QFuture`一起使用。

    ```cpp
    // 用法1：在另外一个线程中执行函数（默认线程池）
    extern void aFunction();
    QFuture<void> future = QtConcurrent::run(aFunction);
    // 用法2：在指定线程池中执行函数
    extern void aFunction();
    QThreadPool pool;
    QFuture<void> future = QtConcurrent::run(&pool, aFunction);
    // 用法3：给函数传递参数
    extern void aFunctionWithArguments(int arg1, double arg2, const QString &string);
    int integer = 0;
    double floatingPoint = 0;
    QString string = "Test";
    QFuture<void> future = QtConcurrent::run(aFunctionWithArguments, integer, floatingPoint, string);
    // 用法4：获取函数返回值
    extern QString functionReturningAString();
    QFuture<QString> future = QtConcurrent::run(functionReturningAString);
    QString result = future.result();
    // 用法5：传参+获取返回值
    extern QString someFunction(const QByteArray &input);
    QByteArray bytearray = "hello world";
    QFuture<QString> future = QtConcurrent::run(someFunction, bytearray);
    QString result = future.result();
    // 用法6：调用成员函数（const函数）
    // 函数原型：QList<QByteArray>  QByteArray::split(char sep) const
    QByteArray bytearray = "hello world";
    QFuture<QList<QByteArray> > future = QtConcurrent::run(bytearray, &QByteArray::split, ',');
    QList<QByteArray> result = future.result();
    // 用法7：调用成员函数（非const函数）
    // 函数原型：void QImage::invertPixels(InvertMode mode)
    QImage image = ...;
    QFuture<void> future = QtConcurrent::run(&image, &QImage::invertPixels, QImage::InvertRgba);
    future.waitForFinished();
    // 此刻，image本身已经被处理完毕了
    // 用法8：调用匿名函数
    QFuture<void> future = QtConcurrent::run([=]() { /* Do something */ });
    QFuture<QVector<QString>> future = QtConcurrent::run([=]() -> QVector<QString> { /* Do something */ });
    ```

  - `void QObject::moveToThread(QThread *targetThread);`

    ```cpp
    QThread workerThread;
    Worker *worker = new Worker; // 一个QObject的派生类
    worker->moveToThread(&workerThread);
    connect(&workerThread, &QThread::finished, worker, &QObject::deleteLater);
    // 触发耗时操作
    connect(this, &Controller::operate, worker, &Worker::doWork);
    // 当耗时操作执行完毕后，获取并处理结果
    connect(worker, &Worker::resultReady, this, &Controller::handleResults);
    workerThread.start();
    // 如何停止线程
    workerThread.quit();
    workerThread.wait();
    ```

  - 【已过时，不推荐使用】重写`QThread`的`run`函数：`[virtual protected] void QThread::run();`
- Qt Creator添加自定义协议模板

  菜单栏 -> 工具 -> 选项 -> C++ -> 文件命名 -> License template
- Qt Creator添加自定义注释模板

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
- Qt中继承`QWidget`之后，样式表不起作用：三个方法
  1. 设置属性`setAttribute(Qt::WA_StyledBackground, true);`
  2. 改成继承`QFrame`，因为`QFrame`自带`paintEvent`函数已做了实现，在使用样式表时会进行解析和绘制。
  3. 重新实现`QWidget`的`paintEvent`函数时，使用`QStylePainter`绘制。

     ```cpp
     void myclass::paintEvent(QPaintEvent *) {
         QStyleOption o;
         o.initFrom(this);
         QPainter p(this);
         style()->drawPrimitive(QStyle::PE_Widget, &o, &p, this);
     }
     ```

- Qt默认不支持大资源文件，需要的话要手动开启：
  - QMake：`CONFIG += resources_big`
  - CMake：`qt5_add_big_resources(<VAR> file1.qrc [file2.qrc ...] [OPTIONS ...])`

  具体的原理是处理小文件时rcc会将其翻译为C++代码，然后与项目其他的源文件一起参与编译和链接过程，而处理大文件时rcc会直接生成.obj文件，不参与编译过程，只参与最后的链接过程。
- 如果需要窗口无边框，但是又需要保留操作系统的边框特性，例如可以自由拉伸边框和窗口阴影等，可以使用 `setWindowFlags(Qt::Window | Qt::CustomizeWindowHint);`。注意一定要用`setWindowFlags`而不是`setWindowFlag`，因为`CustomizeWindowHint`这个Flag会与其他Flag冲突，这些Flag并存时会导致`CustomizeWindowHint`失效，用前者正好可以顺便清除其他Flag。

  注意事项：
  - `setWindowFlag(s)`是`QWidget`独有的函数，`QWindow`请使用`setFlag(s)`。
  - 在Windows平台上，这种窗口的顶端会有一个几像素宽的白条，我专门请教过Qt公司的工程师*Friedemann Kleint*，他调试后告诉我，这个白条在这种状态下是无法去掉的，要去掉白条就只能将系统提供的窗口边框同时去掉，即那个白条和窗口边框是一体的，无法单独去掉。
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

  ```cpp
  // 第一步：如果窗口被隐藏了，显示出来。
  // QWindow 没有 isHidden 函数，请使用 isVisible 函数代替。
  if (isHidden()) {
    show();
  }
  // 第二步：如果窗口被最小化了，恢复原始的大小和状态
  // QWindow 没有 isMinimized 函数，请使用以下语句进行判断
  // if (windowStates().testFlag(Qt::WindowMinimized)) { /* ... */ }
  if (isMinimized()) {
    // QWindow 没有 setWindowState 以及 windowState 函数，请使用 setWindowStates 以及 windowStates 函数代替。
    setWindowState(windowState() & ~Qt::WindowMinimized);
    // 不要用 showNormal 函数，因为如果窗口被最大化了，也会被还原
  }
  // 第三步：如果仍然不是前台窗口，则手动将其移到前台
  // QWindow 没有 isActiveWindow 函数，请使用 isActive 函数代替。
  if (!isActiveWindow()) {
    // 我看了QPA的源码，在激活窗口的过程中，窗口就已经被移到最前端了，不需要手动执行raise()函数
    // 也不需要手动执行alert()函数，因为Windows会自动进行闪烁
    // QWindow 没有 activateWindow 函数，请使用 requestActivate 函数代替。
    activateWindow();
  }
  ```

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
  - 将所有源码文件（.h、.hpp、.c、.cpp、.qml等）的文本编码都改为`UTF-8`，最好不要带`BOM`。
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

  可以参考<https://github.com/buzzySmile/qBreakpad>这个项目，虽然早就不维护了，但仍然有学习的价值。这个项目是GitHub上为数不多的适合Qt项目的Crash Handler框架了。
- `QDialog`窗口默认会阻塞整个应用程序的执行，如果不想这样，可以进行以下设置：

  ```cpp
  QDialog dialog;
  // 这行代码是关键，具体请查阅Qt官方手册
  dialog.setWindowModality(Qt::WindowModal);
  dialog.exec();
  ```

- 删除Qt对象时，强烈建议使用`void QObject::deleteLater()`而不是直接`delete`，因为`deleteLater`会选择在合适的时机进行释放，而`delete`会立即释放，很可能会导致程序出错崩溃。
- 如果要批量删除Qt对象，或删除一个容器里包含的所有对象，可以用`qDeleteAll`：

  ```cpp
  // 容器里存放的全部元素必须都是指针
  QVector<QPushButton *> buttons;
  // 按照需求获取要删除的对象，并将其填充到容器中
  qDeleteAll(buttons);
  // qDeleteAll 不会删除容器里面的元素本身，它只会对里面的对象调用 delete，因此我们要手动清空容器
  buttons.clear();
  ```

- 读写文本数据时推荐使用`QTextStream`。这个类优化的很好，读写速度很快，比直接用`QFile`进行读写快得多。而且用这个类还能设置字符编码，还支持很多其他的功能，没有理由不用它。具体用法请查阅Qt官方手册。
  - 读写文件

    ```cpp
    QFile file(QLatin1String("data.txt"));
    file.open(QFile::WriteOnly | QFile::Text | QFile::Truncate);
    QTextStream out(&file);
    // GB 18030-2000 是最新的简体中文国家标准。GBK 和 GB 2312 是老国标了，不要再用了。
    // QTextStream 的默认文本编码为 UTF-8，不用显式指定。
    // 关于其他Qt所支持的文本编码，请参考 QTextCodec 类。
    out.setCodec("GB18030");
    out << 233 << endl;
    file.close();
    file.open(QFile::ReadOnly | QFile::Text);
    int res = -1;
    out >> res;
    file.close();
    ```

  - 读写控制台

    ```cpp
    // 从标准输入流（控制台）读取数据
    QTextStream in(stdin);
    QString line;
    while (in.readLineInto(&line)) {
      // 进行处理
    }
    // 输出到标准输出流（控制台）
    QTextStream out(stdout);
    out << "An output message from QTextStream." << endl;
    // 输出到标准错误流（控制台）
    QTextStream err(stderr);
    err << "An error message from QTextStream." << endl;
    ```

- 读写二进制数据时推荐使用`QDataStream`。

  这个类是用来读写二进制数据流的，而二进制数据流的内容是不能被第三方软件读取的，因此其常见用途为读写软件独有的文档/数据格式。

  C++的基本类型都是支持的，`QBrush`、`QColor`、`QDateTime`、`QFont`、`QPixmap`、`QString`以及`QVariant`等Qt自己的类型也都支持。请查看Qt手册的`Serializing Qt Data Types`章节来获取`QDataStream`支持的所有Qt类型。

  随着Qt版本的升级，`QDataStream`也会升级版本，不同版本的`QDataStream`所输出的内容是不能互相兼容的，因此在读写数据之前，一定要先确认好`QDataStream`的版本：`void QDataStream::setVersion(int v)`。
  - 写

    ```cpp
    // 软件自己的文档格式
    QFile file("my_doc.abc");
    file.open(QFile::WriteOnly);
    QDataStream out(&file);
    // 文件头：一个“神奇的数字”+文档版本号
    out << (quint32)0xA0B0C0D0; // 识别格式用的
    out << (qint32)123; // 此文档的版本号为 123
    out.setVersion(QDataStream::Qt_5_14);
    // 写入真正的数据
    out << lots_of_interesting_data;
    file.close();
    ```

  - 读

    ```cpp
    QFile file("my_doc.abc");
    file.open(QFile::ReadOnly);
    QDataStream in(&file);
    // 读取文件内容，检查文件头
    quint32 magic;
    in >> magic;
    if (magic != 0xA0B0C0D0) {
      // 文件头无法对应：不是正确的格式或文件已损坏
      return XXX_BAD_FILE_FORMAT;
    }
    // 读取版本（例如：Office 2007）
    qint32 version;
    in >> version;
    if (version < 100) {
      // 版本太老，不能兼容（示例）
      return XXX_BAD_FILE_TOO_OLD;
    }
    if (version > 123) {
      // 版本太新，不能兼容（示例）
      return XXX_BAD_FILE_TOO_NEW;
    }
    // 根据文档中记录的版本号，来设置 QDataStream 的版本
    if (version <= 110) {
      in.setVersion(QDataStream::Qt_5_7);
    } else {
      in.setVersion(QDataStream::Qt_5_14);
    }
    // 读取真正的数据
    in >> lots_of_interesting_data;
    // 读取某版本特有的数据段（示例）
    if (version >= 120) {
      in >> data_new_in_XXX_version_1_2;
    }
    in >> other_interesting_data;
    file.close();
    ```

- Qt如何编写升级程序
  - [QSimpleUpdater](https://github.com/alex-spataru/QSimpleUpdater)

    跨平台。此项目对程序的安装及升级程序没有任何要求。这个项目的代码也比较简短，可以根据自己的需求自行修改扩充。
  - [QtAutoUpdater](https://github.com/Skycoder42/QtAutoUpdater)

    跨平台。此项目要求程序的安装及升级程序必须使用[Qt IFW](http://download.qt.io/official_releases/qt-installer-framework/)制作。
- Qt显示PDF文档
- Qt打印文件
- Qt处理压缩文件
- `winId()`、`effectiveWinId()`以及`windowHandle()->winId()`的区别
  - `winId()`函数

    `QWidget`和`QWindow`都有此函数，返回的是被执行对象的窗口系统标识符（我自己翻译的，不知道正规叫法是什么，Windows平台上是叫窗口句柄）。在Windows平台上就是`HWND`，但要用`reinterpret_cast`转换一下才能用。这个函数的返回值有可能会在运行时发生改变，如果你的程序对此变化敏感，请注意自行处理`QEvent::WinIdChange`这个事件。

    `QWindow`用这个函数没什么需要注意的，但`QWidget`不同。`QWidget`很有可能是一个子控件（例如一个很复杂的窗口中的某个小部件），在这种情况下执行这个函数就会导致Qt为其创建一个窗口句柄，使其成为一个原生窗口。

    用这个函数获取`QWidget`的窗口句柄是Qt4时代的做法，现在已经不推荐这样用了。如果您要获取一个`QWidget`的窗口句柄，请根据具体情况，使用下面两种方法中的一种。
  - `effectiveWinId()`函数

    此函数为`QWidget`独有，当被执行对象为原生窗口（即一个独立的窗口，不是什么窗口内部的子控件）时，返回窗口句柄，当不是原生窗口时，返回当前对象所在的顶级窗口的句柄。

    注意不要储存这个函数的返回值，现用现取就可以了，因为这个函数的返回值可能会在运行时发生变化。
  - `windowHandle()->winId()`函数

    `windowHandle()`这个函数为`QWidget`独有，当`QWidget`是原生窗口时，它返回的是`QWidget`所绑定的`QWindow`（`QWidget`都是显示在`QWindow`上的）的指针，当`QWidget`不是原生窗口时，返回空指针。所以，`windowHandle()->winId()`的作用就是，当`QWidget`为原生窗口时返回其窗口句柄，当不是原生窗口时返回空指针（当然在这个过程中，你要自己判断下`windowHandle()`的返回值是否为空）。

    同样不要存储这个函数的返回值，运行时会发生变化，现用现取即可。

  对比：这三种方法中的后两种其实都是差不多的，第一种和后两种最主要的区别是，看你要获取的句柄到底是窗口的句柄还是控件的句柄。后两种都是用来获取窗口句柄的，第一种当`QWidget`是一个控件的时候获取到的是这个控件的句柄，而不是它所在的窗口的句柄。而`QWindow`就是底层的窗口类，本身就是不能作为控件使用的，因此对`QWindow`执行`winId()`总是返回其窗口句柄。
- MVC
- 添加、删除、更新和获取环境变量

  ```cpp
  // 返回环境变量varName是否已经被设置（但可为空）。等价于!qgetenv(varName).isNull()，但下面这个函数快得多
  bool qEnvironmentVariableIsSet(const char *varName);
  // 返回环境变量varName是否为空。等价于qgetenv(varName).isEmpty()，但下面这个函数快得多
  bool qEnvironmentVariableIsEmpty(const char *varName);
  // 返回环境变量varName的值。如果值为空或者获取失败，则返回默认值defaultValue
  QString qEnvironmentVariable(const char *varName, const QString &defaultValue);
  // 返回环境变量varName的整型数值。如果ok不是空指针，则根据函数执行结果将ok设置为true或false。等价于qgetenv(varName).toInt(ok, 0)，但下面这个函数快得多
  int qEnvironmentVariableIntValue(const char *varName, bool *ok = nullptr);
  // 返回环境变量varName的值，但结果类型为QByteArray。不要在Windows平台使用这个函数，因为可能会产生数据丢失，但在Unix平台不存在这个问题。
  QByteArray qgetenv(const char *varName);
  // 将环境变量varName的值设置为value。如果环境变量不存在，这个函数会创建一个。如果将一个环境变量设置为空，在Windows平台会导致这个环境变量被移除，但在Unix平台会导致一个空的环境变量被创建（而不是被移除）。如果确实要移除一个环境变量，请使用qunsetenv，而不是将其设置为空。
  bool qputenv(const char *varName, const QByteArray &value);
  // 将环境变量varName移除。
  bool qunsetenv(const char *varName);
  ```

- 读写数据库
- 使用`QTimer::singleShot`可以实现延时执行部分代码，在某些特定情况下特别好用，请留意。

  ```cpp
  // 延时为0也是可以的
  QTimer::singleShot(0, [](){
    // Do something
  });
  ```

- 使用弱属性机制，可以很方便的实现许多意想不到的效果：

  ```cpp
  // 设置属性及其值。如果此属性不存在，则新建一个，如果存在，则更新其值。
  bool QObject::setProperty(const char *name, const QVariant &value);
  // 获取属性的值
  QVariant QObject::property(const char *name) const;
  // 获取所有弱属性的名字
  QList<QByteArray> QObject::dynamicPropertyNames() const;
  ```

- 获取类的元对象属性及其值（非弱属性）

  ```cpp
  const auto* metaObject = obj->metaObject();
  for (int i = metaObject->propertyOffset(); i != metaObject->propertyCount(); ++i) {
    const auto metaProperty = metaObject->property(i);
    const auto name = metaProperty.name();
    const auto typeName = metaProperty.typeName();
    const auto value = object->property(name);
    qDebug() << name << typeName << value;
  }
  ```

- `This application failed to start because it could not find or load the Qt platform plugin`错误如何解决？

  此错误仅在Qt程序无法找到或成功加载*QPA*（*Qt Platform Abstract*）插件时才会发生。静态链接Qt库的程序不会发生此错误，只有动态链接或加载Qt库的程序才有可能会发生这个错误。在Windows平台，此插件存在于`C:\Qt\5.14.0\msvc2017_64\plugins\platforms`（以Qt 5.14.0 msvc2017 64-bit为例，且安装到默认位置），发布版文件为`qwindows.dll`，调试版文件为`qwindowsd.dll`，请确保您开发的Qt程序所在的文件夹下存在`platforms`（或`plugins\platforms`）文件夹且`qwindows(d).dll`位于其中，并注意32位和64位的DLL不能混用。另，使用[`UPX`](https://github.com/upx/upx)等压缩或加壳程序处理过的QPA插件也经常无法正常加载，这点也要十分注意。其他平台的QPA插件也大同小异，与Windows平台相比，只是文件名和后缀名有所区别。

  注：所有Qt插件（`plugins`文件夹下的所有内容）都不要用任何第三方程序（例如`UPX`）进行处理，否则会导致Qt程序行为异常。
- 在`QTableView`控件中，如果需要自定义的列按钮、复选框、下拉框等其他模式显示，可以采用自定义委托`QItemDelegate`来实现，如果需要禁用某列，则在自定义委托的重载`createEditor`函数返回`0`即可。自定义委托对应的控件在进入编辑状态的时候出现，如果想一直出现，则需要重载`paint`函数用`drawPrimitive`或者`drawControl`来绘制。
- 将`QApplication::style()`对应的`drawPrimitive`、`drawControl`、`drawItemText`、`drawItemPixmap`等几个方法用熟悉了，再结合`QStyleOption`属性，可以玩转各种自定义委托，还可以直接使用`paint`函数中的`painter`进行各种绘制，各种牛逼的表格、树状列表、下拉框等，绝对屌炸天。`QApplication::style()->drawControl`的第4个参数如果不设置，则绘制出来的控件不会应用样式表。
- 在已知背景色的情况下，为了能够清晰的绘制文字，这个时候需要计算合适的文字颜色：

  ```cpp
  // 根据背景色自动计算合适的前景色
  double gray = (0.299 * color.red() + 0.587 * color.green() + 0.114 * color.blue()) / 255;
  QColor textColor = gray > 0.5 ? Qt::black : Qt::white;
  ```

- `QTableView`或者`QTableWidget`禁用列拖动：

  ```cpp
  ui->tableView->horizontalHeader()->setSectionResizeMode(0, QHeaderView::Fixed);
  ```

- `QVariant`提供了`toInt`、`toReal`、`toBool`、`toString`、`toList`以及`toMap`等方法，可以方便的将`QVariant`转为各种具体的类型，如果遇到想要转换的类型没有类似的转换函数，可以用以下方法手动进行转换：

  ```cpp
  QVariant variant;
  // 某个类型（可以是C++/Qt提供的标准类型，也可以是用户自己定义的类型）
  MyCustomStruct myCustomStruct;
  // 判断 QVariant 能否被转换为指定的类型
  if (variant.canConvert<MyCustomStruct>()) {
      // 取出 QVariant 的值并转换为指定的类型
      myCustomStruct = variant.value<MyCustomStruct>();
  }
  // qvariant_cast 等价于 QVariant::value()
  // 如果不加判断直接进行转换，转换失败时会返回一个默认的值
  QColor color = qvariant_cast<QColor>(variant);
  // 使用 type() 函数可以获取 QVariant 具体的类型
  // 其他类型的枚举请查看 QMetaType::Type 这个枚举
  if (variant.type() == QVariant::Font) {
      // 进行转换或其他操作
  }
  ```

- Qt支持安装多个翻译文件，但如果有重复的翻译条目，后来安装的翻译文件会覆盖之前的。同时还可以用`[static] bool QCoreApplication::removeTranslator(QTranslator *translationFile)`这个API来移除某个特定的已安装的翻译文件。

  注：大多数`QWidget`及其右键菜单（如果有的话）基本都是没有中文翻译的，如果想要完全汉化，则需要自行生成*widgets.ts*并手动翻译。
- 如何获取*此电脑*（*计算机*、*我的电脑*）、以及*回收站*（*垃圾桶*、*废纸篓*）等系统程序的图标或某个具体的文件的图标
  - 系统程序的图标

    ```cpp
    QFileIconProvider fileIconProvider;
    // 其他程序的图标请参考 QFileIconProvider::IconType 这个枚举
    QIcon icon = fileIconProvider.icon(QFileIconProvider::Computer);
    ```

  - 本地文件的图标

    ```cpp
    // 这个文件必须真实存在，否则无法获取图标
    QFileInfo fileInfo("D:/test.docx");
    QFileIconProvider fileIconProvider;
    // 获取fileInfo所指向的文件的图标
    QIcon icon = fileIconProvider.icon(fileInfo);
    ```

    注：使用`QFileIconProvider`获取本地文件的图标只有传`QFileInfo`这一个方法，没有第二种。

- 创建临时文件或临时文件夹

  临时文件或文件夹在某些特殊场景下是非常有用的，比如软件下载更新包，可以先下载到一个临时文件夹中，下载完毕校验成功后再进行下一步的操作。为此，Qt提供了`QTemporaryFile`和`QTemporaryDir`这两个类，它们都可以保证生成一个绝对独一无二的临时文件（夹），默认情况下它们所生成的临时文件（夹）会在其析构时删除，保证不会产生任何残留，但这个行为可以通过`setAutoRemove`这个函数修改。所生成的文件（夹）的路径也可以通过`path`这个函数获取。更多用法请自行查看文档。
- 裁剪Qt，在尽量不影响性能的情况下，使编译得到的二进制文件最小：[Qt Lite](https://qtlite.com/)，通过编译前配置参数，来去掉尽可能多的无用特性。
- 不同的`QObject`及其派生类的对象，在进行类型转换时，建议使用`qobject_cast`而不是`dynamic_cast`，因为前者不需要开启`RTTI`。

  ```cpp
  QObject *obj = new QTimer; // QTimer inherits QObject
  QTimer *timer = qobject_cast<QTimer *>(obj); // timer == (QObject *)obj
  QAbstractButton *button = qobject_cast<QAbstractButton *>(obj); // button = nullptr
  ```

  待转换的对象必须直接或间接继承自`QObject`，并且使用`Q_OBJECT`宏进行了声明。如果不符合以上条件，则返回空指针。
- 访问和操作系统剪贴板
  - 获取剪贴板的内容

    ```cpp
    void DropArea::paste() {
        // 获取全局剪贴板的指针
        const QClipboard *clipboard = QGuiApplication::clipboard();
        // 方法1：通过剪贴板的 QMimeData 来获取其中的所有内容
        const QMimeData *mimeData = clipboard->mimeData();
        if (mimeData->hasImage()) {
            setPixmap(qvariant_cast<QPixmap>(mimeData->imageData()));
        } else if (mimeData->hasHtml()) {
            setText(mimeData->html());
            setTextFormat(Qt::RichText);
        } else if (mimeData->hasText()) {
            setText(mimeData->text());
            setTextFormat(Qt::PlainText);
        } else {
            setText(tr("Cannot display data"));
        }
        // 方法2：直接读取剪贴板中的内容
        // const QString originalText = clipboard->text();
        // const QImage originalImage = clipboard->image();
        // const QMimeData *originalMimeData = clipboard->mimeData();
        // const QPixmap originalPixmap = clipboard->pixmap();
        // 方法1的好处是能提前判断一下，缺点是不如方法2来的直接
    }
    ```

  - 向剪贴板中填充内容

    ```cpp
    const QClipboard *clipboard = QGuiApplication::clipboard();
    // 清空剪贴板
    clipboard->clear();
    // 将指定内容填充到剪贴板
    clipboard->setText(newText);
    clipboard->setPixmap(newPixmap);
    clipboard->setMimeData(newMimeData);
    clipboard->setImage(newImage);
    ```

- 应用程序在执行耗时的操作时，如何防止界面卡顿
  - 在耗时操作的执行过程中，时不时的调用`QCoreApplication::processEvents()`函数。例如：

    ```cpp
    auto busyWork = []() {
        // 这里以一个次数非常多的循环来模拟耗时操作
        for (int i = 0; i != 1000000; ++i) {
            qDebug() << i << endl;
            QCoreApplication::processEvents();
        }
    };
    // 通过按钮触发这个耗时操作
    connect(ui->pushButton, &QPushButton::clicked, this, busyWork);
    ```

    效果：界面不会完全卡住，但在配置不够高的机器上还是有所卡顿。
  - 使用`Qt Concurrent`模块实现多线程

    ```cpp
    // 实际的耗时操作
    static bool actualWork() {
        for (int i = 0; i != 1000000; ++i) {
            qDebug() << i << endl;
        }
        return true;
    }
    // 多线程
    auto busyWork = []() -> void {
        // QFuture 非常强大好用，更多具体的用法请自行查阅 Qt 手册
        // 如果不需要获取线程函数的返回值，或者不需要获取线程执行的状态和进度，可以不用 QFuture
        // QtConcurrent::run 支持匿名函数，但像这样分开写也是可以的
        QFuture<bool> future = QtConcurrent::run(actualWork);
        while (!future.isFinished()) {
            QCoreApplication::processEvents(QEventLoop::AllEvents, 100);
        }
    };
    connect(ui->pushButton, &QPushButton::clicked, this, busyWork);
    ```

    效果：界面完全不会卡住，哪怕是在配置很低的机器上。
  - 传统的多线程：`void QObject::moveToThread(QThread *targetThread);`

    效果：界面完全不会卡住，哪怕是在配置很低的机器上。
  - 【已过时，不推荐使用】重写`QThread`的`run`函数来实现多线程：`[virtual protected] void QThread::run();`

    效果：界面完全不会卡住，哪怕是在配置很低的机器上。

  总结：在执行耗时操作时，推荐使用`Qt Concurrent`模块实现多线程，不仅完全不会使界面卡住，还能以较少的代码实现较多较高级的功能，用起来十分方便舒心。
- 监视文件（夹）的变化：请自行查阅`QFileSystemWatcher`的用法。
- 向Qt提交补丁

  ```bash
  # 【非常重要】根据 https://wiki.qt.io/Setting_up_Gerrit 设置好 Gerrit（一次性）
  # 到 https://code.qt.io/cgit/ 克隆只读仓库（一次性）
  # 此处以 qtbase 模块为例
  git clone https://code.qt.io/qt/qtbase.git
  # 设置 git hook（一次性）
  gitdir=$(git rev-parse --git-dir); scp -P 29418 codereview.qt-project.org:hooks/commit-msg ${gitdir}/hooks/
  # 添加 Gerrit 远端（一次性）
  # 此处以 qtbase 模块为例
  git remote add gerrit ssh://codereview.qt-project.org/qt/qtbase
  # 切换到需要的分支。此处以 5.14 分支为例。
  git checkout 5.14
  # 新建分支用于制作补丁
  git checkout -b my-fix
  # 修改文件
  # 提交到本地仓库
  git commit -a
  # 推送到 Gerrit 远端。此处以 5.14 分支为例。
  git push gerrit HEAD:refs/for/5.14
  # 到 https://codereview.qt-project.org/ 查看并找人 review
  ```

  注：如果需要修改已经提交的内容（例如代码审阅人提出了修改意见），可以在本地分支直接修改（切换到补丁所在的分支之后就不要再动*Git*了，直接在上次提交的基础上进行修改），然后使用`git commit -a --amend`修改上一次的提交（所有内容都改完了，再执行这一条命令即可，这条命令的意思是，当前所做的所有的改动，都是对上一次提交的改动，而不是创建一个新的提交），再重新推送到远端即可。这样会在*JIRA*上形成多个*patch set*。注意在执行`git commit`命令时一定不要忘了`-a`和`--amend`这两个参数，`-a`这个参数的作用是把所有未跟踪的文件添加到跟踪列表，`--amend`参数的作用是指示*Git*此次操作是修改而不是创建提交。

- 在Qt 5.10以后，表格控件`QTableWidget`或`QTableView`默认的最小列宽改成了15，以前的版本是0。所以在新版的Qt中，如果设置表格列宽时数值过小，小于15（即默认的最小列宽），将不会生效。所以如果要设置比默认的最小列宽更小的列宽需要修改最小列宽：`ui->tableView->horizontalHeader()->setMinimumSectionSize(0);`
- 不要直接在构造函数里获取控件的尺寸和位置等信息，因为Qt的各种控件在被显示出来之前是不具备这些信息的，虽然能获取到具体的数字，但不一定准确。如果一定要获取类似的信息，一定要等它们显出出来再进行。
- Qt中有个全局的焦点切换信号`focusChanged`，可以用它做自定义的输入法。Qt4中默认会安装输入法上下文，这个默认安装的输入法上下文会拦截两个信号，`QEvent::RequestSoftwareInputPanel`和`QEvent::CloseSoftwareInputPanel`，以至于就算你安装了全局的事件过滤器也依然获取不到这两个信号，你只需要在`main`函数执行`app.setInputContext(nullptr)`即可，意思是设置输入法上下文为空。
- 理论上串口和网络收发数据默认都是异步的，操作系统自动调度，完全不会卡住界面，网上那些说收发数据卡住界面主线程的都是瞎说的，真正耗时的是运算以及运算后的处理，而不是收发数据。在一些运算数据量很小的项目中，一般不建议动用线程去处理，线程调度是有开销的，不要什么东西都往线程里边扔，线程不是万能的。只有当真正需要进行一些很耗时的操作（比如多媒体编解码等）时，才需要移到线程处理。
- 数据库处理一般建议在主线程中进行，如果非要在其他线程中操作，务必记得打开数据库也要在那个线程，即在哪个线程使用数据库就在哪个线程打开它，而不能在主线程打开数据库，在子线程执行sql，这样很可能出问题。
- 新版的`QTcpServer`类在64位版本的Qt下很可能不会进入`incomingConnection`函数，那是因为Qt5对应的`incomingConnection`函数的参数类型变了，由之前的`int`改成了`qintptr`。改成`qintptr`有个好处，那就是在32位上是`quint32`而在64位上是`quint64`。如果在Qt5中继续把参数当`int`，在32位上不会出问题，但在64位上肯定会出问题，所以为了兼容Qt4和Qt5，必须分开处理：

  ```cpp
  #if (QT_VERSION >= QT_VERSION_CHECK(5, 0, 0))
      void incomingConnection(qintptr handle);
  #else
      void incomingConnection(int handle);
  #endif
  ```

- 判断两个浮点数是否相等时，尽量使用Qt提供的`qFuzzyCompare`函数（头文件：`QtGlobal`），但不能对`NaN`（Not a Number，可以使用`qIsNaN`函数进行判断）或者无穷（可以使用`qIsInf`函数进行判断）这两类数字进行判断。注意不要用这个函数和常数`0`/`0.0`比较，这种情况下要改用`qFuzzyIsNull`函数。
- 部分不常用的宏

  | 宏 | 作用 | 示例 |
  | -- | -- | -- |
  | `Q_LIKELY(expr)` | 告知编译器，表达式`expr`的计算结果很有可能（但不一定）为`true`。此宏被用来改善编译器优化，但编译器不一定都接受此提示。 | - |
  | `Q_UNLIKELY(expr)` | 告知编译器，表达式`expr`的计算结果很有可能（但不一定）为`false`。此宏被用来改善编译器优化，但编译器不一定都接受此提示。 | - |
  | `Q_UNREACHABLE` | 告知编译器，当前位置是任何情况下都不可能到达的（例如`switch`语句的某些分支）。 | - |
  | `Q_UNUSED(name)` | 告知编译器，参数`name`没有被使用。此宏被用来消除某些编译器警告。 | `Q_UNUSED(argc)` |
  | `Q_FOREVER` | 无限循环，等价于`for(;;)` | `Q_FOREVER { qDebug() << "*"; }` |
  | `Q_ASSUME(expr)` | 告知编译器，表达式`expr`的计算结果一定为`true`。但当表达式`expr`的计算结果为`false`时，此宏的效果与`Q_UNREACHABLE`相同。此宏被用来改善编译器优化，但编译器不一定都接受此提示。 | - |

- 如何像微软的Office套件那样，用户关机时暂时中断系统的关机进程，提示用户保存未保存的文档，处理完毕后再继续关机？

  ```cpp
  MyMainWidget::MyMainWidget(QWidget *parent) : QWidget(parent) {
      QGuiApplication::setFallbackSessionManagementEnabled(false);
      connect(qApp, &QGuiApplication::commitDataRequest, this, &MyMainWidget::commitData);
  }

  void MyMainWidget::commitData(QSessionManager& manager) {
      if (manager.allowsInteraction()) {
          // 程序拥有与用户进行交互的权限
          const int ret = QMessageBox::warning(mainWindow, tr("My Application"), tr("Save changes to document?"), QMessageBox::Save | QMessageBox::Discard | QMessageBox::Cancel);
          switch (ret) {
          case QMessageBox::Save:
              manager.release();
              if (!saveDocument()) {
                  manager.cancel();
              }
              break;
          case QMessageBox::Discard:
              break;
          case QMessageBox::Cancel:
          default:
              manager.cancel();
          }
      } else {
          // 没有与用户交互的权限，做一些其他合理的事情
      }
  }
  ```

  注：更多更详细的用法请自行查看Qt文档中关于`QSessionManager`的部分。
- Qt支持将`QPushButton`和`QLineEdit`等控件自动关联与它们相对应的`on_控件名_信号(参数)`这一类的槽函数，比如按钮*openButton*的点击信号会自动关联槽函数`on_openButton_clicked()`（如果找得到这个函数的实现的话）。
- 如何获取程序正在使用的OpenGL类型（OpenGL或OpenGL ES）、版本、格式、制造商（英特尔、英伟达等）等信息：

  ```cpp
  void dumpGlInfo(QTextStream &str, bool listExtensions) {
      QOpenGLContext context;
      if (context.create()) {
  #ifdef QT_OPENGL_DYNAMIC
          str << "Dynamic GL ";
  #endif
          switch (context.openGLModuleType()) {
          case QOpenGLContext::LibGL:
              str << "LibGL";
              break;
          case QOpenGLContext::LibGLES:
              str << "LibGLES";
              break;
          }
          QWindow window;
          window.setSurfaceType(QSurface::OpenGLSurface);
          window.create();
          context.makeCurrent(&window);
          QOpenGLFunctions functions(&context);
          str << " Vendor: " << reinterpret_cast<const char *>(functions.glGetString(GL_VENDOR))
              << "\nRenderer: " << reinterpret_cast<const char *>(functions.glGetString(GL_RENDERER))
              << "\nVersion: " << reinterpret_cast<const char *>(functions.glGetString(GL_VERSION))
              << "\nShading language: " << reinterpret_cast<const char *>(functions.glGetString(GL_SHADING_LANGUAGE_VERSION))
              <<  "\nFormat: " << context.format();
  #ifndef QT_OPENGL_ES_2
          GLint majorVersion;
          functions.glGetIntegerv(GL_MAJOR_VERSION, &majorVersion);
          GLint minorVersion;
          functions.glGetIntegerv(GL_MINOR_VERSION, &minorVersion);
          const QByteArray openGlVersionFunctionsName = "QOpenGLFunctions_" + QByteArray::number(majorVersion) + '_' + QByteArray::number(minorVersion);
          str << "\nProfile: None (" << openGlVersionFunctionsName << ')';
          if (majorVersion > 3 || (majorVersion == 3 && minorVersion >= 1)) {
              QOpenGLVersionProfile profile;
              profile.setVersion(majorVersion, minorVersion);
              profile.setProfile(QSurfaceFormat::CoreProfile);
              if (QAbstractOpenGLFunctions *f = context.versionFunctions(profile)) {
                  if (f->initializeOpenGLFunctions())
                      str << ", Core (" << openGlVersionFunctionsName << "_Core)";
              }
              profile.setProfile(QSurfaceFormat::CompatibilityProfile);
              if (QAbstractOpenGLFunctions *f = context.versionFunctions(profile)) {
                  if (f->initializeOpenGLFunctions())
                      str << ", Compatibility (" << openGlVersionFunctionsName << "_Compatibility)";
              }
          }
          str << '\n';
  #endif // !QT_OPENGL_ES_2
          if (listExtensions) {
              QByteArrayList extensionList = context.extensions().values();
              std::sort(extensionList.begin(), extensionList.end());
              str << " \nFound " << extensionList.size() << " extensions:\n";
              for (const QByteArray &extension : qAsConst(extensionList))
                  str << "  " << extension << '\n';
          }
      } else {
          str << "Unable to create an Open GL context.\n";
      }
  }
  ```

  注：摘自Qt工具[qtdiag](https://code.qt.io/cgit/qt/qttools.git/tree/src/qtdiag/qtdiag.cpp)的源码。
- 将文件（夹）移动到回收站/废纸篓，而不是彻底删除：`QFile::moveToTrash`，此API于Qt **5.15**引入。
  - 方法1

    ```cpp
    QFile file;
    file.setFileName("readme.txt");
    if (file.moveToTrash()) {
        // 支持获取文件在回收站/废纸篓中位置的系统，fileName()会返回其在回收站/废纸篓的路径
        qDebug() << "File has been moved to" << file.fileName();
    } else {
        // 删除失败
    }
    ```

  - 方法2

    ```cpp
    // 如果想获取被删除文件在回收站/废纸篓中的路径，请将一个QString传入第二个参数
    if (!QFile::moveToTrash("D:/test.dat")) {
        qDebug() << "Failed to ...";
    }
    ```

- 开发Qt Quick插件时，如何使Qt Creator支持语法高亮

  使用`qmlplugindump`工具生成`plugins.qmltypes`文件，将其放在与库文件同级的目录中。

  同时将这个文件写入`qmldir`文件中：`typeinfo plugins.qmltypes`。

  注：Qt 5.15 引入了一个新的生成该文件的方式：在.pro文件中写入`CONFIG += qmltypes`即可，但仅支持QMake，CMake的支持要等到Qt 6才有。
- 开发Qt Quick插件时，如何将qml文件编译到库文件中，而不是直接暴露在外部
  - 将qml文件添加到qrc文件中，使其编译到最终的库文件中
  - 在C++中注册

    ```cpp
    qmlRegisterType(QUrl("qrc:/MySlider.qml"), "com.mycompany.myqmlcomponents", 1, 0, "Slider");
    ```

  作用：
  - 保护知识产权
  - 防止恶意篡改
- 如何修改Qt WebEngine编译时的参数：直接编辑`qtwebengine\src\3rdparty\chromium\build\config\compiler\BUILD.gn`
- Qt可以在编译前的配置时通过`-qtnamespace <namespace>`将所有Qt函数和类都移入一个指定的命名空间（默认是没有的，这个功能只有特殊场景才需要），因此如果你对Qt的类使用了前置声明，一定要用`QT_BEGIN_NAMESPACE`和`QT_END_NAMESPACE`这两个宏进行包裹，否则在这种带命名空间的Qt上无法编译通过。

  ```cpp
  QT_BEGIN_NAMESPACE
  class QWindow;
  class QWidget;
  QT_END_NAMESPACE
  ```

- `QObject`是一个非常重型的类，初始化较慢，且实例化后占用内存较多，如果只是做一些简单的工作或完全不需要Qt的元对象系统，就不要继承自该类。虽然是在编写Qt程序，但一定不要无脑派生该类。
- 善用`qScopeGuard`来执行各种“清理”操作。`QScopeGuard`：在当前代码块的生命周期结束前，执行指定的代码，但不可抛出异常。

  ```cpp
  void myComplexCodeWithMultipleReturnPoints(int v) {
      // The lambda will be executed right before your function returns
      auto cleanup = qScopeGuard([]() { /* code you want executed goes HERE */ });
      if (v == -1)
          return;
      int v2 = code_that_might_throw_exceptions();
      if (v2 == -1)
          return;
      // (...)
  }
  ```

- `QWidget`无法在构造函数里获取其所在`QWindow`的指针，如果有什么工作必须要在窗口显示出来之前进行，可以在这个Widget的`showEvent`中操作，在这个事件里`QWindow`是能被获取到的。

  ```cpp
  void FramelessWidget::showEvent(QShowEvent *event) {
      QWidget::showEvent(event);
      // windowHandle() will always return nullptr before the QWidget itself is
      // shown. So we can't do this in the constructor function, it will result in
      // a crash.
      const QWindow *win = windowHandle();
      if (win) {
          const auto hwnd = reinterpret_cast<HWND>(windowHandle()->winId());
          if (hwnd) {
              const DWMNCRENDERINGPOLICY ncrp = DWMNCRP_ENABLED;
              if (FAILED(DwmSetWindowAttribute(hwnd, DWMWA_NCRENDERING_POLICY,
                                               &ncrp, sizeof(ncrp)))) {
                  qDebug() << "Failed to enable non-client area rendering.";
                  return;
              }
              MARGINS margins = {0, 0, 0, 0};
              if (FAILED(DwmExtendFrameIntoClientArea(hwnd, &margins))) {
                  qDebug() << "Failed to reset window frame.";
                  return;
              }
              margins = {-1, -1, -1, -1};
              if (FAILED(DwmExtendFrameIntoClientArea(hwnd, &margins))) {
                  qDebug() << "Failed to bring frame shadow back.";
                  return;
              }
          }
      }
  }
  ```

- 如果要保存某个`QObject`或其派生类的指针，不涉及指针所指向的对象的所有权，建议使用`QPointer`，因为它会在对象销毁后自动重置为空指针。其用法与正常指针一样，因此基本可以无缝迁移。

  ```cpp
  // 声明。就当成一个普通的指针。
  QPointer<QWidget> mywidget = nullptr;
  // 赋值。直接把指针传过去就行。
  mywidget = ui->pushButton_ok;
  // 使用。对象被销毁后QPointer会自动重置为空指针
  if (mywidget) {
      mywidget->show();
  }
  // QPointer只会保存指针，不会转移其所有权，因此就算QPointer本身被销毁了，它所保存的指针也不会受到任何影响。即QPointer本身不会对其所保存的指针进行任何操作，与智能指针是完全不同的东西。
  ```

  注：`QPointer`只支持`QObject`及其派生类。
- 默认情况下控件获取焦点以后会有虚边框，如果看着觉得碍眼不舒服可以去掉，设置样式即可：`setStyleSheet("*{outline:0px;}");`
- Qt表格控件一些常用的设置封装，`QTableWidget`继承自`QTableView`，所以下面这个函数支持传入`QTableWidget`。

  ```cpp
  void QUIHelper::initTableView(QTableView *tableView, int rowHeight, bool headVisible, bool edit) {
      //奇数偶数行颜色交替
      tableView->setAlternatingRowColors(false);
      //垂直表头是否可见
      tableView->verticalHeader()->setVisible(headVisible);
      //选中一行表头是否加粗
      tableView->horizontalHeader()->setHighlightSections(false);
      //最后一行拉伸填充
      tableView->horizontalHeader()->setStretchLastSection(true);
      //行标题最小宽度尺寸
      tableView->horizontalHeader()->setMinimumSectionSize(0);
      //行标题最大高度
      tableView->horizontalHeader()->setMaximumHeight(rowHeight);
      //默认行高
      tableView->verticalHeader()->setDefaultSectionSize(rowHeight);
      //选中时一行整体选中
      tableView->setSelectionBehavior(QAbstractItemView::SelectRows);
      //只允许选择单个
      tableView->setSelectionMode(QAbstractItemView::SingleSelection);
      //表头不可单击
      tableView->horizontalHeader()->setSectionsClickable(false);
      //鼠标按下即进入编辑模式
      if (edit) {
          tableView->setEditTriggers(QAbstractItemView::CurrentChanged | QAbstractItemView::DoubleClicked);
      } else {
          tableView->setEditTriggers(QAbstractItemView::NoEditTriggers);
      }
  }
  ```

- Qt日志输出技巧：使用`QLoggingCategory`

  使用`QLoggingCategory`可以做到输出日志时连同日志分类一起输出（如果日志格式没有被更改），还能使用一定的规则对日志消息进行过滤。如果不使用这个类，单纯使用`qDebug`等函数进行输出，最大的缺点就是无法对日志消息进行过滤，要输出就全都输出，要屏蔽就全都屏蔽，不能针对某一类消息进行过滤。

  - 声明

    ```cpp
    // 在头文件中
    #include <QLoggingCategory>
    // 使用Q_DECLARE_LOGGING_CATEGORY这个宏来声明一个日志分类
    // 这个宏只能在头文件里使用，不要在cpp文件里用
    // 这个宏的参数是你日志分类的名称，每个分类都要这样声明一次，而且不要重复声明
    // 这个分类名称是内部使用的，不会对外展示，所以叫什么名字都可以，但不要以qt开头，而且不要与C++和Qt的关键字重名，最好是纯字母，连数字和下划线都不要有
    Q_DECLARE_LOGGING_CATEGORY(lcMyLoggingCategory)
    // -----------------------------------------------
    // 在源文件中
    // 使用Q_LOGGING_CATEGORY这个宏来完善这个日志分类的声明
    // 这个宏最好不要在头文件里用，没有特殊理由就在cpp文件里用
    // 这个宏的第一个参数就是你在头文件里声明的分类名，这里的名字一定要和头文件里的那个完全相同，保险起见可以直接复制粘贴过来
    // 这个宏的第二个参数是对应日志分类的具体的名字，用于向用户展示，过滤消息时用的也是这个名字，所以这个名字一定要有意义，不能随便起
    Q_LOGGING_CATEGORY(lcMyLoggingCategory, "myapp.myloggingcategory")
    ```

  - 使用

    ```cpp
    // 用起来和以前唯一的区别就是多了个“C”和分类名：qInfo->qCInfo(分类名), qDebug->qCDebug(分类名)等等
    qCInfo(lcMyLoggingCategory) << "my message.";
    qCDebug(lcMyLoggingCategory) << "my message.";
    qCWarning(lcMyLoggingCategory) << "my message.";
    qCCritical(lcMyLoggingCategory) << "my message.";
    ```

  - 过滤

    有三种方式对日志消息进行过滤：C++函数、INI配置文件和环境变量。通过调用静态函数`QLoggingCategory::setFilterRules()`可以设置过滤规则；INI配置文件位于`[QLibraryInfo::DataPath]/qtlogging.ini`和`[QStandardPaths::GenericConfigLocation]/QtProject/qtlogging.ini`（后者优先级高于前者），文件的内容就是日志的过滤规则，和你使用函数时传递的参数一样，Qt程序会在启动时自动加载这个文件，你在这个文件里设置的规则会自动生效，如果你将规则写入到别的地方了，就只能手动加载了；环境变量的名字是`QT_LOGGING_RULES`，内容也是过滤规则。这三个方式的优先级是环境变量>C++ API>INI文件。如果你想知道你的程序是从哪里加载的规则，设置`QT_LOGGING_DEBUG`这个环境变量就能看到了。

    我这里只是简略的提了下，还有很多详细的内容没写，具体用法请自行查阅Qt手册。
- 如何使`qDebug`等函数支持我自己的数据类型？

  ```cpp
  #ifndef QT_NO_DEBUG_STREAM
  QDebug operator<<(QDebug d, const MdkObject::Chapters &chapters) {
      QDebugStateSaver saver(d);
      d.nospace();
      d.noquote();
      QString chaptersStr = QString();
      for (auto &&chapter : qAsConst(chapters)) {
          chaptersStr.append(
              QString::fromUtf8("(title: %1, beginTime: %2, endTime: %3)")
                  .arg(chapter.title, QString::number(chapter.beginTime),
                       QString::number(chapter.endTime)));
      }
      d << "QVector(" << chaptersStr << ')';
      return d;
  }
  #endif
  ```

  注意事项：
  - Qt支持编译时去掉日志功能以减小二进制文件的大小和提高程序的性能，所以要先用`QT_NO_DEBUG_STREAM`这个宏判断一下，否则这个代码在这种配置的Qt上会无法编译
  - `QDebugStateSaver`是用于在其对象销毁时还原`QDebug`的设置，如果你没有修改`QDebug`的格式，是不需要这个的，但如果不放心的话可以无脑带上，反正没坏处
  - 重载了`QDebug`的`<<`运算符就足够了，`qInfo`、`qWarning`和`qCritical`也都能用
- 连接信号和槽函数而槽函数又是一个匿名函数时，有无槽函数父对象指针的区别

  ```cpp
  // QObject::connect函数
  connect(ui->pushButton, &QPushButton::clicked, this, [](){});
  connect(ui->pushButton, &QPushButton::clicked, [](){});
  // QTimer::singleShot函数
  QTimer::singleShot(50, this, [](){});
  QTimer::singleShot(50, [](){});
  ```

  从上面的代码可以明显看出，两个连接方式唯一的区别就是有没有槽函数父对象的指针，当然此处的槽函数是我们自己写的匿名函数。在槽函数的执行上，这两种写法没有任何区别，但对于指定了槽函数父对象的方式，Qt会在调用槽函数前首先检查其父对象是否已经销毁，如果已经销毁，会自动执行`disconnect`并忽略槽函数的执行。如果没有指定槽函数的父对象，会无视其父对象的生命周期，只要触发相关信号就会调用所绑定的槽函数。
