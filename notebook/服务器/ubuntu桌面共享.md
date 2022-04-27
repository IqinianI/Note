# ubuntu20.10设置桌面共享

https://blog.csdn.net/xingyu97/article/details/111091528

## 方法二（安装xrdp服务）

 Xrdp 是一个微软远程桌面协议（RDP）的开源实现，它允许你通过图形界面控制远程系统。通过 RDP，你可以登录远程机器，并且创建一个真实的桌面会话，就像你登录本地机器一样。windows系统中默认远程登录用的就是RDP协议，在ubuntu中安装xrdp服务意味着在登录ubuntu远程桌面时可以使用windows的远程桌面软件。安装步骤如下：

#### 一、安装桌面环境

 Ubuntu 服务器通常使用命令行进行管理，并且默认没有安装桌面环境。如果你正在运行 Ubuntu 桌面版，忽略这一步。

   在 Ubuntu 源仓库有很多桌面环境供你选择。一个选择是安装 Gnome，它是 Ubuntu 20.04 的默认桌面环境。另外一个选项就是安装 xfce。它是快速，稳定，并且轻量的桌面环境，使得它成为远程服务器的理想桌面。

   运行下面任何一个命令去安装你选择的桌面环境：

- 安装 Gnome

```shell
sudo apt update
sudo apt install ubuntu-desktop
```
- 安装 Xfce

```shell
sudo apt update
sudo apt install xubuntu-desktop
```

#### **二、安装 Xrdp**

​    Xrdp 被包含在默认的 Ubuntu 软件源中。想要安装它，运行：

```shell
sudo apt install xrdp 
```

一旦安装完成，Xrdp 服务将会自动启动。你可以输入下面的命令，验证它：

```shell
sudo systemctl status xrdp
```

默认情况下，Xrdp 使用/etc/ssl/private/ssl-cert-snakeoil.key,它仅仅对“ssl-cert” 用户组成语可读。所以为了能够登录远程桌面，运行下面的命令，将`xrdp`用户添加添加到这个用户组：

```shell
sudo useradd xrdp ssl-cert
```

❌PS: 我不太明白为什么要执行上述useradd命令，而且执行之后我用自己的普通账户登录仍然无法远程登录，但是使用root账户是可以的，我目前没有解决普通账户无法登陆的问题。

 重启 Xrdp 服务，使得修改生效：

```
sudo systemctl restart xrdp
```

#### 三、Xrdp 配置

​       Xrdp 配置文件定位在/etc/xrdp目录。对于基本的 Xrdp 链接，你不需要对配置文件做任何改动。

   Xrdp 使用默认的 X Window 桌面环境（Gnome or XFCE）。

   主要的配置文件被命名为 xrdp.ini。这个文件被分成不同的段，允许你设置全局配置，例如安全，监听地址，创建不同的 xrdp 登录会话等。

   不管什么时候你对配置文件做出修改，你需要重启 Xrdp 服务。

   Xrdp 使用startwm.sh文件启动 X 会话。如果你想使用另外一个 X Window 桌面，编辑这个文件。

#### 四、配置防火墙

   Xrdp 守护程序在所有的网络接口上监听端口3389。如果你在你的 Ubuntu 服务器上运行一个防火墙，你需要打开 Xrdp 端口。

   想要允许从某一个指定的 IP 地址或者 IP 范围访问 Xrdp 服务器，例如192.168.33.0/24,你需要运行下面的命令：

```shell
sudo ufw allow from 192.168.33.0/24 to any port 3389
```


​       如果你想允许从任何地方访问（由于安全原因，这种方式不鼓励），运行：

```shell
sudo ufw allow 3389
```


​       想要增加安全，你可以考虑 Xrdp 仅仅监听 localhost，并且创建一个 SSH 隧道，将本地机器的3389端口到远程服务器的同样端口之间的流量加密。

#### 五、连接 Xrdp 服务器

   现在你已经设置好你的 Xrdp 服务器，是时候打开你的 Xrdp 客户端并且连接到服务器。

   如果你有一台 Windows 电脑，你可以使用默认的 RDP 客户端。在 Windows 搜索栏输入“remote”，并且点击“Remote Desktop Connection”或者使用“win+R”组合键，然后输入mstsc。这将会打开一个 RDP 客户端。在“计算机”区域输入远程服务器 IP地址，并且点击“连接”。



## 远程连接卡顿

https://www.ivpser.com/windows-smooth/

我们在远程桌面连接到Windows服务器中，打开网络浏览器查看网页，当遇到网页中有较多的图片时，操作可能会有卡顿的现象。原因是远程桌面RDP协议实时地将画面返回到客户端的过程中，显示帧速会增加，贴图量会更大，这样就会消耗大量的客户端带宽，当达到客户端电脑与服务器传输速度达到瓶颈时，“卡顿”的现象就出现了。

1. 点击远程桌面连接软件左下角的“选项”。
   ![changeDisplay1](D:\notebook\imgs\changeDisplay1-300x171.jpg)
2. 点击“显示”选项卡。在“显示配置”处将远程桌面显示大小调整为“1024×768像素”，颜色调整为“增强色15位”
   ![changeDisplay2](D:\notebook\imgs\changeDisplay2-300x235.jpg)
3. 再点击“体验”选项卡，在选择连接速率来优化性能处选为“调制解调器 56Kbps”
   ![changeDisplay3](D:\notebook\imgs\changeDisplay3-300x234.jpg)
4. 再点击“常规”选项卡，点“保存”，再点“连接”。接下来的远程桌面体验中，您会发现操作速度就会流畅很多！
   ![password](D:\notebook\imgs\password-284x300.jpg)