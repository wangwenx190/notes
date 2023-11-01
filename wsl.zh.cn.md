## WSL2 安装

  0. 如果是从来没有安装过WSL2相关的东西，那么下面每一步都要走，如果只是为了安装一个新的发行版，只需要走下面的第四步。
  1. 管理员权限运行*PowerShell*，输入以下指令，作用是启用Windows的Linux子系统功能：

     ```ps
     dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
     ```
  
  2. 等待上面的指令运行完成后，再运行下面的指令，作用是启用Windows的虚拟机平台功能：

     ```ps
     dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
     ```

  3. 下载<https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi>这个 Linux 内核更新包并安装
  4. 运行下面的指令安装需要的发行版，可以安装多个：

     ```ps
     wsl --install -d ubuntu22.04
     ```

  5. 运行下面这个指令，作用是默认使用WSL2而不是1：
    
     ```ps
     wsl --set-default-version 2
     ```
  
  6. 运行下面这个指令更新WSL自己的相关组件：
    
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