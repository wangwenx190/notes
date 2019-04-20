# CMake
## 常用内部变量
| 变量 | 作用 | 示例 |
| --- | ---- | ---- |
| CMAKE_C_STANDARD | C标准，可取的值为`90`，`99`和`11` | set(CMAKE_C_STANDARD 11) |
| CMAKE_CXX_STANDARD | C++标准，可取的值为`98`，`11`，`14`，`17`和`20` | set(CMAKE_CXX_STANDARD 14) |
| CMAKE_C_COMPILER | C编译器（具体的程序名） | set(CMAKE_C_COMPILER clang) |
| CMAKE_CXX_COMPILER | C++编译器（具体的程序名） | set(CMAKE_CXX_COMPILER clang++) |
| CMAKE_LINKER | 链接器（具体的程序名） | set(CMAKE_LINKER ld.lld) |
| CMAKE_C_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | 编译C文件时的选项 | - |
| CMAKE_CXX_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | 编译C++文件时的选项 | - |
| CMAKE_BUILD_TYPE | 构建类型 | set(CMAKE_BUILD_TYPE Release) |
| BUILD_SHARED_LIBS | 更改库默认构建类型为动态库 | - |
| CMAKE_SOURCE_DIR,PROJECT_SOURCE_DIR | 项目顶级CMakeLists.txt所在目录，即项目根目录 | - |
| CMAKE_BINARY_DIR,PROJECT_BINARY_DIR | 项目构建目录，即build文件夹 | - |
| CMAKE_INSTALL_PREFIX | 项目安装路径前缀 | - |
| CMAKE_EXE_LINKER_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | exe链接选项 | - |
| CMAKE_MODULE_LINKER_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | - | - |
| CMAKE_SHARED_LINKER_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | - | - |
| CMAKE_STATIC_LINKER_FLAGS[_DEBUG/_MINSIZEREL/_RELEASE] | - | - |
| CMAKE_RUNTIME_OUTPUT_DIRECTORY[_DEBUG/_MINSIZEREL/_RELEASE] | 可执行程序输出路径 | set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin) |
| CMAKE_LIBRARY_OUTPUT_DIRECTORY[_DEBUG/_MINSIZEREL/_RELEASE] | 链接库（.dll/.so）输出路径 | set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin) |
| CMAKE_ARCHIVE_OUTPUT_DIRECTORY[_DEBUG/_MINSIZEREL/_RELEASE] | 链接库（.lib/.a）输出路径 | set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib) |
| CMAKE_GENERATOR | 生成器名称（cmake参数：-G） | set(CMAKE_GENERATOR Ninja) |
| CMAKE_GENERATOR_PLATFORM | 生成器架构（cmake参数：-A） | set(CMAKE_GENERATOR_PLATFORM Win32) |
| CMAKE_GENERATOR_TOOLSET | 生成器工具链（cmake参数：-T） | set(CMAKE_GENERATOR_TOOLSET x64) |
| CMAKE_CURRENT_SOURCE_DIR | 当前CMakeLists.txt所在目录 | - |
| CMAKE_CURRENT_BINARY_DIR | 当前target的构建目录 | - |
| CMAKE_CURRENT_LIST_FILE | 调用这个变量的CMakeLists.txt的完整路径 | - |
| PROJECT_NAME | 项目名 | - |
| CMAKE_SYSTEM | 系统名称（带版本号） | - |
| CAMKE_SYSTEM_NAME | 系统名称（不带版本号） | - |
| CMAKE_SYSTEM_VERSION | 系统版本 | - |
| CMAKE_SYSTEM_PROCESSOR | 处理器名称，如`i686` | - |
| CMAKE_DEBUG_POSTFIX | 调试版本库文件后缀 | set(CMAKE_DEBUG_POSTFIX "_d") |
| CMAKE_RELEASE_POSTFIX | 发布版本库文件后缀 | set(CMAKE_RELEASE_POSTFIX "_r") |

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
| **install**(类型 路径 [CONFIGURATIONS Debug\|Release] 路径) | 安装文件和文件夹，即将指定的文件及文件夹复制到指定的位置。类型可取的值为`TARGETS`，`FILES`和`DIRECTORY`（常用），其中`TARGETS`用于脚本中已经存在的target，其后的路径可以直接用target的名字，`FILES`即一般文件，`DIRECTORY`为文件夹。<del>在使用`Ninja`作为构建工具时，还必须开启`CMAKE_INSTALL_WITH_RPATH`，即`set(CMAKE_INSTALL_WITH_RPATH ON)`，否则构建会失败</del> | - |
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
   project(qtdemo VERSION 1.2.3)
   set(CMAKE_INCLUDE_CURRENT_DIR ON)
   set(CMAKE_AUTOMOC ON)
   set(CMAKE_AUTOUIC ON)
   set(CMAKE_AUTORCC ON)
   find_package(Qt5 COMPONENTS Core Gui Widgets CONFIG REQUIRED)
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
   add_executable(demo WIN32 ${HEADERS} ${SOURCES} ${FORMS} ${RESOURCES}) #不是Windows程序就不要加WIN32
   target_link_libraries(demo Qt5::Core Qt5::Gui Qt5::Widgets)
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
   if(CMAKE_SYSTEM_NAME MATCHES "Linux")
       message("Current platform: Linux")
   elseif(CMAKE_SYSTEM_NAME MATCHES "Windows")
       message("Current platform: Windows")
   elseif(CMAKE_SYSTEM_NAME MATCHES "FreeBSD")
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
   ```
- 如何为调试版本和发布版本的可执行程序设置不同后缀：
   ```cmake
   set_target_properties(${TARGET_NAME} PROPERTIES DEBUG_POSTFIX "_d")
   set_target_properties(${TARGET_NAME} PROPERTIES RELEASE_POSTFIX "_r")
   ```
- 注释：
  - 单行注释：`#`开头
  - 多行注释：使用`#[[`开头，`]]`结尾
