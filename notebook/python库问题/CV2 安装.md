### CV2 安装

```shell
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple opencv-python
    
```

#### 依赖安装

```shell
apt-get update
apt-get install libgl1-mesa-glx
apt-get install -y libglib2.0-0 libsm6 libxext6 libxrender-dev
```



### 直方图均衡化

https://blog.csdn.net/YZXnuaa/article/details/79231817

#### 全局直方图均衡化

- cv2.equalizeHist().

#### 局部直方图均衡化

- 把整个图像分成许多小块（比如按10*10作为一个小块），那么对每个小块进行均衡化

- cv2.createCLAHE()