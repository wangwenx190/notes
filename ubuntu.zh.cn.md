# Ubuntu 使用笔记
- 压缩解压`tar`文件：
   ```text
   *.tar：tar -xvf PATH
   *.tar.xz：tar -xvJf PATH
   *.tar.gz：tar -xvzf PATH
   *.tar.bz2：tar -xvjf PATH
   ```
   `-c`参数意为创建压缩包，`-x`参数意为解压压缩包，`-v`参数意为显示输出信息，`-f`参数意为指定压缩包文件（待创建或待解压文件），`-t`参数意为列出压缩包中的文件（显示压缩包内容）
- 压缩解压`7z`文件：
   ```text
   a：添加文件到压缩包。例如：7z a test.7z ./data/*
   e：解压。例如：7z e ./test.7z
   l：显示压缩包内容。例如：7z l ./test.7z
   -m：设置压缩选项。“-mx -myx -m0=LZMA2:d=1g:fb=273”为极限压缩，“-ms=on”为启用固实压缩（默认），“-mmt=on”为启用多线程压缩（默认）。例如：7z a test.7z ./data/* -mx -myx -ms=on -mmt=on -m0=LZMA2:d=1g:fb=273
   ```
- 创建软链接：`ln ORIGINAL NEW`。`-s`参数意为创建软链接，`-f`参数意为删除已经存在的软链接（即覆盖同名的软链接）。例如：`sudo ln -sf /usr/bin/clang /usr/bin/x86_64-w64-mingw32-clang`
- 搜索文件：`find PATH -name "NAME"`。例如：`find /usr/bin -name "*clang"`
- 删除文件和文件夹：`rm PATH`。`-f`参数意为强制删除，`-r`参数意为递归删除（即删除文件夹）。例如：`rm -rf ./test/build`
- 创建文件夹：`mkdir NAME`。例如：`mkdir build`
- 下载文件：
   - `wget URL`：`-O`参数意为指定保存文件名，`-c`参数意为继续下载。例如：`wget -O test.zip https://www.ex.com/abc?=down`
   - `curl URL`：`-o`参数意为指定保存文件名，`--progress`参数意为显示进度条。例如：`curl -o test.zip --progress https://file.abc.com/down.asp`
- 列出文件：`ls PATH`。`-l`参数意为显示详细信息，`-h`参数意为显示人类易读的大小，`-a`参数意为显示所有文件和文件夹（包括“.”和“..”），`-A`参数意为显示除了“.”和“..”以外的所有文件和文件夹，`-L`参数意为列出链接文件名而不是链接到的文件，`-N`参数意为不限制文件长度，`-Q`参数意为把输出的文件名用双引号括起来，`-R`参数意为列出所有子目录下的文件，`-d`参数意为将目录像文件一样显示，而不是显示其下的文件。例如：`ls -lhA /usr/bin`
- 设置环境变量：`export A=b:C=d`。不同变量之间以英文半角冒号`:`分隔，以英文半角美元符号`$`引用其他变量。例如：`export PATH=./eg1/bin:./eg2/bin:$PATH`
- 挂载和卸载：`mount SOURCE MOUNT_POINT`和`umount MOUNT_POINT`。`-r`参数意为只读挂载，`-w`参数意为读写挂载（默认）。例如：`mount -o loop ./test/soft.iso /mnt/iso1` -> `umount /mnt/iso1`
- 安装`.DEB`文件：`dpkg -i FILE`。例如：`sudo dpkg -i example.deb`
- 创建空文件：`touch PATH`。例如：`touch ./code/untitled.txt`
- 输出文件内容：`cat PATH`。例如：`cat ./test/cfg.ini`
- 复制文件和文件夹：`cp SOURCE TARGET`。`-f`参数意为强制覆盖，`-p`参数意为将文件属性也一同复制，`-r`参数意为递归复制（即复制文件夹），`-s`参数意为复制为软链接。例如：`cp -rf ./code ./test/code`
- 移动文件和文件夹：`mv SOURCE TARGET`。`-f`参数意为强制覆盖。例如：`mv -f ./code ./download/code`
- 重命名文件和文件夹：同`mv`命令。
- 清空控制台内容：`clear`。
- 包管理：
   ```text
   apt install：安装软件包
   apt search：搜索软件包
   apt purge：删除软件包，同时删除其所有配置文件
   apt autoremove：删除为了满足依赖而安装的，但现在已经不需要的软件包（但保留其配置文件）
   apt remove：删除软件包，但保留其配置文件
   apt autoclean：删除“/var/cache/apt/archives”文件夹下过期的安装包（清除过久的缓存）
   apt clean：删除“/var/cache/apt/archives”文件夹下所有的安装包（清除全部缓存）
   apt --fix-broken install：安装软件时出现任何依赖错误，都尝试使用此命令解决
   ```
- 修改默认程序：修改`/etc/gnome/defaults.list`文件。
- `PPA`的添加和删除：
   ```text
   apt-add-repository ppa:NAME：添加名为“NAME”的PPA
   apt-add-repository --remove ppa:NAME：删除名为“NAME”的PPA。此命令不会删除使用此PPA安装的软件包。
   #如果要删除PPA以及用它安装的软件包，要使用“ppa-purge”这个工具，要单独安装
   #ppa-purge ppa:NAME：删除名为“NAME”的PPA，同时删除此PPA安装的所有软件包
   ```
- 更新软件：
   ```bash
   sudo apt update
   sudo apt full-upgrade
   ```
- 更新系统（摘自Ubuntu官方Wiki）：
   - Open the "Software & Updates" application.
   - Select the 3rd Tab called "Updates".
   - Set the "Notify me of a new Ubuntu version" dropdown menu to "For any new version".
   - Press Alt+F2 and type in "update-manager -c" (without the quotes) into the command box.
   - Update Manager should open up and tell you: New distribution release '19.04' is available.
      - If not you can also use "/usr/lib/ubuntu-release-upgrader/check-new-release-gtk"
   - Click Upgrade and follow the on-screen instructions.
- <del>安装、更换和删除内核</del>（没必要，系统会自动更新内核。更新完再用`sudo apt autoremove`清理一下就可以了）
- 双显卡笔记本防止开机卡住安装方法+安装英伟达独显驱动：
   1. 安装系统之前先关闭`Security Boot`（安全启动，去`BIOS`里找）
   2. <del>屏蔽`Nouveau`开源显卡驱动：在`grub`界面，选中`Install Ubuntu`，按`e`进入命令行模式，在`quiet splash --`后面（也可能没有`-`），添加`acpi_osi=linux nomodeset`，然后按`F10`重新引导</del>★从**19.04**开始，Ubuntu官方已经增加了**Safe Graphics Mode**启动模式，此模式会自动添加此参数，不用再手动修改了，在GRUB界面直接选择即可。
   3. 重新引导之后，安装程序的窗口可能有一部分在屏幕下方，导致部分按钮无法点击。按下`Alt+F7`，鼠标会变成手指形状，此时将窗口向上拖动即可
   4. 安装完成，重启。在电脑重启黑屏的时候，拔出U盘（重启的时候也可能卡在 logo，所以在要求选择引导选项的时候，重复上述操作）
   5. 永久屏蔽`Nouveau`开源显卡驱动：
       ```bash
       sudo nano /etc/modprobe.d/blacklist.conf
       #在blacklist.conf文件最后添加下面两行后保存
       blacklist nouveau
       options nouveau modeset=0
       sudo update-initramfs -u #刷新内核
       #sudo gedit /boot/grub/grub.cfg
       #进行第二步的操作，然后保存
       #sudo update-grub #更新grub
       ```
   6. 安装英伟达独显驱动：
      ```bash
      #方法一（推荐）：
      ubuntu-drivers devices #查看显卡型号及建议安装的驱动
      sudo ubuntu-drivers autoinstall #自动安装推荐的驱动
      #方法二：
      sudo add-apt-repository ppa:graphics-drivers/ppa
      sudo apt update
      ubuntu-drivers devices
      sudo apt install nvidia-390 #从上一行命令的输出中选择合适的版本安装
      #方法三：
      #“软件和更新” -> “附加驱动” -> 安装驱动
      #安装成功后记得重启系统以生效
      ```
    7. 重启
- 安装常用软件：
   - <del>搜狗拼音输入法</del>（新版Ubuntu已经自带中文输入法了，没必要再装搜狗了）：
      1. <del>先安装`fcitx`，`fcitx-libs-qt`，`libfcitx-qt0`，`libopencc2`和`libqtwebkit4`</del>（依赖关系不满足时使用`sudo apt --fix-broken install`修复）
      2. 到官方网站 https://pinyin.sogou.com/linux/ 下载安装包后安装
      3. Region & Language -> Manage Installed Languages -> Keyboard input method system -> fcitx -> Apply System-Wide
      4. 去右上角系统托盘里找到键盘图标，右击打开菜单，Configure Current Input Method -> Input Method -> + -> Sogou Pinyin
   - 网易云音乐：到官方网站 https://music.163.com/#/download 下载安装包后安装
   - WPS：到官方网站 http://www.wps.cn/product/wpslinux 下载安装包后安装。字体到这里下载安装：http://mirrors.ustc.edu.cn/deepin/pool/non-free/t/ttf-wps-fonts/
   - TIM：http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin.com.qq.office/
   - 微信：http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin.com.wechat/ 和 http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin.com.weixin.work/
   - Telegram：包名为`telegram-desktop`，如果官方版本较老，新版本可在 https://desktop.telegram.org/ 下载安装
   - 迅雷极速版：http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin.com.thunderspeed/
   - 百度网盘：http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin.com.baidu.pan/
   - qBittorrent：包名为`qbittorrent`，如果官方版本较老，新版本可在 https://www.qbittorrent.org/download.php 下载安装
   - Steam：包名为`steam`，如果官方版本较老，新版本可在 https://store.steampowered.com/about/ 下载安装
   - VLC Media Player：包名为`vlc`，如果官方版本较老，新版本可在 https://www.videolan.org/vlc/#download 下载安装
   - screenfetch：包名为`screenfetch`
   - Free Download Manager：https://www.freedownloadmanager.org/board/viewtopic.php?f=1&t=17900
   - 7-Zip：包名为`p7zip-rar`，`p7zip-full`和`p7zip`（都要安装）
   - 有道词典：http://cidian.youdao.com/multi.html#linuxAll
   - 美化工具和通知栏：包名为`gnome-tweak-tool`和`libindicator`
   - Wine：安装深度移植的软件必须要安装这个：http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin-wine/ http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin-wine-helper/ http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin-wine-helper64/ http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin-wine-plugin/ http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin-wine-plugin-virtual/ http://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin-wine-uninstaller/
   - 邮件客户端 Thunderbird：包名为`thunderbird`，如果官方版本较老，新版本可在 https://www.thunderbird.net/zh-CN/ 下载安装
   - 火狐浏览器：`firefox`
   - 谷歌浏览器：`chromium-browser`
   - Rhythmbox?
   - Brasero：包名为`brasero`（有必要安装吗？）
- 安装专业软件：
   - 基本：包名为`build-essential`，`gdb`，`git`，`cmake`，`pkg-config`，`autoconf`，`automake`，`python`，`python3`，`perl`，`ruby`，`flex`，`bison`，`yasm`，`nasm`，`binutils`，`upx-ucl`，`ninja-build`，`autotools-dev`，`libtool`
   - VSCode：到官方网站 https://code.visualstudio.com/Download 下载合适的安装包安装
   - Qt Creator：包名为`qtcreator`，如果官方版本较老，新版本可在 http://mirrors.ustc.edu.cn/qtproject/official_releases/qtcreator/ 下载安装
   - VirtualBox：包名为`virtualbox`，如果官方版本较老，新版本可按照官方网站 https://www.virtualbox.org/wiki/Linux_Downloads 的指导来安装
   - Clang：包名为`clang`，`clang-tools`，`lldb`，`lld`，`libfuzzer-N-dev`，`libc++-dev`，`libc++abi-dev`，`libomp-dev`（`N`为具体的主版本号，例如：7、8和9等），如果官方版本较老，新版本可按照官方网站 http://apt.llvm.org/ 的指导来安装
   - MinGW-w64：包名为`mingw-w64`，`mingw-w64-common`，`mingw-w64-i686-dev`，`mingw-w64-tools`，`mingw-w64-x86-64-dev`。具体请查看官方网站 https://launchpad.net/ubuntu/+source/mingw-w64
   - glibc：包名为`libc6-dev`，`libc6-dev-amd64`，`libc6-dev-amd64-cross`，`libc6-dev-amd64-i386-cross`，`libc6-dev-i386`，`libc6-dev-i386-amd64-cross`，`libc6-dev-i386-cross`（不知道具体哪个有用，我就都写下来了）
   - GCC/G++：包`build-essential`自带
   - ICC：按照官方网站 https://software.intel.com/en-us/qualify-for-free-software/opensourcecontributor 的指导下载安装
   - Unity3D：https://forum.unity.com/threads/unity-hub-v-1-3-2-is-now-available.594139/ 或 https://forum.unity.com/threads/unity-on-linux-release-notes-and-known-issues.350256/
   - Krita：包名为`krita`
   - UnrealEngine：https://docs.unrealengine.com/en-US/Platforms/Linux/BeginnerLinuxDeveloper/SettingUpAnUnrealWorkflow
- 安装使用代理：
   1. **SS**节点：
      1. 安装`shadowsocks-qt5`：
         1. 【推荐】直接去作者的 GitHub 下载 snap 版本：https://github.com/shadowsocks/shadowsocks-qt5/releases
         2. 使用命令行安装（**不推荐**，因为我试了很多次，都安装不上）
            ```bash
            sudo add-apt-repository ppa:hzwhuang/ss-qt5
            sudo apt update
            sudo apt install shadowsocks-qt5
            ```
      2. 设置节点：
         ```text
         Local Address：127.0.0.1
         Local Port：1080
         Local Server Type：HTTP(S)
         Automation：Auto connect on application start
         #其他选项按你的节点来
         ```
      3. 设置系统代理，**IP地址**均为`127.0.0.1`，**端口**均为`1080`
      4. 设置浏览器代理，IP地址与端口的设置与上一步的相同（所有协议都要这样设置）
      5. 软件里点击Connect
   2. **SSR**节点：
      1. 安装`electron-ssr`：直接去官网下载AppImage版本 https://github.com/erguotou520/electron-ssr/releases
      2. 运行软件后添加节点。如果运行后看不到软件主界面，按**CTRL + SHIFT + W**显示。
      3. 在软件主界面按**CTRL + SHIFT + B**调出菜单，系统代理模式->**全局代理**。
      4. 系统设置->网络->网络代理->自动，浏览器设置->代理->使用系统代理（所有协议）
      5. 如果要使用 Git ，可以直接开始用了，不用给它再单独设置代理了，因为已经开启了全局代理
- 常用分区方案：

   | 挂载点 | 文件系统 | 推荐大小 | 分区介绍 |
   | ----- | -------- | ------- | ------- |
   | / | ext4 | 15~20GB | 根目录，相当于 Windows 的 C 盘，用于存放系统文件。此外，所有软件也都会安装到这个分区，如果你可能安装很多软件，要适当调整这个分区的大小 |
   | /home | ext4 | 个人桌面用户建议此分区尽可能的大 | 主目录，用户的所有个人文件均存放在这个分区 |
   | /boot | ext4 | 用不着太大，但也不能太小，建议1GB | 引导分区，系统存放内核的地方 |
   | /swap | swap | 当你的物理内存<8GB时建议为物理内存的两倍，否则为物理内存的大小 | 交换空间，相当于 Windows 的虚拟内存 |

   注：如果你只有一块硬盘，并且也不用考虑与其他操作系统（比如 Windows）共存的问题，可以只创建一个根分区。
- 常用快捷键（终端）：

   | 按键 | 作用 |
   | ---- | ---- |
   | Ctrl + Alt + T | 打开终端 |
   | Tab | 命令或文件名自动补全 |
   | F11 | 切换全屏/窗口 |
   | Ctrl + Shift + C | 复制 |
   | Ctrl + Shift + V | 粘贴 |
- 常用快捷键（文件管理器）：

   | 按键 | 作用 |
   | ---- | ---- |
   | Ctrl + L | 显示地址栏（可直接复制路径） |
- 命令行刻录U盘：
   ```bash
   sudo fdisk -l #找到你的U盘
   umount /dev/sdb #卸载你的U盘
   sudo dd if=~/Downloads/ubuntu.iso of=/dev/sdb bs=4M #等待刻录结束，期间不会有任何输出或提示
   ```
- 验证文件哈希值：
   ```bash
   md5sum myfile.dat #计算文件的MD5值
   sha1sum myfile.dat #计算文件的SHA-1值
   sha256sum myfile.dat #计算文件的SHA-256值
   sha512sum myfile.dat #计算文件的SHA-512值
   md5sum -c md5sum.txt #计算md5sum.txt中给定文件的MD5值并与记录的值比对
   sha1sum -c sha1sum.txt #计算sha1sum.txt中给定文件的SHA-1值并与记录的值比对
   sha256sum -c sha256sum.txt #计算sha256sum.txt中给定文件的SHA-256值并与记录的值比对
   sha512sum -c sha512sum.txt #计算sha512sum.txt中给定文件的SHA-512值并与记录的值比对
   #注：文本文件中可以有不止一个文件的哈希值
   ```
- Linux系统使用`libimobiledevice`实现对iOS设备的访问，常见发行版一般都自带此库，不用手动安装，将iOS设备连接至电脑后直接打开文件管理器即可
