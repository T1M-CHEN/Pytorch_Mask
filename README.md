

# 人脸口罩佩戴检测(识别)的AI Pipeline优化与部署



## Environment

- Ubuntu 18.04
- GTX 1050Ti / TITAN X
- E5 *5 Cores
- RAM 10G
- py3.8_pytorch 1.7.1_CUDA 11.0_CUDNN 8
- TensorRT 7.2.2.3



## Model

`git clone https://github.com/ultralytics/yolov5.git`

或者直接下载YOLOv5的5.0版本的代码。



## Task

受疫情影响，戴口罩的人脸遮挡了人脸的很多特征，将口罩作为一个特征进行识别，在此之前的检测模型不能很好的检测被口罩大部分遮挡的人脸，为了检测到戴口罩的人脸，设计了新的思路，将口罩作为一个特征进行识别，便于人脸位置的确定和后期人脸关键点的追踪等等。



## Introdution

(1)使用 AIZOO + RMFD 开源数据集11064张照片，使用Python爬取并收集手捂脸图片图片144张，共计11208张正脸+侧脸图片，并对图片进行数据清洗(XML文件修正)与标注。

(2)使用YOLOv5模型，在默认Baseline条件的基础上通过更换Adam优化器、添加Label Smoothing、更换YOLOv5 P6模型等方式进行优化，最终模型mAP@.5:.95为68.5%，模型权重文件大小为14.4M，推理速度在TITAN X上约为6.6 ms。

(3)使用TensorRT SDK对模型进行量化，将网络权重从FP32压缩至INT8，期间随机抽取1000张验证集数据进行FP16至INT8校准，最终在检测精度几乎不变的情况下模型压缩28%，推理速度提升50%。

(4)使用Flask Web对模型进行部署，系统架构采用前后端分离模式，实现通过图形化界面上传并可视化检测结果，最终在服务器本地进行请求与推理时间平均约18ms。

