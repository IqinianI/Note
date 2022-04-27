### PyTorch使用tensorboard

#### 远程服务器和本地计算机

- ###### 建立远程服务器与本地电脑的连接

  ```shell
  ssh -N -f -L localhost:16006:localhost:6006 username@your_remote_host_name
  
  eg:
  ssh -p 12345 -N -f -L localhost:16006:localhost:6006 jasper@172.1x.1x.xxx
  ```

  

- ###### 在远程服务器上启动tensorboard

  ```shell
  tensorboard --logdir=file
  ```

- 在本地打开链接

  ```shell
  http://172.1x.1x.xxx:6006(远程服务器ip)
  http://localhost:6006(本地ip)
  ```

  
