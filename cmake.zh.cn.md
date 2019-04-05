# CMake
## 常用内部变量
| 变量 | 作用 |
| --- | ---- |
| CMAKE_C_COMPILER | 设置C编译器 |
| CMAKE_CXX_COMPILER | 设置C++编译器 |
| CMAKE_C_FLAGS | 设置编译C文件时的选项 |
| CMAKE_CXX_FLAGS | 设置编译C++文件时的选项 |
| CMAKE_BUILD_TYPE | 设置构建类型（Debug和Release等） |
| BUILD_SHARED_LIBS | 更改库默认构建类型为动态库 |
| PROJECT_SOURCE_DIR | 项目源文件路径 |
| PROJECT_BINARY_DIR | 项目二进制文件路径 |
| CMAKE_INSTALL_PREFIX | 设置项目安装路径 |

如何使用：
  1. 在CMakeLists.txt中指定，使用`set`命令
  2. 在cmake命令中使用，如`cmake -DCMAKE_BUILD_TYPE=Release`
## 常用命令
| 命令 | 作用 | 示例 |
| --- | ---- | ---- |
| **cmake_minimum_required** (VERSION 版本号) | 指定cmake的最低版本，这个选项也影响着cmake使用的各种策略 | - |
| **project** (项目名 [VERSION 版本号]) | 指定项目名称。`${项目名_SOURCE_DIR}`表示项目根目录，也可以用`${PROJECT_SOURCE_DIR}`。如果给定了版本号，cmake将会自动添加`PROJECT_VERSION`，`PROJECT_VERSION_MAJOR`，`PROJECT_VERSION_MINOR`，`PROJECT_VERSION_PATCH`，`PROJECT_VERSION_TWEAK`和`CMAKE_PROJECT_VERSION`的定义 | - |
| **include_directories** (路径1 路径2 ...) | 添加头文件的搜索路径，相当于`-I`参数。此为全局指令，针对不同的目标，可以使用`target_include_directories` |
| **link_directories** (路径) | 添加库的搜索路径，相当于`-L`参数。此为全局指令，针对不同的目标，可以使用`target_link_directories` | - |
| **add_subdirectory** (路径) | 包含子目录。这个子目录的顶层也必须有CMakeLists.txt文件 | - |
| **add_executable** (目标名 [WIN32] [MACOSX_BUNDLE] 源文件1 源文件2 ...) | 编译可执行程序 | - |
| **target_link_libraries** (目标名 链接库1 链接库2 ...) | 添加链接库，相当于`-l`参数 | - |
| **add_library** (库名 [STATIC\|SHARED] 源文件1 源文件2 ...) | 编译库，默认为静态库 | - |
| **find_package** (包名 [版本] [REQUIRED]) | 查找包。如果指定了`REQUIRED`，会在找不到包时报错并中断编译。如果找到了对应的包，会自动添加`包名_FOUND`的宏定义 | - |
| **message** ([模式] "待输出信息") | 输出信息。模式可取的值为`STATUS`，`WARNING`，`AUTHOR_WARNING`，`SEND_ERROR`，`FATAL_ERROR`，`DEPRECATION` | - |
| **option** (变量名 "帮助文本" [默认值]) | 给用户提供一个可以选择`OFF/ON`的选项，默认值为`OFF` |
| **configure_file** (输入文件 输出文件 [@ONLY]) | 根据输入文件输出一个新的文件，其中的`@变量名@`会自动被替换为cmake中变量的值。建议使用`#cmakedefine`代替`#define`。`@ONLY`参数可以使cmake跳过输入文件中`${变量名}`这种变量的替换，防止破坏原来的文件 |
| **include** (路径) | 包含一个外部的cmake脚本 |
| **add_compile_definitions** (定义1 定义2 ...) | 添加预处理器定义。此为全局指令，针对不同的目标，可以使用`target_compile_definitions` |
| **add_compile_options** (选项1 选项2 ...) | 添加编译选项。此为全局指令，针对不同的目标，可以使用`target_compile_options` |
| **add_link_options** (选项1 选项2 ...) | 添加链接选项。此为全局指令，针对不同的目标，可以使用`target_link_options` |
| **install** (TARGETS\|FILES\|DIRECTORY [CONFIGURATIONS Debug\|Release] [RUNTIME\|LIBRARY] 路径)
## 杂项
- cmake中编译器的名称：`AppleClang`，`Clang`，`GNU`，`MSVC`，`SunPro`，`Intel`
- 为 VS 生成：`cmake -G"Visual Studio 16 2019" -AWin32/x64 [-Thost=x86/x64]`
- 交叉编译（以Clang为例）：
   ```cmake
   set(CMAKE_SYSTEM_NAME Linux) #设置目标系统
   set(CMAKE_SYSTEM_PROCESSOR arm) #设置目标架构
   set(triple arm-linux-gnueabihf) #设置目标triple
   set(CMAKE_C_COMPILER clang) #设置C编译器
   set(CMAKE_C_COMPILER_TARGET ${triple}) #设置C编译器triple
   set(CMAKE_CXX_COMPILER clang++) #设置C++编译器
   set(CMAKE_CXX_COMPILER_TARGET ${triple}) #设置C++编译器triple
   ```
- 编译Qt程序：
   ```cmake
   cmake_minimum_required(VERSION 3.8.0 FATAL_ERROR)
   project(qtdemo)
   set(CMAKE_INCLUDE_CURRENT_DIR ON)
   set(CMAKE_AUTOMOC ON)
   set(CMAKE_AUTOUIC ON)
   set(CMAKE_AUTORCC ON)
   find_package(Qt5 COMPONENTS Core Gui Widgets CONFIG REQUIRED)
   set(helloworld_SRCS
       mainwindow.ui
       mainwindow.cpp
       main.cpp
   )
   add_executable(helloworld WIN32 ${helloworld_SRCS})
   target_link_libraries(helloworld Qt5::Core Qt5::Gui Qt5::Widgets)
   ```
