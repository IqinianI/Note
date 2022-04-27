# nn-Unet 我奶奶都会用的分割模型

## 1. 安装

```shell
pip install nnunet
```



## 2. 创建文件夹

先创建 nnUNet_preprocessed、nnUNet_trained_models、nnUNet_raw 3 个主文件夹，在 nnUNet_raw 下新建 nnUNet_raw _data 文件夹，然后在nnUNet_raw _data 文件夹下新建文件夹  TaskXXX_task, XXX 是任务序列号（大于100），任务名取好记的英文名

```
nnUNet_preprocessed
nnUNet_trained_models
nnUNet_raw/nnUNet_raw_data/
├── Task101_kidney
├── Task102_xxxx
├── Task103_xxxx 

```

任务文件夹里的结构如下

```
Task001_BrainTumour/
├── dataset.json
├── imagesTr
├── imagesTs
└── labelsTr

nnUNet_preprocessed
nnUNet_trained_models
nnUNet_raw/nnUNet_raw_data/
├── Task556_PZ
├── Task557_PX
├── Task558_PS
    ├──dataset.json
    ├──imagesTr
        ├──ps_0000_0000.nii.gz
        ├──...
    ├──imagesTs
    ├──labelsTr
    ├──labelsTs
    ├──log
```

**imagesTr** 和 **labelsTr** 分别存放训练的原图和标签。**imagesTs**存放测试的原图。**dataset.json** 下面会说。



## 3. 添加环境变量

修改Home目录下的**.bashrc**文件，在最后加入下面内容(!**注意这三个目录应该是对应自己的**)

```
export nnUNet_raw_data_base="/xxx/xxx/nnUNet_raw"                   
export nnUNet_preprocessed="/xxx/xxx/nnUNet_preprocessed"
export RESULTS_FOLDER="/xxx/xxx/nnUNet_trained_models"


export nnUNet_raw_data_base="/workspace/dataset/nn_Unet/nnUNet_raw_data_base/nnUNet_raw"
export nnUNet_preprocessed="/workspace/dataset/nn_Unet/nnUNet_raw_data_base/nnUNet_preprocessed"
export RESULTS_FOLDER="/workspace/dataset/nn_Unet/nnUNet_raw_data_base/nnUNet_trained_models"

```

保存后，在终端输入

```
source ~/.bashrc
```



## 4. 图像命名

**imagesTr** 和 **labelsTr** 中图像的命名也有要求。其中**imagesTr**需要改成类似如下这样，而且必须是 **.nii.gz** 格式。

```
cta_000_0000.nii.gz
cta_001_0000.nii.gz
cta_002_0000.nii.gz
cta_003_0000.nii.gz
...
cta_100_0000.nii.gz
```

**labelsTr **需要改成如下样式，注意原图和标签图序号一一对应, 名字后面不带**_0000**。

```
cta_000.nii.gz
cta_001.nii.gz
cta_002.nii.gz
cta_003.nii.gz
...
cta_100.nii.gz
```

**imagesTs **改成如下，无需从0开始标号，可以接着训练集继续标下去，比较方便。

```
cta_101_0000.nii.gz
cta_102_0000.nii.gz
cta_103_0000.nii.gz
cta_104_0000.nii.gz
...
```



## 5. dataset.json 生成脚本

修改 foldername 和 root_path, root_path 是存放的是刚刚创建的 nnUNet_raw、 nnUNet_preprocessed、nnUnet_trained_models 文件夹的根目录。

```python
import os
from collections import OrderedDict
from batchgenerators.utilities.file_and_folder_operations import save_json


def main():

    foldername = "Task504_px"
    root_path = "/workspace/nn_Unet/nnUNet_raw_data_base/"

    numTraining = 149
    numTest = 0

    numClass = 2

    json_dict = OrderedDict()

    json_dict['name'] = foldername

    json_dict['description'] = foldername

    json_dict['tensorImageSize'] = "4D"

    json_dict['reference'] = "see challenge website"

    json_dict['licence'] = "see challenge website"

    json_dict['release'] = "0.0"

    json_dict['modality'] = {
        "0": "CT",
    }

    json_dict['labels'] = {i: str(i) for i in range(numClass)}

    json_dict['numTraining'] = numTraining

    json_dict['numTest'] = numTest

    json_dict['training'] = [{
        'image':
        os.path.join(
            root_path,
            "/nnUNet_raw/nnUNet_raw_data/{foldername}/imagesTr/cta_{n:0>3d}.nii.gz".format(
                n=i, foldername=foldername)),
        "label":
        os.path.join(
            root_path,
            "/nnUNet_raw/nnUNet_raw_data/{foldername}/labelsTr/cta_{n:0>3d}.nii.gz".format(
                n=i, foldername=foldername)),
    } for i in range(numTraining)]

    json_dict['test'] = [
        os.path.join(
            root_path,
            "/nnUNet_raw/nnUNet_raw_data/{foldername}/imagesTs/cta_{n:0>3d}.nii.gz".format(
                n=i, foldername=foldername)) for i in range(numTraining, numTraining + numTest)
    ]

    save_json(
        json_dict,
        os.path.join(
            root_path,
            "/nnUNet_raw/nnUNet_raw_data/{foldername}/dataset.json".format(foldername=foldername)))
    print(json_dict['training'])

```



## 6. 数据预处理

XXX 代替为你的任务序号，该命令进行预处理

```shell
nnUNet_plan_and_preprocess -t XXX --verify_dataset_integrity
```

坑：预处理如果检查类型的时候出现错误，建议把去3个主文件夹加找到相对应的预处理生成的 TaskXXX 文件夹，全部删除干净后再重新预处理



## 7. 训练

GPU设置

```shell
export CUDA_VISIBLE_DEVICES=X
```

模型架构有3种Unet：2D U-Net`, `3D U-Net 和 U-Net Cascade



使用命令 nnUNet_train， 该命令参数很多，可以使用`nnUNet_train -h`查看参数的含义

```shell
nnUNet_train CONFIGURATION TRAINER_CLASS_NAME TASK_NAME_OR_ID FOLD (additional options)
```

TASK_NAME_OR_ID 是自己创建的任务名，FOLD是五折交叉的折数（0, 1, 2, 3, 4）

### 7.1  2D U-Net 

```shell
nnUNet_train 2d nnUNetTrainerV2 TaskXXX_MYTASK FOLD
```



### 7.2 3D full resolution Unet 

```shell
nnUNet_train 3d_fullres nnUNetTrainerV2 TaskXXX_MYTASK FOLD
```

###  

### 7.3 3D U-Net cascade

step1 : 3D low resolution U-Net 训练

```shell
nnUNet_train 3d_lowres nnUNetTrainerV2 TaskXXX_MYTASK FOLD
```



step2: 3D full resolution U-Net 训练， 这里需要用 nnUNetTrainerV2CascadeFullRes，必须在 step1 跑完五折交叉后才能进行。

```shell
nnUNet_train 3d_cascade_fullres nnUNetTrainerV2CascadeFullRes TaskXXX_MYTASK FOLD
```



## 8. 断点开始训练

 nnU-Net 每 50 个 epoch 存储一个检查点。如果您需要继续之前的训练，只需在训练命令中添加一个==-c==即可。如：

```shell
nnUNet_train 3d nnUNetTrainerV2 100 3 -c
```


## 9. 预测

```
nnUNet_predict -i INPUT_FOLDER -o OUTPUT_FOLDER -t TASK_NAME_OR_ID -m CONFIGURATION
```

- INPUT_FOLDER: 测试数据地址
- OUTPUT_FOLDER： 分割数据存放地址
- CONFIGURATION： 使用的什么架构，2d or 3d_fullres or 3d_cascade_fullres等

例如

```shell
nnUNet_predict -i /home/.../nnunet_file/nnUNet_raw/nnUNet_raw_data/Task100_adrenal/imagesTs -o /home/.../nnunet_file/output -t 100 -m 2d -f 2

```

 
