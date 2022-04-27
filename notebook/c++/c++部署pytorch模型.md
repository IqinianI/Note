# C++部署pytorch模型

## 方法一：利用pytorch官方提供的LibTorch加载训练好的模型和网络

[利用pytorch官方提供的LibTorch加载训练好的模型和网络](https://zhuanlan.zhihu.com/p/191569603)

 [在C ++中加载TORCHSCRIPT模型官网链接](https://link.zhihu.com/?target=https%3A//pytorch.org/tutorials/advanced/cpp_export.html)

### 利用LibTorch调用pytorch模型

#### 1.pytorch训练好模型



#### 2.将模型序列化并存为pt文件

##### Tracing

```python
import torch
def model_trans(self, model_weight_path, model_save_path):
    # An instance of your model.
    model = self.net
    model.load_state_dict(torch.load(model_weight_path))
    model.eval()
    # An example input you would normally provide to your model's forward() method.
    example = torch.rand(1, 1, 64, 160, 160).cuda()
    # Use torch.jit.trace to generate a torch.jit.ScriptModule via tracing.
    traced_script_module = torch.jit.trace(model.module, example)
    traced_script_module.save(model_save_path)
```



##### GPU及DataParallel的问题

对于利用DataParallel训练的模型，需要在trace时使用model.module

```python
traced_script_module = torch.jit.trace(model.module, example)
```



#### 3.在C中利用LibTorch的接口进行正向推演

##### c++部署模型代码如下

```c++
#include <torch/script.h>
#include <iostream>
#include <opencv2/opencv.hpp>
#include <torch/torch.h>

// 有人说调用的顺序有关系，我这好像没啥用~~

int main()
{
    torch::DeviceType device_type;
    if (torch::cuda::is_available()) {
        std::cout << "CUDA available! Predicting on GPU." << std::endl;
        device_type = torch::kCUDA;
    }
    else {
        std::cout << "Predicting on CPU." << std::endl;
        device_type = torch::kCPU;
    }
    torch::Device device(device_type);

    //Init model
    std::string model_pb = "tests.pth";
    auto module = torch::jit::load(model_pb);
    module.to(at::kCUDA);

    auto image = cv::imread("dog.jpg", cv::ImreadModes::IMREAD_COLOR);
    cv::Mat image_transfomed;
    cv::resize(image, image_transfomed, cv::Size(32, 32));

    // convert to tensort
    torch::Tensor tensor_image = torch::from_blob(image_transfomed.data,
        { image_transfomed.rows, image_transfomed.cols,3 }, torch::kByte);
    tensor_image = tensor_image.permute({ 2,0,1 });
    tensor_image = tensor_image.toType(torch::kFloat);
    tensor_image = tensor_image.div(255);
    tensor_image = tensor_image.unsqueeze(0);
    tensor_image = tensor_image.to(at::kCUDA);
    torch::Tensor output = module.forward({ tensor_image }).toTensor();
    auto max_result = output.max(1, true);
    auto max_index = std::get<1>(max_result).item<float>();
    std::cout << output << std::endl;
    //return max_index;
    return 0;

}
```



##### CMake错误

⁉OpenCV找不到config.cmake

- 依赖包文件夹全局搜索文件路径
- 在功能包的CMakeLists里，在find_package(…)前面加入：**set(OpenCV_DIR xxxx)** （用于设置路径，让config文件被找到）



##### libtorch 报错 PyTorch is not linked with support for cuda devices

- 确认cuda是否有用

  ```c++
  std::cout << "CUDA:   " << torch::cuda::is_available() << std::endl;
      std::cout << "CUDNN:  " << torch::cuda::cudnn_is_available() << std::endl;
      std::cout << "GPU(s): " << torch::cuda::device_count() << std::endl;
  ```

- 如果都打印0，在cmakefile里写

  ```c++
  target_link_libraries(${PROJECT_NAME_STR} c10 c10_cuda torch torch_cuda torch_cpu "-Wl,--no-as-needed -ltorch_cuda")
  ```

  

