# Docker 使用

## Docker基础

https://blog.csdn.net/zphdgqs/article/details/90110040

![image-20210902111307024](D:\notebook\imgs\image-20210902111307024.png)



## Docker常用命令

### docker镜像

- 查看镜像列表：**`docker images`**

- 从docker Hub下载镜像：**`docker pull <docker name>`**

- 查找镜像：**`docker search <docker name>`**

- 删除镜像：**`docker rmi <docker name>`**

- 创建镜像

  - 从容器中更新镜像：

    ```shell
    docker commit -m="commit describe" -a="<image author>" <container name/ID> <target image name>
    ```

  - 用dockerfile创建新镜像

- 设置镜像标签

  ```shell
  docker tag <image id/name> <username/iamge name>:<tag name>
  ```

### docker容器

- 查看容器：**``docker ps -a``**

- 启动容器:

  - **-i** : 交互操作
  - **-t** : 终端
  - **-d** : 后台运行
  - **-v** : 挂载文件夹
  - ⁉**-rm**: 退出自动删除容器⁉

  ```shell
  docker run -itd -name <container name> <image name> /bin/bash
  ```

- 进入容器

  - -**u ** 0: root用户登录 

  ```shell
  docker exec -it <container name/ID> /bin/bash
  ```

- 从容器更新镜像

  ```shell
   docker commit -m "add pytorch" kits21 kits21_danli:1.0
  ```

  



- 启动停止容器：**`docker start <container name/ID>`**

- 停止容器：**`docker stop <container name/ID>`**

- 重启容器：**`docker stop <container name/ID>`**

- 删除容器：**`docker rm -f <container name/ID>`**

  

### Dockerfile

- **FROM**：指定基础image（本地或者官方仓库）
- **MAINTAINER**：指定镜像创建者信息
- **COPY**：将文件从bulid context复制到镜像
  - COPY src dest
- **ADD**：与copy同，但是会自动解压文件
- **EXPOSE**：端口映射
- **RUN**：再容器中运行指定命令（多用于安装包）
- **CMD**：容器启动时运行指定命令
- **ENTRYPOINT**：容器启动时运行的命令

------



## kits21 docker环境配置

##### 从dockerfile build镜像

- 基础镜像
- 基础包的安装
- pytorch安装([pytorch链接](https://pytorch.org/get-started/locally/))
  - 用官方镜像安装包大
  - 换源安装
- COPY 本地文件到container
- CMD：进入镜像后执行的命令

```shell
docker build -t image_name .
```

```python

FROM wd:latest

RUN groupadd -r bigdata -g 1001 && \
  useradd -m leedan -u 1001 -r -g bigdata -s /bin/bash

USER leedan
# -m 自动创建用户目录

```

```shell

# Here is an example of a Dockerfile to use. Please make sure this file is placed to the same folder as run_inference.py file and directory model/ that contains your training weights.

FROM nvidia/cuda:11.0-base

# Install some basic utilities and python

RUN apt-get update \
  && apt-get install -y python3-pip python3-dev \
  && cd /usr/local/bin \
  && ln -s /usr/bin/python3 python \
  && pip3 install --upgrade pip

RUN pip3 install numpy simpleitk nibabel pandas opencv-python
# # # Install pytorch
# RUN pip3 install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html
RUN pip3 install torch torchvision -i http://mirrors.aliyun.com/pypi/simple/  --trusted-host mirrors.aliyun.com

# Copy the folder with your pretrained model here to /model folder within the container.
COPY kits21/ /kits21/
COPY run_inference.py /
# ADD model /model/

RUN groupadd -r myuser -g 433 && \
  useradd -u 431 -r -g myuser -s /sbin/nologin -c "Docker image user" myuser

RUN mkdir /input_nifti && mkdir /output_nifti && chown -R myuser /input_nifti && chown -R myuser /output_nifti

RUN mkdir -p /input/images/ct/ && mkdir -p /output/images/kidney-tumor-and-cyst/ && chown -R myuser /input && chown -R myuser /output

USER myuser

CMD python3 ./run_inference.py

```

### 查看是否成功安装torch及cuda是否可用

```python
import torch
print(torch.__version__)
print(torch.cuda.is_available())
```

### 从创建容器

```shell
docker run -itd --ipc=host --name="lidan" --gpus=all --shm-size="96g" -v /home/lidan:/workspace -v /home/lidan/kidney:/workspace/kidney pytorch_lidan
```

### 进入容器

```shell
docker exec -it danli_kits_test /bin/bash
```

### 保存镜像

```shell
docker save YOUR_DOCKER_IMAGE_NAME | gzip -c > test_docker.tar.gz
```

### 加载镜像

```shell
docker load -i test_docker.tar.gz
```



------



## ERROR

### 1. Docker push 

*Docker push 到仓库时失败*

##### docker 登录

login:https://blog.csdn.net/hcz666/article/details/120058535

```shell
docker login -u <username> -p <password>
```

##### docker push

error: https://blog.csdn.net/qq_14997473/article/details/110917351

发布镜像时，需要将image重新命名，命名格式应为: **dockerhub用户名/镜像名**

```shell
docker tag test/docker_node:v1 dockerwychen/docker_node:20201209001
docker login -u admin -p 123456
docker push dockerwychen/docker_node:20201209001
```



### 2. Docker build

*docker build 时 Sending build context to Docker daemon 数据过大的问题*

- 原因：因为在使用 dockerfile 制作镜像时 docker client 会发送 dockerfile 同级目录下的所有文件到docker daemon
- 解决🉑：使用 dockerfile 创建镜像时新建目录

