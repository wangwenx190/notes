# Ubuntu 使用笔记
- 压缩解压tar：
- 创建软链接：`ln -s`。其中，`-s`参数意为创建软链接。例如：`sudo ln -s /usr/bin/clang /usr/bin/x86_64-w64-mingw32-clang`
- 搜索文件：`find URL -name "name"`。例如：`find /usr/bin -name "*clang"`
- 删除文件：`rm -f FILE`。其中，`-f`参数意为强制删除。例如：`rm -f ./test.cpp`
- 删除文件夹：`rm -rf DIR`。其中，`-r`参数意为递归删除。例如：`rm -rf ./build`
- 创建文件夹：`mkdir DIR`。例如：`mkdir build`
- 下载文件：wget curl
- 查看文件：`ls -l -h`。其中，`-l`参数意为显示详细信息，`-h`参数意为显示人类易读的大小。例如：`ls -l -h /usr/bin`
- 挂载卸载：
- 双显卡防止开机卡住安装方法+安装英伟达独显驱动：
   1. 安装系统之前先关闭`Security Boot`（安全启动，去`BIOS`里找）
   2. 屏蔽`Nouveau`开源显卡驱动：在`grub`界面，选中`Install Ubuntu`，按`e`进入命令行模式，在`quiet splash --`后面（也可能没有`-`），添加`acpi_osi=linux nomodeset`，然后按`F10`重新引导
   3. 重新引导之后，安装程序的窗口可能有一部分在屏幕下方，导致部分按钮无法点击。此时，按下`Alt+F7`，鼠标会变成手指形状，此时将窗口向上拖动即可
   4. 安装完成，重启。在电脑重启黑屏的时候，拔出U盘（重启的时候也可能卡在 logo，所以在要求选择引导选项的时候，重复上述操作）
   5. 成功进入桌面以后，要**立即安装英伟达独显驱动**：
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
    6. 为了能永久屏蔽`Nouveau`开源显卡驱动，需要修改`grub`配置文件：
       ```bash
       sudo gedit /boot/grub/grub.cfg #用nano也行，总之用一个文本编辑器打开
       #进行第二步的操作，然后保存
       sudo update-grub #更新grub
       ```
    7. 重启
- 安装常用软件：
   - 搜狗拼音输入法：
      1. 先通过`sudo apt install fcitx`安装`fcitx`
      2. 到官方网站 https://pinyin.sogou.com/linux/ 下载安装包后安装
         ```bash
         sudo dpkg -i XXX.deb
         #如果安装出错，就用“sudo apt --fix-broken install”后重新安装
         ```
      3. Region & Language -> Manage Installed Languages -> Keyboard input method system -> fcitx -> Apply System-Wide
      4. 去右上角系统托盘里找到键盘图标，右击打开菜单，Configure Current Input Method -> Input Method -> + -> Sogou Pinyin
   - 网易云音乐：步骤同上。官网：https://music.163.com/#/download
   - WPS：步骤同上。官网：http://www.wps.cn/product/wpslinux
   - TIM：
   - 微信：
   - Telegram：
   - 迅雷：
   - 百度云：
   - qBittorrent：
   - Steam：
   - mpv：
   - 7-Zip：
      ```bash
      sudo apt install p7zip-rar p7zip-full p7zip
      ```
- 安装专业软件：
   - 基本：
   - VSCode：
   - Qt Creator：
   - VirtualBox：
- 安装使用VPN：
   1. 安装`shadowsocks-qt5`：
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
