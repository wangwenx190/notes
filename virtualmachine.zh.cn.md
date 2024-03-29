## VMware 如何让虚拟机里的系统使用宿主机的网络代理

  - 进入你自己的代理软件，开启“允许来自局域网的连接”（或类似名字）这个功能，并记下代理的端口号。
  - Windows 宿主机进入命令提示符界面，输入`ipconfig /all`。
  - 找到一个名叫**VMware Network Adapter VMnet8**的虚拟网卡（你会发现有好几个*VMware*开头的虚拟网卡，都不要管，只要找这个*VMnet8*结尾的）。
  - 记下它的**IPv4**地址。
  - 进入虚拟机，找到你系统设置网络代理的地方，设置代理服务器为你刚才记下的IP地址，端口为你代理软件的端口号。
  - 浏览器进入设置网络代理的地方，如果设置为“使用系统代理设置”（或类似名字），那么现在就能直接连接外网了；如果设置为“使用自定义代理设置”（或类似名字），那么就要参照上一步那样填写IP地址和端口号（视具体浏览器而定，有些浏览器要求IP地址必须以`http://`开头，有些则一定不能以它开头，请自行尝试），填好后就能正常上外网了。
  - 命令行工具需要设置环境变量`HTTP_PROXY`和`HTTPS_PROXY`，格式为`http://IP地址:端口号`（一定要有`http://`这个前缀），设置好就可以访问代理了。但注意，`ping`工具无法使用代理，你设置什么环境变量也没用。注意这样设置的环境变量是临时的，关掉控制台就失效了，如果想持久，就要写到配置文件里，请自行搜索教程。