

### vscode 远程连接MySQL

#### 一、安装插件

1. ##### MySQL插件

   ![image-20211008171812602](D:\notebook\imgs\image-20211008171812602.png)  

2. ##### SQL语法高亮插件

   ![image-20211008171832776](D:\notebook\imgs\image-20211008171832776.png)

#### 二、使用

[MYSQL插件使用官方文档](https://marketplace.visualstudio.com/items?itemName=cweijan.vscode-mysql-client2#connect)

1. ##### 登录数据库

![image-20211008173058742](D:\notebook\imgs\image-20211008173058742.png)

2. 执行.sql

   ![image-20211008173332365](D:\notebook\imgs\image-20211008173332365.png)



### python读写MySQL

##### 一、安装pymysql

##### 二、连接远程db

```python
conn = pymysql.connect(
    host='192.168.0.190',  # mysql服务器地址
    port=3306,  # 端口号
    user='root',  # 用户名
    passwd='123456',  # 密码
    db='labeled_data_db',  # 数据库名称
    charset='utf8',  # 连接编码，根据需要填写
)
```



##### 三、关闭远程连接

```python
conn.close()

```



### 四、建表实例

#### 4.1 common table

```python
# common table
import json
import os
import pymysql

conn = pymysql.connect(
    host='192.168.0.190',  # mysql服务器地址
    port=3306,  # 端口号
    user='root',  # 用户名
    passwd='123456',  # 密码
    db='labeled_data_db',  # 数据库名称
    charset='utf8',  # 连接编码，根据需要填写
)
cur = conn.cursor()  # 创建并返回游标
  
# sql = "CREATE TABLE path ("
# for key in all.keys():
#     sql += str(key)+" VARCHAR(100),"
# sql = sql[:-1]+");"

# print(sql)
# 根据文件内容创建表头
sql_1 = "CREATE TABLE common_table (series_id  VARCHAR(100),studyUID VARCHAR(100),patientID  VARCHAR(100),description VARCHAR(100),spacing FLOAT);"

# cur.execute(sql_1)#执行上述sql命令，首次运行时，需要执行上面的语句，用于创建table

a = open(r"D:\\kidney\\kidney_data\\script\\clean_from_dicom\\data_all.json", "r", encoding='UTF-8')
out = a.read()
tmp = json.dumps(out)
tmp = json.loads(out)
x = len(tmp)
# print(tmp)
# print(x)

M1 = list(tmp['SeriesInstanceUID'].values())
M2 = list(tmp['StudyInstanceUID'].values())
M3 = list(tmp['PatientID'].values())
M4 = list(tmp['SeriesDescription'].values())
M5 = list(tmp['SliceThicness'].values())
print(M1[0])
print(M2[0])
print(M3[0])
print(M4[0])
print(M5[0])

for i in range(len(M1)):
    if M5[i]==None:
        M5[i]=pymysql.NULL
    sql_2 = f"insert into common_table \
            (series_id,studyUID,patientID,description,spacing)\
            values ('{M1[i]}', '{M2[i]}', '{M3[i]}', '{M4[i]}', {M5[i]})"
    print(sql_2)

    cur.execute(sql_2)
    conn.commit()

conn.close()

```

#### 4.2 path table

```python
# path table
sql_1 = "CREATE TABLE path (SeriesInstanceUID  VARCHAR(100),root_path  VARCHAR(255),data_path TEXT);"
cur.execute(sql_1)#执行上述sql命令，首次运行时，需要执行上面的语句，用于创建table

a = open(r"D:\\kidney\\kidney_data\\script\\clean_from_dicom\\data_all.json", "r", encoding='UTF-8')
out = a.read()
tmp = json.dumps(out)
tmp = json.loads(out)
x = len(tmp)
# print(tmp)
# print(x)

M1 = tmp['SeriesInstanceUID']
M2 = tmp['root_path']
M3 = tmp['data_path']

for i in range(len(M1)):
    
    sql_2 = "insert into path (SeriesInstanceUID,root_path,data_path) values (" + "'"+M1[i]+"'" +","+ "'"+M2[i]+"'" + ","+"'"+M3[i]+"'" + ");"
    cur.execute(sql_2)
    conn.commit()

conn.close()


```

#### 4.3 key table

```python

# 根据文件内容创建表头
sql_1 = "CREATE TABLE key_table (\
        case_name  VARCHAR(20),\
        series_id VARCHAR(100),\
        spacing FLOAT);"

cur.execute(sql_1)#执行上述sql命令，首次运行时，需要执行上面的语句，用于创建table

a = open(r"D:\\kidney\\kidney_data\\script\\clean_from_dicom\\data_all.json", "r", encoding='UTF-8')
out = a.read()
tmp = json.dumps(out)
tmp = json.loads(out)
x = len(tmp)
# print(tmp)
# print(x)

M1 = list(tmp['case_name'].values())
M2 = list(tmp['SeriesInstanceUID'].values())
M3 = list(tmp['SliceThicness'].values())


for i in range(len(M1)):
    if M3[i]==None:
        M3[i]=pymysql.NULL
    sql_2 = f"insert into key_table \
            (case_name,series_id,spacing)\
            values ('{M1[i]}', '{M2[i]}', {M3[i]})"
    print(sql_2)

    cur.execute(sql_2)
    conn.commit()

conn.close()
```

