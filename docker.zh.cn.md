# Docker

## 安装运行

### Windows

- 系统需求：Windows 10 22H2 或更新，Windows 11 21H2 或更新
- 需要先启用WSL2，具体步骤请参考我另一个关于WSL的文档
- 从 Docker 官网下载 [Docker Desktop 安装包](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe)并安装
- 然后直接从桌面快捷方式或者开始菜单快捷方式找到软件图标点击运行即可

### Ubuntu

- 非 GNOME 桌面环境必须安装`gnome-terminal`：

  ```bash
  sudo apt install -y gnome-terminal
  ```

- 先卸载之前安装的技术预览版或者测试版 Docker Desktop：

  ```bash
  rm -rf ~/.docker/desktop
  rm -rf ~/.config/systemd/user/docker-desktop.service
  rm -rf ~/.local/share/systemd/user/docker-desktop.service
  sudo rm -f /usr/local/bin/com.docker.cli
  sudo apt purge docker-desktop
  ```

  注意：

  - 之前没有装过 Docker Desktop 的可以跳过这一步
  - 删除文件夹时如果找不到文件夹直接忽略

- 添加 Docker 的 apt 仓库：

  ```bash
  sudo apt update
  sudo apt install -y ca-certificates curl gnupg
  sudo install -m 0755 -d /etc/apt/keyrings
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
  sudo chmod a+r /etc/apt/keyrings/docker.gpg
  echo \
    "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  sudo apt update
  ```

- 安装 Docker 相关的包：

  ```bash
  sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  ```

- 测试 Docker 是否已成功安装：

  ```bash
  sudo docker run hello-world
  ```

- 从 Docker 官网下载 [Docker Desktop 的安装包](https://desktop.docker.com/linux/main/amd64/docker-desktop-4.25.0-amd64.deb?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-linux-amd64)并安装：

  ```bash
  sudo apt update
  sudo apt install -y ./docker-desktop-<version>-<arch>.deb
  ```

  如果安装到最后报错，错误信息类似`N: Download is performed unsandboxed as root, as file '/home/user/Downloads/docker-desktop.deb' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)`这样的，可以直接忽略（官方做法）。

- 启动 Docker Desktop：

  ```bash
  systemctl --user start docker-desktop
  ```

- 退出 Docker Desktop：

  ```bash
  systemctl --user stop docker-desktop
  ```

- 设置 Docker Desktop 为开机自启：

  ```bash
  systemctl --user enable docker-desktop
  ```

### macOS

#### 英特尔CPU

- 从官网下载 [Docker Desktop 安装包](https://desktop.docker.com/mac/main/amd64/Docker.dmg?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-mac-amd64)并安装
- 然后直接从访达或启动器里找到对应的软件图标点击运行即可

#### Apple silicon

- 安装 Rosetta 2：

  ```bash
  softwareupdate --install-rosetta
  ```

- 从官网下载 [Docker Desktop 安装包](https://desktop.docker.com/mac/main/arm64/Docker.dmg?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-mac-arm64)并安装
- 然后直接从访达或启动器里找到对应的软件图标点击运行即可

## 常用操作

### 名词解释

- 镜像（Image）：类似于*GHOST*打包的`.GHO`系统镜像文件，或者各种系统的`ISO`光盘镜像，可以视为静态的系统快照
- 容器（Container）：类似于从某个具体的镜像启动的虚拟机实例，可以视为从某个系统快照恢复过来的正在运行的系统

### 查看本地所有镜像

```bash
docker images
```

### 查看本地所有正在运行中的容器

```bash
docker ps
```

### 查看本地所有运行过的容器

```bash
docker ps -a
```

### 复制宿主机文件到容器/复制容器文件到宿主机

```bash
docker cp C:/Test/test.zip aabbcc:/home/myaccount/
```

```bash
docker cp aabbcc:/home/myaccount/test.zip C:/Test/
```

### 容器使用宿主机的网络代理

### 导出容器到文件

```bash
docker export aabbcc -o mycontainer.tar
```

注意：

- 一定不要使用`>`保存文件，因为Windows和UNIX的换行符不一样（CRLF/LF），会导致你导出的容器无法跨系统导入。
- `export`命令只针对容器，不能对镜像使用

### 从文件导入容器

```bash
docker import mycontainer.tar
```

导入后可以使用`docker images`命令查看

注意：`import`命令只能导入`export`命令生成的文件，不能导入`save`命令生成的文件。

### 启动容器

- 从一个已存在的镜像启动一个全新的容器：

  ```bash
  docker run -it --name=MyContainer1 ubuntu:20.04 /bin/bash
  docker run -it --name=MyContainer2 myorganization/myimage:v1 /bin/bash
  docker run -it --name=MyContainer3 aabbcc /bin/bash # docker import 导入的容器
  ```

  注意：

  - 如果本地找不到对应的镜像，Docker会自动从网上下载（如果有的话）
  - 执行`exit`命令后，容器会直接停止运行
  - 最好使用`--name`参数指定一个容器名字，以后容器多了方便区分。但这个参数只有从镜像创建容器时才可用，也就是不能中途更改（待确认？）。
- 继续运行已经停止运行的容器：

  ```bash
  docker start aabbcc
  docker exec -it aabbcc /bin/bash
  ```

  注意：使用`exec`命令进入的容器，执行`exit`命令后容器**不会**停止运行。
- 退出容器：

  ```bash
  exit
  ```

  注意：

  - 这个命令前面没有`docker`这个前缀
  - 直接关闭当前容器所在的控制台窗口也可以

### 停止容器

```bash
docker stop aabbcc
```

注意：如果想要强制结束容器的运行，要改用`docker kill`命令。

### 进入已经在运行中的容器

```bash
docker attach aabbcc
```

注意：使用`attach`命令进入的容器，在执行`exit`命令后，容器会直接停止运行。

### 提交容器修改记录，保存当前容器为一个镜像

```bash
docker commit -m="这里填提交信息" -a="这里填作者" aabbcc myorganization/myimage:v1
```

注意：冒号后面是`TAG`，写什么都可以，不是必须写成版本号。

### 导出镜像到文件

```bash
docker save myorganization/myimage:v1 -o myimage.tar
```

注意：

- 一定不要使用`>`输出到文件，因为Windows和UNIX的换行符不一样（CRLF/LF），这样会导致你导出的文件无法跨系统加载。
- `save`命令只针对镜像，不能对容器使用。

### 从文件导入镜像

```bash
docker load myimage.tar
```

导入后可以使用`docker images`命令查看

注意：`load`命令只能导入`save`命令生成的文件，不能导入`export`命令生成的文件。

### 删除容器

```bash
docker rm aabbcc
```

### 删除镜像

```bash
docker rmi myorganization/myimage:v1
```

### 查看某个容器的状态

```bash
docker stats aabbcc
```

### 查看某个容器正在运行中的进程

```bash
docker top aabbcc
```

### 查看某个镜像的提交历史

```bash
docker history myorganization/myimage:v1
```

### 查看某个容器的日志

```bash
docker logs aabbcc
```

### [Windows] 创建一个新的容器，并把当前文件夹映射到容器中

批处理（非PowerShell！）：

```bat
docker run -itd --name=MyContainer -p 10022:22 -v %cd%:/home/wangwenx190/myproject ubuntu:20.04
```

解释：

- `-it`：交互式启动容器并将日志输出到控制台（可以当作固定参数，无脑添加）
- `-d`：启动容器后转移到后台运行
- `-p`：设置端口转发，空格后面是端口绑定规则，格式为`容器端口:宿主机端口`（待确认？）
- `-v`：设置挂载点，空格后面是挂载点参数，格式为`Windows中的路径:容器中的路径`
- `%cd%`：批处理文件中代表当前文件夹路径
- `/home/wangwenx190/myproject`：希望将当前文件夹挂载到容器什么位置
- `ubuntu:20.04`：镜像，前面已经详细解释过了，故不再赘述