

# 人脸口罩佩戴检测(识别)的AI Pipeline优化与部署

----



## Environment

- Ubuntu 18.04
- GTX 1050Ti / TITAN X
- E5 *5 Cores
- RAM 10G
- python3.8 
- pytorch1.7.1 
- CUDA11.0 
- cudnn8
- TensorRT 7.2.2.3



## Model

YOLOv5：

`git clone https://github.com/ultralytics/yolov5.git`

或者直接下载YOLOv5的5.0版本的代码。

TensorRT：

` git clone https://github.com/wang-xinyu/tensorrtx.git`



## Introduction：

现在模型训练与获得十分的容易，但是如何对模型进行压缩、优化与部署，是将深度学习真正应用到实际生活中必不可少的一部分，因此在关注模型优化与部署的前提下，选择了口罩识别这个较新且具有代表性的项目进行学习。



## Task

实现对人脸佩戴口罩进行检测是识别，并通过各种手段对模型进行优化，实现边缘设备的部署



## Action

1. 基于 AIZOO + RMFD 开源数据集图片 **11064** 张的基础上，使用Python爬取并收集手捂脸图片图片 144 张，共计 **11208** 张正脸+侧脸图片，并对图片进行数据清洗(XML文件修正)与标注。
2. 使用 YOLOv5 模型，在 Baseline 的基础上通过更换 **Adam** 优化器、添加 **Label Smoothing**、更换**YOLOv5 P6**模型等方式进行优化。最终模型的 mAP@.5:.95 为**68.5%**，权重文件大小为 **14.4M** ，推理速度在 TITAN X 上达到 **6.6 ms**。
3. 使用 **TensorRT SDK** 对模型进行量化，将网络权重从 **FP32** 压缩至 **INT8**。期间随机抽取 **1000** 张验证集数据进行 FP16 至 INT8 校准，优化后模型在检测精度几乎不变的基础上被压缩 **28%**，推理速度**提升近 50%**。
4. 使用 **Flask Web** 对模型进行部署，系统架构采用前后端分离模式，实现图形化交互界面与动态更新检测结果，最终平均耗时 **18ms** 完成本地请求+推理结果可视化。 



## Validation：

- YOLOv5性能统计

`python val.py --data data/voc-mask.yaml --weights runs/train/base/weights/best.pt --batch-size 16`

- Flask部署

`sh run.sh`

访问`127.0.0.1:8888`



## TODO：

多目标跟踪与计数、社交距离估计、边缘设备部署
