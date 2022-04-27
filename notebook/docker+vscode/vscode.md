## VS Code

#### vscode 背景图

```python
    "update.enableWindowsBackgroundUpdates": true,
    "background.customImages": [
        "file:///D:/picture/2.jpg",
    ],
    "background.style": {
        "content": "''",
        "pointer-events": "none",
        "position": "absolute", //图片位置 absolute
        "width": "100%",
        "height": "100%",
        "z-index": "99999",
        "background.repeat": "no-repeat",
        "opacity": 0.2, //透明度
        "background-position": "0% 0%",
        "background-size": "cover"
    },
    "background.useFront": true,
    "background.useDefault": false, //是否使用默认图片
    "background.enabled": true,
```



#### vs code远程连接服务器

1. 安装拓展 remote ssh

![image-20211008114538390](D:\notebook\imgs\image-20211008114538390.png)

2. 选择ssh target![image-20211008114719212](D:\notebook\imgs\image-20211008114719212.png)

3. 打开配置文件，输入用户名和ip

   ![image-20211008114837860](D:\notebook\imgs\image-20211008114837860.png)

   ![image-20211008133938193](D:\notebook\imgs\image-20211008133938193.png)

   

4. 输入密码并进入文件管理

   ![image-20211008134537264](D:\notebook\imgs\image-20211008134537264.png)





#### vs code 快捷键

- 代码行缩进 `Ctrl+[` 、 `Ctrl+]`
- 代码格式化： `Shift+Alt+F`
- 上下移动一行： `Alt+Up` 或 `Alt+Down`
- 向上向下复制一行： `Shift+Alt+Up` 或 `Shift+Alt+Down`
- 在当前行下边插入一行 `Ctrl+Enter`
- 在当前行上方插入一行 `Ctrl+Shift+Enter`



- 选中行：`Ctrl+l`
- 移动到行首、行尾：`home` `end`
- 光标到行首、行尾:`shift+home`  `shift+end`


#### vscode远程连接mysql
[[vscode远程连接MySQL]]

#### vscode远程连接docker
[[vscode远程连接docker]]