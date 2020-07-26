# CMake

## 常用内部变量

| 变量 | 作用 | 示例 |
| --- | ---- | ---- |
| CMAKE_C_STANDARD | C标准，可取的值为`90`，`99`和`11` | set(CMAKE_C_STANDARD 11) |
| CMAKE_C_EXTENSIONS | 是否启用C语言编译器扩展 | set(CMAKE_C_EXTENSIONS OFF) |
| CMAKE_CXX_STANDARD | C++标准，可取的值为`98`，`11`，`14`，`17`和`20` | set(CMAKE_CXX_STANDARD 14) |
| CMAKE_CXX_EXTENSIONS | 是否启用C++语言编译器扩展 | set(CMAKE_CXX_EXTENSIONS OFF) |
| CMAKE_C_COMPILER | C编译器（除非在环境变量中，否则要写具体的路径） | set(CMAKE_C_COMPILER clang) |
| CMAKE_CXX_COMPILER | C++编译器（除非在环境变量中，否则要写具体的路径） | set(CMAKE_CXX_COMPILER clang++) |
| CMAKE_LINKER | 链接器（除非在环境变量中，否则要写具体的路径） | set(CMAKE_LINKER ld.lld) |
| CMAKE_C_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | 编译C文件时的选项，其中，不带后缀的为公共选项，会添加到其他所有选项的后边 | - |
| CMAKE_CXX_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | 编译C++文件时的选项，其中，不带后缀的为公共选项，会添加到其他所有选项的后边 | - |
| CMAKE_BUILD_TYPE | 构建类型，分为`Debug`、`MinSizeRel`、`Release`和`RelWithDebInfo` | set(CMAKE_BUILD_TYPE Release) |
| BUILD_SHARED_LIBS | 设置库默认构建类型是否为动态库 | set(BUILD_SHARED_LIBS OFF) |
| CMAKE_SOURCE_DIR,PROJECT_SOURCE_DIR | 项目顶级源码目录，即项目根目录 | - |
| CMAKE_BINARY_DIR,PROJECT_BINARY_DIR | 项目顶级构建目录，用来存放CMake生成的各种脚本、缓存文件和所有编译过程中生成的文件。如果当前项目存在多个target，CMake会在顶级构建目录中分别创建每个target对应的构建目录 | - |
| CMAKE_INSTALL_PREFIX | 项目安装路径前缀 | set(CMAKE_INSTALL_PREFIX /usr) |
| CMAKE_EXE_LINKER_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | 可执行程序链接选项，其中，不带后缀的为公共选项，会添加到其他所有选项的后边 | - |
| CMAKE_MODULE_LINKER_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | 模块？链接选项，其中，不带后缀的为公共选项，会添加到其他所有选项的后边 | - |
| CMAKE_SHARED_LINKER_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | 动态库链接选项，其中，不带后缀的为公共选项，会添加到其他所有选项的后边 | - |
| CMAKE_STATIC_LINKER_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | 静态库链接选项，其中，不带后缀的为公共选项，会添加到其他所有选项的后边 | - |
| CMAKE_RUNTIME_OUTPUT_DIRECTORY[_DEBUG/_MINSIZEREL/_RELEASE] | 可执行程序输出路径 | set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin) |
| CMAKE_LIBRARY_OUTPUT_DIRECTORY[_DEBUG/_MINSIZEREL/_RELEASE] | 动态链接库（.dll/.so）输出路径 | set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin) |
| CMAKE_ARCHIVE_OUTPUT_DIRECTORY[_DEBUG/_MINSIZEREL/_RELEASE] | 静态链接库（.lib/.a）输出路径 | set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib) |
| CMAKE_GENERATOR | 生成器名称（cmake参数：-G） | set(CMAKE_GENERATOR Ninja) |
| CMAKE_GENERATOR_PLATFORM | 生成器架构（cmake参数：-A） | set(CMAKE_GENERATOR_PLATFORM Win32) |
| CMAKE_GENERATOR_TOOLSET | 生成器工具链（cmake参数：-T） | set(CMAKE_GENERATOR_TOOLSET x64) |
| CMAKE_CURRENT_SOURCE_DIR | 当前target的源码目录（不一定是CMakeLists.txt所在的目录） | - |
| CMAKE_CURRENT_BINARY_DIR | 当前target的构建目录 | - |
| CMAKE_CURRENT_LIST_DIR | 当前CMakeLists.txt所在的目录 | - |
| CMAKE_CURRENT_LIST_FILE | 当前CMakeLists.txt的完整路径 | - |
| CMAKE_PROJECT_NAME,PROJECT_NAME | 项目名 | - |
| CMAKE_PROJECT_VERSION,PROJECT_VERSION | 项目完整版本号 | - |
| CMAKE_PROJECT_VERSION_MAJOR,PROJECT_VERSION_MAJOR | 项目主版本号 | - |
| CMAKE_PROJECT_VERSION_MINOR,PROJECT_VERSION_MINOR | 项目次版本号 | - |
| CMAKE_PROJECT_VERSION_PATCH,PROJECT_VERSION_PATCH | 项目修订版本号 | - |
| CMAKE_PROJECT_VERSION_TWEAK,PROJECT_VERSION_TWEAK | WIN32程序专用：项目第四位版本号 | - |
| CMAKE_SYSTEM | 系统名称（带版本号） | - |
| CAMKE_SYSTEM_NAME | 系统名称（不带版本号） | - |
| CMAKE_SYSTEM_VERSION | 系统版本 | - |
| CMAKE_SYSTEM_PROCESSOR | 处理器名称，如`i686` | - |
| CMAKE_DEBUG_POSTFIX | 调试版本库文件后缀 | set(CMAKE_DEBUG_POSTFIX "_debug") |
| CMAKE_MINSIZEREL_POSTFIX | 为大小优化版本库文件后缀 | set(CMAKE_MINSIZEREL_POSTFIX "_minsizerel") |
| CMAKE_RELEASE_POSTFIX | 为速度优化版本库文件后缀 | set(CMAKE_RELEASE_POSTFIX "_release") |
| CMAKE_RELWITHDEBINFO_POSTFIX | 发布版本（带调试符号）库文件后缀 | set(CMAKE_RELWITHDEBINFO_POSTFIX "_relwithdebinfo") |
| CMAKE_INTERPROCEDURAL_OPTIMIZATION | 是否开启*链接时间代码生成*（*LTCG*） | set(CMAKE_INTERPROCEDURAL_OPTIMIZATION ON) |
| CMAKE_MSVC_RUNTIME_LIBRARY | 设置MSVC运行时（仅限MSVC/clang-cl/intel-cl），可选值为`MultiThreaded`（即`MT`）、`MultiThreadedDLL`（即`MD`）、`MultiThreadedDebug`（即`MTd`）以及`MultiThreadedDebugDLL`（即`MDd`）。如果将此值设置为空，则CMake不会主动添加任何参数。 | set(CMAKE_MSVC_RUNTIME_LIBRARY MultiThreaded) |

注：

1. 如何设置这些变量：除了使用`set`命令，也可以在cmake命令中使用，如`cmake -DCMAKE_BUILD_TYPE=Release`，此举可覆盖CMakeLists.txt中的设置
2. 如何获取这些变量：使用`${变量名}`的方式获取，例如`${CMAKE_CXX_COMPILER}`可以获取到默认的C++编译器

## 常用命令

| 命令 | 作用 | 示例 |
| --- | ---- | ---- |
| **cmake_minimum_required**(VERSION 版本号) | 指定cmake的最低版本，这个选项也影响着cmake使用的各种策略 | cmake_minimum_required(VERSION 3.12.0 FATAL_ERROR) |
| **project**(项目名 [VERSION 版本号]) | 指定项目名称。`${项目名_SOURCE_DIR}`表示项目根目录，也可以用`${PROJECT_SOURCE_DIR}`。如果给定了版本号，cmake将会自动添加`PROJECT_VERSION`，`PROJECT_VERSION_MAJOR`，`PROJECT_VERSION_MINOR`，`PROJECT_VERSION_PATCH`，`PROJECT_VERSION_TWEAK`和`CMAKE_PROJECT_VERSION`的定义 | project(demo VERSION 1.2.3) |
| **include_directories**(路径1 路径2 ...) | 添加头文件的搜索路径，相当于`-I`参数。此为全局指令，针对不同的目标，可以使用`target_include_directories` | - |
| **link_directories**(路径) | 添加库的搜索路径，相当于`-L`参数。此为全局指令，针对不同的目标，可以使用`target_link_directories` | - |
| **add_subdirectory**(路径) | 包含子目录。这个子目录的顶层也必须有CMakeLists.txt文件 | - |
| **add_executable**(目标名 [WIN32] [MACOSX_BUNDLE] 源文件1 源文件2 ...) | 编译可执行程序。如果给定`WIN32`选项，程序必须使用`(w)WinMain`作为入口点。 | - |
| **link_libraries**(目标名 链接库1 链接库2 ...) | 添加链接库，相当于`-l`参数。此为全局指令，针对不同的目标，可以使用`target_link_libraries` | - |
| **add_library**(库名 [STATIC\|SHARED] 源文件1 源文件2 ...) | 编译库，默认为静态库 | - |
| **find_package**(包名 [版本] [COMPONENTS 组件1 组件2 ...] [CONFIG] [REQUIRED]) | 查找包。如果指定了`REQUIRED`，会在找不到包时报错并中断编译。如果找到了对应的包，会自动添加`包名_FOUND`的宏定义 | find_package(Qt5 COMPONENTS Core Gui Widgets CONFIG REQUIRED) |
| **message**([模式] "待输出信息") | 输出信息。模式可取的值为`STATUS`，`WARNING`，`AUTHOR_WARNING`，`SEND_ERROR`，`FATAL_ERROR`，`DEPRECATION` | - |
| **option**(变量名 "帮助文本" [默认值]) | 给用户提供一个可以选择`OFF/ON`的选项，默认值为`OFF` | - |
| **configure_file**(输入文件 输出文件 [@ONLY]) | 根据输入文件输出一个新的文件，其中的`@变量名@`会自动被替换为cmake中变量的值。建议使用`#cmakedefine`代替`#define`。`@ONLY`参数可以使cmake跳过输入文件中`${变量名}`这种变量的替换，防止破坏原来的文件 | - |
| **include**(路径) | 包含一个外部的cmake脚本 | - |
| **add_compile_definitions**(定义1 定义2 ...) | 添加预处理器定义。此为全局指令，针对不同的目标，可以使用`target_compile_definitions` | - |
| **add_compile_options**(选项1 选项2 ...) | 添加编译选项。此为全局指令，针对不同的目标，可以使用`target_compile_options` | - |
| **add_link_options**(选项1 选项2 ...) | 添加链接选项。此为全局指令，针对不同的目标，可以使用`target_link_options` | - |
| **install**(类型 路径 [CONFIGURATIONS Debug\|Release] 路径) | 安装文件和文件夹，即将指定的文件及文件夹复制到指定的位置。类型可取的值为`TARGETS`，`FILES`和`DIRECTORY`（常用），其中`TARGETS`用于脚本中已经存在的target，其后的路径可以直接用target的名字，`FILES`即一般文件，`DIRECTORY`为文件夹。 | - |

## 杂项

- cmake支持的编译器的名称为`AppleClang`，`Clang`，`GNU`，`MSVC`，`SunPro`和`Intel`，可以通过`CMAKE_CXX_COMPILER_ID`这个变量获取到，并可以通过`STREQUAL`这个函数来进行字符串的比较，**大小写敏感**。编译器版本号可以通过`CMAKE_CXX_COMPILER_VERSION`这个变量获取到，可以使用`VERSION_LESS`和`VERSION_GREATER`来比较版本号。示例：

   ```cmake
   if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
       if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS 4.8.0)
           message(FATAL_ERROR "GCC 4.8 or higher required!")
       endif()
   elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
       if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS 3.3.0)
           message(FATAL_ERROR "Clang 3.3 or higher required!")
       endif()
   endif()
   ```

- 为 VS 生成：`cmake -G"Visual Studio 16 2019" -AWin32/x64 [-Thost=x86/x64]`
- 交叉编译（以Clang为例）：

   ```cmake
   set(CMAKE_SYSTEM_NAME Windows) #设置目标系统
   set(CMAKE_SYSTEM_PROCESSOR x86_64) #设置目标架构
   set(triple ${CMAKE_SYSTEM_PROCESSOR}-pc-windows-gnu) #设置目标triple
   set(CMAKE_C_COMPILER clang) #设置C编译器
   set(CMAKE_C_COMPILER_TARGET ${triple}) #设置C编译器triple
   set(CMAKE_CXX_COMPILER clang++) #设置C++编译器
   set(CMAKE_CXX_COMPILER_TARGET ${triple}) #设置C++编译器triple
   ```

- 编译Qt程序：

   ```cmake
   cmake_minimum_required(VERSION 3.12.0 FATAL_ERROR)
   project(qtdemo VERSION 1.2.3 LANGUAGES CXX)
   set(CMAKE_INCLUDE_CURRENT_DIR ON)
   set(CMAKE_AUTOMOC ON)
   set(CMAKE_AUTOUIC ON)
   set(CMAKE_AUTORCC ON)
   find_package(Qt5 COMPONENTS Core Gui Widgets REQUIRED)
   set(HEADERS
       mainwindow.h
   )
   set(SOURCES
       mainwindow.cpp
       main.cpp
   )
   set(RESOURCES
       demo.qrc
   )
   set(FORMS
       mainwindow.ui
   )
   add_executable(demo WIN32 ${HEADERS} ${SOURCES} ${FORMS} ${RESOURCES}) #不是Windows GUI程序就不要加WIN32，因为加了WIN32会导致CMake链接WinMain函数
   target_link_libraries(demo PRIVATE Qt5::Core Qt5::Gui Qt5::Widgets)
   install(TARGETS demo DESTINATION bin) #可以使用“CMAKE_INSTALL_PREFIX”改变安装路径
   install(DIRECTORY ${PROJECT_SOURCE_DIR}/themes DESTINATION bin/themes)
   install(FILES ${PROJECT_SOURCE_DIR}/i18n/*.qm DESTINATION bin/translations)
   ```

   注：如果想使用自己构建的Qt套件，可以使用`CMAKE_PREFIX_PATH`指定Qt套件的路径，例如：`cmake -DCMAKE_PREFIX_PATH=~/qt/clang64 ..`
- CMake中支持的生成器的名称为`Borland Makefiles`，`MSYS Makefiles`，`MinGW Makefiles`，`NMake Makefiles`，`NMake Makefiles JOM`，`Unix Makefiles`，`Watcom WMake`，`Ninja`，`Visual Studio 10 2010`，`Visual Studio 11 2012`，`Visual Studio 12 2013`，`Visual Studio 14 2015`，`Visual Studio 15 2017`，`Visual Studio 16 2019`，`Green Hills MULTI`和`Xcode`等
- CMake支持的架构的名称为`Win32`，`x64`，`ARM`和`ARM64`等（以VS为例）
- 如何判断平台：

   ```cmake
   #方法一：
   message("Current operating system is ${CMAKE_SYSTEM}")
   if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
       message("Current platform: Linux")
   elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")
       message("Current platform: Windows")
   elseif(CMAKE_SYSTEM_NAME STREQUAL "FreeBSD")
       message("Current platform: FreeBSD")
   else()
       message("Other platform: ${CMAKE_SYSTEM_NAME}")
   endif()
   #方法二：
   if(WIN32)
       message("Now is Windows.")
   elseif(APPLE)
       message("Now is macOS.")
   elseif(UNIX)
       message("Now is UNIX-like OS.")
   endif()
   #其他CMake预定义的宏还有WINDOWS_STORE、MINGW、CYGWIN、ANDROID和IOS等
   ```

- 如何为调试版本和发布版本的可执行程序设置不同后缀：

   ```cmake
   # 设置 CMAKE_DEBUG_POSTFIX 或 CMAKE_RELEASE_POSTFIX 也可，这样就不用为每个target都设置后缀了
   set_target_properties(${TARGET_NAME} PROPERTIES DEBUG_POSTFIX "_d")
   set_target_properties(${TARGET_NAME} PROPERTIES RELEASE_POSTFIX "_r")
   ```

- 注释：
  - 单行注释：`#`开头
  - 多行注释：使用`#[[`开头，`]]`结尾
- Qt添加翻译文件：

  ```cmake
  find_package(Qt5 COMPONENTS LinguistTools)
  set(CMAKE_AUTOMOC ON)
  set(CMAKE_AUTOUIC ON)
  set(CMAKE_AUTORCC ON)
  # 如果CMake找到了Qt Linguist，会自动添加Qt5LinguistTools_FOUND这个宏
  if(Qt5LinguistTools_FOUND)
  # qt5_add_translation：只运行lrelease工具，要求ts文件已经存在（不会自动创建）且不能给lrelease传参数
  # qt5_add_translation(QM_FILES ts文件（可以不止一个）)
  # qt5_create_translation：如果ts文件不存在会自动创建，如果存在就更新。源文件可以是.ui或.cpp或其他任何lupdate支持的格式
  # qt5_create_translation(QM_FILES 待翻译的源文件或源文件所在的文件夹（可以不止一个） ts文件（可以不止一个） [OPTIONS 可选项，要传给lupdate工具的命令行参数])
  qt5_create_translation(QM_FILES ${CMAKE_SOURCE_DIR} english.ts french.ts)
  # qm文件是在构建目录生成的，但qrc文件里是相对路径，因此要将qrc文件复制到构建目录中（也可以在qrc文件中使用绝对路径，使用CMake变量替换）
  configure_file(translations.qrc ${CMAKE_BINARY_DIR} COPYONLY)
  # 这里把${QM_FILES}添加为可执行程序的依赖项，可以在翻译更新后强制CMake重新构建
  add_executable(demo main.cpp ${CMAKE_BINARY_DIR}/translations.qrc ${QM_FILES})
  target_link_libraries(demo Qt5::XXX)
  endif()
  ```

  注：根据 <https://bugreports.qt.io/browse/QTBUG-41736> ，qt5_create_translation这个宏会在make clean或rebuild时把全部ts文件都删掉后再重新生成，这意味着已经翻译好的文本会全部丢失，已有的解决方法也已经失效，而Qt官方也没有针对这个问题进行修复，因此不建议再使用这个宏了，还是手动生成ts文件再搭配qt5_add_translation比较保险。
- 如何通过CMake直接进行构建和安装过程（即让CMake自行调用设定的生成工具）：

  ```cmake
  # Configure
  cmake <path-to-your-top-level-source-code-dir>
  # Build
  cmake --build <path-to-your-project's-top-level-binary-dir>
  # Install
  cmake --install <path-to-your-project's-top-level-binary-dir>
  ```

- 显式指定CMake二进制文件夹和源码文件夹

  ```cmake
  cmake -S <path-to-top-level-source-code-dir> -B <path-to-top-level-binary-dir>
  ```

- 如何使CMake自动复制MSVC的运行时DLL到指定位置：

  ```cmake
  # 包含对应的CMake模块。注：此模块为MSVC专用。
  include(InstallRequiredSystemLibraries)
  # 设置为TRUE时会一同复制UCRT的DLL。非必需。
  set(CMAKE_INSTALL_UCRT_LIBRARIES TRUE)
  # 可以使用下面这个变量来设置自定义的Win10 SDK路径。非必需。
  set(CMAKE_WINDOWS_KITS_10_DIR "C:/Program Files (x86)/Windows Kits/10")
  # 设置为TRUE时会一同复制MFC的运行时DLL。非必需。
  set(CMAKE_INSTALL_MFC_LIBRARIES TRUE)
  # 设置为TRUE时会一同复制OpenMP的DLL。非必需。
  set(CMAKE_INSTALL_OPENMP_LIBRARIES TRUE)
  # 可以通过下面这个变量来设置要将所有DLL复制到什么地方，在Windows平台默认是bin，其他平台默认是lib。非必需。
  set(CMAKE_INSTALL_SYSTEM_RUNTIME_DESTINATION bin64)
  ```

- Qt程序编译完成后自动执行`win(mac)deployqt`工具

  ```cmake
  # 第一步，先获取win(mac)deployqt的路径
  find_package(Qt5 COMPONENTS Core REQUIRED)
  get_target_property(_qmake_executable Qt5::qmake IMPORTED_LOCATION)
  get_filename_component(_qt_bin_dir "${_qmake_executable}" DIRECTORY)
  find_program(DEPLOYQT_EXECUTABLE NAMES windeployqt macdeployqt HINTS "${_qt_bin_dir}")
  # 第二步
  if(WIN32)
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD COMMAND ${DEPLOYQT_EXECUTABLE} "$<TARGET_FILE:${PROJECT_NAME}>")
  elseif(APPLE)
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD COMMAND ${DEPLOYQT_EXECUTABLE} "$<TARGET_BUNDLE_DIR:${PROJECT_NAME}>")
  endif()
  ```

- 判断并启用*链接时间代码生成*/*链接时间优化*（即*LTCG*/*LTO*/*IPO*）

  ```cmake
  # CMake的早期版本（3.9以前）只支持ICC for Linux开启IPO，下面一行的作用是开启对其他编译器的兼容支持
  # cmake_policy(SET CMP0069 NEW)
  # 包含所需模块
  # include(CheckIPOSupported)
  # 方式一，如果编译器不支持IPO则直接报致命错误，中断配置进程：
  check_ipo_supported()
  set_property(TARGET foo PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
  # 方式二，编译器支持IPO才启用此特性，不支持时不会中断配置进程：
  check_ipo_supported(RESULT ipo_result OUTPUT error_message)
  if(ipo_result)
    set_property(TARGET foo PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
  else()
    message(WARNING "IPO is not supported: ${error_message}")
  endif()
  ```

  把CMake变量`CMAKE_INTERPROCEDURAL_OPTIMIZATION`设置为`ON`可以在全局范围内无条件启用IPO，但如果编译器不支持，会报错。
- 遍历文件夹下的所有源码文件：

  ```cmake
  file(GLOB_RECURSE HEADERS FOLLOW_SYMLINKS LIST_DIRECTORIES false CONFIGURE_DEPENDS *.h)
  file(GLOB_RECURSE SOURCES FOLLOW_SYMLINKS LIST_DIRECTORIES false CONFIGURE_DEPENDS *.cpp)
  file(GLOB_RECURSE FORMS FOLLOW_SYMLINKS LIST_DIRECTORIES false CONFIGURE_DEPENDS *.ui)
  ```

- 修改输出文件的文件名

  ```cmake
  # 不会影响 DEBUG_POSTFIX
  set_target_properties(mytarget PROPERTIES OUTPUT_NAME MyNewName)
  ```

- 如何为库附加包含目录（对外部使用者而言）

  ```cmake
  # 一定要 PUBLIC
  # ${CMAKE_CURRENT_LIST_DIR} 可以修改成你自己的路径
  target_include_directories(mytarget PUBLIC
      "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}>"
      "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/aaa>"
      "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/bbb>"
      "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}>/aa/bb"
  )
  ```
