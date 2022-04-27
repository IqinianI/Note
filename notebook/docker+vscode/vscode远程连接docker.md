# vs code 远程docker配置

## docker基础
[[Docker基础]]

https://segmentfault.com/a/1190000023095631

### 远程主机配置

1. 创建文件 `daemon.json` 到目录 `/etc/docker`:

   ```python
    {"hosts": ["tcp://0.0.0.0:2375", "unix:///var/run/docker.sock"]}
   ```

2. 创建文件 `/etc/systemd/system/docker.service.d/override.conf`:

   ```python
    [Service]
    ExecStart=
    ExecStart=/usr/bin/dockerd
   ```

   

3. 重启docker：

   ```shell
    systemctl daemon-reload
    systemctl restart docker.service 
   ```

### 本地配置docker客户端

#### 安装docker

Linux/Mac:直接安装管理包

Windows:

- 方法一：安装[docker desktop](https://link.segmentfault.com/?url=https%3A%2F%2Fhub.docker.com%2Feditions%2Fcommunity%2Fdocker-ce-desktop-windows), -- **其实不需要这么大而全**

- 方法二：下载 [docker.exe](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2FStefanScherer%2Fdocker-cli-builder%2Freleases%2F) 放到Path包含的路径下就行了， 比如c:windows

#### 配置本地使用远程docker服务

1. 创建一个context

   ```shell
   docker context create <context name> --docker "host=ssh://<user>@<host>"
   ```

2. 切换到这个context

   ```shell
   docker context use <context name>
   ```

3. 测试

   ```shell
   docker info
   ```

### Visual Studio Code

#### 打开vscode，安装插件

- Extensions -> Search Remote -> `Remote Development`
- Extensions -> Search Docker -> `Docker`

#### 本地vscode访问远程容器

1. 切换Dockers context

2. 打开VSCode，按下 `ctrl+shift+p` 运行 `docker contexts use` , 选择上面创建的docker context.

#### 连接容器

1. 按下 `ctrl+shift+p` 运行 `Remote-Containers:Attach to Running Container...`, 选择上面创建的容器名字。

2. 连接成功后，按下 `ctrl+k, ctr+o`, 你会发现VSCode弹出的不是本地目录，而是容器内部的目录！现在VSCode只是一个客户端，一切操作都在容器中了