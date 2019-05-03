## Manjaro
- 包管理
  ```text
  pacman -S <package_name>：安装指定软件包
  pacman -R <package_name>：删除指定软件包，但保留其全部已经安装的依赖关系
  pacman -Rs <package_name>：删除指定软件包，及其所有没有被其他已安装软件包使用的依赖关系
  pacman -Rsc <package_name>：删除软件包和所有依赖这个软件包的程序。警告: 此操作是递归的，请小心检查，可能会一次删除大量的软件包。
  pacman -Rdd <package_name>：删除软件包，但是不删除依赖这个软件包的其他程序
  pacman -Rn <package_name>：pacman 删除某些程序时会备份重要配置文件，在其后面加上*.pacsave扩展名。-n 选项可以避免备份这些文件

  pacman -Ss string1 string2 ...：查找软件包
  pacman -Qs string1 string2 ...：查找本地已经安装的软件包
  pacman -Si <package_name>：显示软件包的详细信息
  pacman -Qi <package_name>：显示本地安装包的详细信息
  pacman -Ql <package_name>：获取已安装软件包所包含文件的列表
  pacman -Fl <package_name>：查询远程库中软件包包含的文件
  pacman -Qk <package_name>：检查软件包安装的文件是否都存在。两个参数 k 将会执行一次更彻底的检查。
  pacman -Qdt：罗列所有不再作为依赖的软件包（孤立orphans）
  pacman -Qet：罗列所有明确安装而且不被其它包依赖的软件包
  pactree <package_name>：显示软件包的依赖树
  pactree -r <package_name>：检查一个安装的软件包被哪些包依赖

  pacman -Sc：清除未安装软件包的缓存（即清理 /var/cache/pacman/pkg/），会保留软件包的当前有效版本
  pacman -Scc：清理所有缓存，但这样 pacman 在重装软件包时就只能重新下载了

  pacman -U /path/to/package/package_name-version.pkg.tar.xz：安装一个本地包（不从源里下载）
  pacman -U http://www.example.com/repo/example.pkg.tar.xz：安装一个远程包（不在 pacman 配置的源里面）
  ```
- 更新系统（即更新所有软件）
  ```bash
  pacman -Syu
  pacman -Su
  ```
- 添加中文社区加速镜像源：
  1. 所有有效的加速服务器列表：https://github.com/archlinuxcn/mirrorlist-repo 。可将 https://github.com/archlinuxcn/mirrorlist-repo/blob/master/archlinuxcn-mirrorlist 中所有的服务器信息都写入到一个叫`archlinuxcn`（没有后缀名）的文本文件中，保存到`/etc/pacman.d/`，此操作**需要管理员权限**。
  2. 打开`/etc/pacman.conf`，在文件结尾添加一下内容：
     ```text
     [archlinuxcn]
     SigLevel = Optional TrustedOnly
     Include = /etc/pacman.d/archlinuxcn
     ```
     此操作**需要管理员权限**。
  3. `sudo pacman -Syyu`
  4. 安装`archlinuxcn-keyring`这个软件包
  5. 更新系统并安装软件包
- 安装专业软件：
  - 基本：`base-devel`，`cmake`，`ninja`，`gdb`，`git`，`pkg-config`
  - Clang：`clang`，`clang-tools`，`lld`，`lldb`，`llvm`
- 为bash脚本添加可执行权限：
  ```bash
  chmod 777 ./test.sh
  ```
