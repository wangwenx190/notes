## WSL2 安装

  0. 如果是从来没有安装过WSL2相关的东西，那么下面每一步都要走，如果只是为了安装一个新的发行版，只需要走下面的第七步。
  1. **管理员权限**运行*PowerShell*，输入以下指令，此步作用是启用Windows的Linux子系统功能：

     ```ps
     dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
     ```

  2. 等待上面的指令运行完成后，再运行下面的指令，此步作用是启用Windows的虚拟机平台功能：

     ```ps
     dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
     ```

  3. 等待上面的指令运行完成后，再运行下面的指令，此步作用是开启Hyper-V虚拟机：

     ```ps
     dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V /all /norestart
     ```

  4. 重启电脑。重启过程中会显示正在安装系统更新，这是正常的，耐心等待即可。
  5. 下载<https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi>这个 Linux 内核更新包并安装。
  6. 再次进入*PowerShell*环境，注意此时就**不需要管理员权限**了。运行下面这个指令，作用是默认使用WSL2而不是1：

     ```ps
     wsl --set-default-version 2
     ```

  7. 运行下面的指令安装需要的发行版，可以安装多个：

     ```ps
     wsl --install -d ubuntu22.04
     ```

     注意：这个命令是从 Microsoft Store 下载发行版，因此需要保证你本机的UWP微软商店客户端可以顺畅的连接微软的网络，如果你网络环境不行，这一步会报错，此时可以去微软的网站手动下载发行版的安装包进行安装：

     | 发行版 | 链接 |
     | ----- | ---- |
     | Ubuntu 22.04 | <https://aka.ms/wslubuntu2204> |
     | Ubuntu 20.04 | <https://aka.ms/wslubuntu2004> |
     | Ubuntu 18.04 | <https://aka.ms/wsl-ubuntu-1804> |

     下载完成后，使用`Add-AppxPackage .\app_name.appx`来手动安装。

  8. 运行下面这个指令更新WSL自己的相关组件：
    
     ```ps
     wsl --update
     ```

## WSL2 查看已经安装的发行版

  ```ps
  wsl --list
  ```

## WSL2 删除已经安装的发行版

  ```ps
  wsl --unregister ubuntu22.04
  ```

## WSL2 更新

  ```ps
  wsl --update
  ```

## WSL2 使用 Windows 系统的网络代理

  进入你自己的代理软件，记下代理的端口号，此处以`7890`为例：

  ```bash
  #!/bin/bash
  host_ip=$(cat /etc/resolv.conf |grep "nameserver" |cut -f 2 -d " ")
  export HTTP_PROXY="http://$host_ip:7890"
  export HTTPS_PROXY="http://$host_ip:7890"
  export ALL_PROXY="http://$host_ip:7890"
  ```

  注意：一个是代理地址里的`http://`前缀一定不能丢，另一个是端口号要替换成你自己的，不要无脑照抄我的。

## WSL2 和 Windows 系统互相复制文件

  进入Windows的文件管理器，地址栏输入`\\wsl$`后回车，即可看到目前已经安装的所有发行版的文件夹，可以直接和Windows系统互相复制文件。你往里面复制文件后，WSL2里的发行版会自动刷新，不需要重启WSL2，也不需要手动执行什么命令。

  注意，输入地址按下回车后可能没反应，这是因为内容还没有加载出来，一定要耐心等待，不要把窗口关了。在我自己的电脑上甚至等了好几分钟。

## WSL2 打开带图形界面的程序

  - 先安装一个 X Server，此处以*VcXsrv*为例。从<https://sourceforge.net/projects/vcxsrv/files/vcxsrv/>下载最新版的安装包，安装。
  - 运行软件，默认进入配置窗口，在*Display number*里面输入一个数字并记好，此处以`0`为例，一直下一步直到出现几个复选框，此时勾选*Disable access control*这个复选框，之后一直下一步到完成，此时VcXsrv就设置完成并启动了。
  - 进入WSL2，运行下面代码：

    ```bash
    #!/bin/bash
    host_ip=$(cat /etc/resolv.conf |grep "nameserver" |cut -f 2 -d " ")
    export DISPLAY=$host_ip:0
    ```

    注意上面那个`:0`，这个数字填的就是上一步里设置的*Display number*，一定要正确对应起来，而且冒号也不能丢。然后直接运行你的GUI软件即可。如果你是运行Qt软件，可能还是无法启动，这是因为缺少X11相关环境导致的，运行下面指令安装：

    ```bash
    sudo apt install -y libxcb* libxkbcommon-x11-0
    ```

## WSL2 设置 CPU、内存 等硬件参数

  创建一个名叫`.wslconfig`的文件，放在`C:\Users\<UserName>`文件夹下，例如`C:\Users\wangwenx190\.wslconfig`。下面是一个简单的用法示例：

  ```bash
  # Settings apply across all Linux distros running on WSL 2
  [wsl2]

  # Limits VM memory to use no more than 4 GB, this can be set as whole   numbers using GB or MB
  memory=4GB 

  # Sets the VM to use two virtual processors
  processors=2

  # Specify a custom Linux kernel to use with your installed distros.   The default kernel used can be found at https://github.com/microsoft/  WSL2-Linux-Kernel
  kernel=C:\\temp\\myCustomKernel

  # Sets additional kernel parameters, in this case enabling older Linux   base images such as Centos 6
  kernelCommandLine = vsyscall=emulate

  # Sets amount of swap storage space to 8GB, default is 25% of   available RAM
  swap=8GB

  # Sets swapfile path location, default is   %USERPROFILE%\AppData\Local\Temp\swap.vhdx
  swapfile=C:\\temp\\wsl-swap.vhdx

  # Disable page reporting so WSL retains all allocated memory claimed   from Windows and releases none back when free
  pageReporting=false

  # Turn on default connection to bind WSL 2 localhost to Windows   localhost
  localhostforwarding=true

  # Disables nested virtualization
  nestedVirtualization=false

  # Turns on output console showing contents of dmesg when opening a WSL   2 distro for debugging
  debugConsole=true

  # Enable experimental features
  [experimental]
  sparseVhd=true
  ```

  详细解释和更多用法请参考官方文档：<https://learn.microsoft.com/en-us/windows/wsl/wsl-config#configuration-settings-for-wslconfig>