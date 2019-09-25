---
layout: post
#标题配置
title:  MTCNN和RetinaFace人脸检测测试报告
date:   2019-09-20 18:35:00 +0800
#大类配置
categories: python
#小类配置
tag: CV笔记
---

* content
{:toc}

---


一、测试背景
------------

人脸检测为目标检测的特例，是商业化最早的目标检测算法，也是目前几乎各大 CV 方向
AI 公司的必争之地。因此对常规通用的人脸检测算法MTCNN（Multi-task Cascaded
Convolutional
Networks，多任务级联卷积网络）和目前最好的开源人脸检测算法RetinaFace进行一系列检测准确率和性能的测试，探究其应用的潜力，为后续项目储备技术。

### 1.1人脸检测技术简介

人脸检测的目标是找出图像中所有的人脸对应的位置，算法的输出是人脸外接矩形在图像中的坐标，可能还包括姿态如倾斜角度等信息。

### 1.2人脸检测技术典型应用

人脸检测是机器视觉领域被深入研究的经典问题，在安防监控、人证比对、人机交互、社交等领域都有重要的应用价值。数码相机、智能手机等端上的设备已经大量使用人脸检测技术实现成像时对人脸的对焦、图集整理分类等功能，各种虚拟美颜相机也需要人脸检测技术定位人脸，然后才能根据人脸对齐的技术确定人脸皮肤、五官的范围然后进行美颜。在人脸识别的流程中，人脸检测是整个人脸识别算法的第一步。

二、测试范围
------------

1.  两种人脸检测技术在Linux与Windows系统平台下，测试记录软件依赖包及其兼容版本要求；

2.  两种人脸检测技术在Linux与Windows系统平台下，测似记录不同硬件环境下的检测精度对比，检测速度对比；

3.  两种人脸检测技术应用于视频流，测试其是否满足实时处理。

三、单张图片测试
----------------

测试MTCNN和RetinaFace
两种人脸检测技术，记录在Windows和Linux平台和不同硬件版本的情况下检测人脸数目和总的检测用时。

### 3.1 MTCNN单张测试

测试图片如下图：

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/01cf4e5d01184313a07576d38d63861c.png)

图 3.1 MTCNN单张测试图片（selfie.png 2048×1150）

#### 3.1.1 Windows-CPU测试

**Windows下CPU环境配置：**

表 3.1 MTCNN单张windows-cpu配置

| CPU环境测试 | MTCNN-Windows-CPU                                                                               |
|-------------|-------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i5-7500 Memory：8GB                                                                 |
| 软件配置    | 系统OS：Windows7 SP1 Python：3.6.5 框架：Tensorflow-cpu 1.12.0                                  |
| 依赖包      | Opencv-python 3.4.4 numpy 1.16.1 scipy 1.1.0 scikit-learn 0.19.1 tensorflow 1.12.0 Pillow 5.1.0 |

兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0,

**测试结果：**

| 默认参数 | 网络阈值        | 影响因子factor | 最小面容minisize | 检测到人脸faces | 测试用时（s） |
|----------|-----------------|----------------|------------------|-----------------|---------------|
| 参数值   | [0.8, 0.8, 0.8] | 0.7            | 20               | 288             | 9.47          |

**BoardingBox人脸框检测结果：**

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/5b3291730f5b5b5335a1a502d1c29e4f.png)

图 3.2 人脸检测框输出（MTCNN-windows-CPU）

主要从网络阈值的影响、影响因子（原始图像的比例跨度）的影响、要检测的最小面容参数影响几个方面，使用控制变量的方法，改变参数设置进行测试。

##### (1)网络阈值的影响测试

网络阈值指的是：人脸框得分阈值，P-Net、R-Net、O-Net三个网络可单独设定阈值，阈值设置的太小，会有很多框通过，也就增加了计算量，还有可能导致最后不是人脸的框错认为人脸，或者同一张人脸被重复标注。

默认测试参数如下：

| 默认参数 | 影响因子factor | 最小面容minisize |
|----------|----------------|------------------|
| 参数值   | 0.7            | 20               |

**改变P-Net阈值，测试结果：**

| P-Net阈值 | 检测到人脸框(faces) | 检测耗时（s） |
|-----------|---------------------|---------------|
| 0.6       | 293                 | 17.45         |
| 0.8       | 288                 | 9.47          |
| 0.9       | 284                 | 6.07          |

**改变R-Net阈值，测试结果：**

| R-Net阈值 | 检测到人脸框(faces) | 检测耗时（s） |
|-----------|---------------------|---------------|
| 0.6       | 318                 | 9.76          |
| 0.8       | 288                 | 9.47          |
| 0.9       | 266                 | 9.14          |

**改变O-Net阈值，测试结果：**

| O-Net阈值 | 检测到人脸框(faces) | 检测耗时（s） |
|-----------|---------------------|---------------|
| 0.6       | 303                 | 9.33          |
| 0.8       | 288                 | 9.47          |
| 0.9       | 263                 | 9.36          |

###### 测试结论

前两个网络P-Net和R-Net的阈值对MTCNN的检测结果影响较大。由MTCNN原理可以知道P-Net和R-Net主要负责人脸框回归，当P-Net和R-Net的阈值增大时，可以检测出更多人脸，但这也意味着增大了错误几率。检测出的人脸越多，检测用时越久。

##### (2)影响因子（原始图像的比例跨度）的影响测试

影响因子（原始图像的比例跨度）‘**factor**’指的是：生成图像金字塔时候的缩放系数,
范围(0,1)，可控制图像金字塔的阶层数的参数之一，越大，阶层越多，计算越多。

默认测试参数如下：

| 默认参数 | 网络阈值        | 最小面容minisize |
|----------|-----------------|------------------|
| 参数值   | [0.8, 0.8, 0.8] | 20               |

**改变影响因子参数，测试结果：**

| 影响因子factor | 检测到人脸框(faces) | 检测耗时（s） |
|----------------|---------------------|---------------|
| 0.6            | 283                 | 6.90          |
| 0.7            | 288                 | 9.47          |
| 0.8            | 295                 | 14.18         |

###### 测试结论

影响因子（原始图像的比例跨度）越大，检测出的人脸越多，检测用时越久。

##### (3)要检测的最小面容参数的影响测试

最小面容指的是：最小可检测图像，该值大小，可控制图像金字塔的阶层数的参数之一，越小，阶层越多，计算越多。

默认测试参数如下：

| 默认参数 | 网络阈值        | 影响因子factor |
|----------|-----------------|----------------|
| 参数值   | [0.8, 0.8, 0.8] | 0.7            |

**改变最小面容参数，测试结果：**

| 最小面容参数 | 检测到人脸框(faces) | 检测耗时（s） |
|--------------|---------------------|---------------|
| 15           | 302                 | 14.49         |
| 20           | 288                 | 9.47          |
| 30           | 235                 | 4.25          |

###### 测试结论

最小面容参数，即最小可检测图像越小，检测出的人脸越多，检测用时越久。

#### 3.1.2 Windows-GPU测试

**Windows下GPU环境配置：**

表 3.2 MTCNN单张windows-gpu配置

| GPU环境测试 | MTCNN-Windows-GPU                                                                                   |
|-------------|-----------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i7-9750H Memory：16GB Graphics card：GeForce RTX 2080 with Max-Q Design 8G              |
| 软件配置    | 系统OS：Windows10 1809 Python：3.7.3 CUDA 10.0 框架：Tensorflow-gpu 1.14.0                          |
| 依赖包      | Opencv-python 4.1.1 numpy 1.16.4 scipy 1.2.1 scikit-learn 0.21.3 tensorflow-gpu 1.14.0 Pillow 6.1.0 |

兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0.

**测试结果：**

| 默认参数 | 网络阈值        | 影响因子factor | 最小面容minisize | 检测到人脸faces | 测试用时（s） |
|----------|-----------------|----------------|------------------|-----------------|---------------|
| 参数值   | [0.8, 0.8, 0.8] | 0.7            | 20               | 288             | 9.22          |

**BoardingBox人脸框检测结果：**

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/5b3291730f5b5b5335a1a502d1c29e4f.png)

图 3.3 人脸检测框输出（MTCNN-windows-GPU）

##### 网络阈值的影响测试

###### 测试结论（同CPU环境）

前两个网络P-Net和R-Net的阈值对MTCNN的检测结果影响较大。由MTCNN原理可以知道P-Net和R-Net主要负责人脸框回归，当P-Net和R-Net的阈值增大时，可以检测出更多人脸，但这也意味着增大了错误几率。检测出的人脸越多，检测用时越久。

##### (2)影响因子（原始图像的比例跨度）的影响测试

###### 测试结论（同CPU环境）

原始图像的比例跨度越大，检测出的人脸越多，检测用时越久。

##### (3)要检测的最小面容参数的影响测试

###### 测试结论（同CPU环境）

要检测的最小面容越小，检测出的人脸越多，检测用时越久。

#### 3.1.3 Linux-GPU测试

**Linux下GPU环境配置：**

表 3.3 MTCNN单张Linux-gpu配置

| GPU环境测试 | MTCNN-Linux-GPU                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® Xeon® Gold 6136 \@ 3.00GHz Memory：64GB Graphics card：GeForce RTX 2080 Ti |
| 软件配置    | 系统OS：Red Hat Enterprise Linux Python：3.6.8 CUDA 9.0 框架：Tensorflow-gpu 1.12.0    |
| 依赖包      | Opencv-python 3.4.4 numpy 1.16.1 scipy 1.2.1 scikit-learn 0.20.2 tensorflow-gpu 1.12.0 |

>   兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0,numpy\<=1.16.2。

**测试结果：**

| 默认参数 | 网络阈值        | 影响因子factor | 最小面容minisize | 检测到人脸faces | 测试用时（s） |
|----------|-----------------|----------------|------------------|-----------------|---------------|
| 参数值   | [0.8, 0.8, 0.8] | 0.7            | 20               | 288             | 9.28          |

**BoardingBox人脸框检测结果：**

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/5b3291730f5b5b5335a1a502d1c29e4f.png)

图 3.4 人脸检测框输出（MTCNN-Linux-GPU）

###### 测试结论

\*网络阈值的影响、影响因子（原始图像的比例跨度）的影响、要检测的最小面容参数影响结论同Windows环境。

#### \*遇到的问题：

MTCNN对单张图片进行人脸检测时，在同样参数的情况下，CPU和GPU处理耗时差距不明显。

分析原因：加载模型需要大量时间，对单张图片cpu和gpu的tensorflow处理速度差距不大。

### 3.2 RetinaFace单张测试

测试图片如下图：

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/be79f7ba5d028266608e5b12697e3b9c.png)

图 3.5 RetinaFace单张测试图片（selfie.png 2048×1150）

#### 3.2.1 windows-cpu测试

**Windows下CPU环境配置：**

表 3.4 RetinaFace单张windows-cpu配置

| CPU环境测试 | RetinaFace-Windows-CPU                                                                        |
|-------------|-----------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i5-7500 Memory：8GB                                                               |
| 软件配置    | 系统OS：Windows7 SP1 Python：3.6.5 框架：MxNet 1.2.0                                          |
| 依赖包      | Opencv-python 3.4.4 numpy 1.16.1 scipy 1.1.0 scikit-learn 0.19.1 mxnet 1.2.0 Cython = 0.29.13 |

兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0.

#####  (1)加载不同预训练模型的影响测试

测试加载标准ResNet50预训练模型与轻量级MobileNet0.25预训练模型，对比人脸检测结果的准确率和用时。

默认测试参数如下：

| 默认参数 | 非极大值抑制（nms） | 网络阈值（thresh） |
|----------|---------------------|--------------------|
| 参数值   | 0.4                 | 0.8                |

**改变加载的模型，测试结果：**

| 加载模型      | 检测到人脸框(faces) | 检测耗时（s） |
|---------------|---------------------|---------------|
| ResNet50      | 426                 | 15.24         |
| MobileNet0.25 | 228                 | 23.17         |

**BoardingBox人脸框检测结果：**

| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image4.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image4.png) | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image4.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image4.png) |
|------------------------------------------|------------------------------------------|


图 3.6 RetinaFace-CPU人脸检测框输出（左边ResNet-50，右边MobileNet0.25）

###### 测试结论

对于相同的极大值抑制参数，人脸检测准确度加载使用‘ResNet-50’模型优于‘MobileNet0.25’，但加载轻量型模型‘MobileNet0.25’单张耗时却增加了。但在后面的连续视频帧测试中发现，轻量模型实际可以在连续视频帧中提高检测速度。

##### (2)不同非极大值抑制参数的影响测试

非极大值抑制（NMS），顾名思义就是抑制不是极大值的元素，搜索局部的极大值。检测中，滑动窗口经提取特征，经分类器分类识别后，每个窗口都会得到一个分类及分数。但是滑动窗口会导致很多窗口与其他窗口存在包含或者大部分交叉的情况。这时就需要用到NMS来选取那些邻域里分数最高（是某类对象的概率最大），并且抑制那些分数低的窗口。

使用‘ResNet-50’模型,测试不同极大值抑制参数，对比人脸检测结果的准确率和用时。

默认测试参数如下：

| 默认参数 | 网络模型  | 网络阈值（thresh） |
|----------|-----------|--------------------|
| 参数值   | ResNet-50 | 0.8                |

**改变非极大值抑制参数（nms），测试结果：**

| 非极大值抑制（nms） | 检测到人脸框(faces) | 检测耗时（s） |
|---------------------|---------------------|---------------|
| 0.2                 | 460                 | 11.03         |
| 0.4                 | 462                 | 10.53         |
| 0.9                 | 957                 | 11.11         |

**BoardingBox人脸框检测结果：**

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/92bdf7a4dc1e3074fbab9057e610d5ff.png)

![G:\\PycharmProjects\\RetinaFace\\detector_test1.jpg](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/93317716e94f8e562ba2900d1b7b10ff.jpg)

图 3.8 RetinaFace-CPU人脸检测框输出（左边nms=0.2，右边nms=0.9）

从上图人脸检测框输出，nms=0.2时没有明显人脸被重复标记，当nms=0.9时可以看到大量单个人脸被重复标记。

###### 测试结论

适当调节极大值抑制参数（nms）可以改变测试结果的准确率，但是能会同样会倒置较多漏检和检测框重复标注的情况。

##### (3)网络阈值影响的测试

在非极大值抑制参数‘nms=0.4’，使用‘ResNet-50’预训练模型时，测试不同网络阈值参数，对比人脸检测结果的人脸框和检测用时。

默认测试参数如下：

| 默认参数 | 网络模型  | 非极大值抑制（nms） |
|----------|-----------|---------------------|
| 参数值   | ResNet-50 | 0.4                 |

**改变网络阈值参数（thresh），测试结果：**

| 网络阈值参数（thresh） | 检测到人脸框(faces) | 检测耗时（s） |
|------------------------|---------------------|---------------|
| 0.6                    | 513                 | 15.60         |
| 0.8                    | 462                 | 11.53         |
| 0.9                    | 430                 | 11.51         |

**BoardingBox人脸框检测结果：**

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/650df8776839a6708de736abf8c47e3e.png)

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/0db33eaa5c1cd2b821328d2a6640f0ec.png)

图 3.11 RetinaFace-CPU人脸检测框输出（thresh=0.6，thresh=0.9）

###### 测试结论

对于网络阈值参数的值，适当降低网络参数阈值thresh，可以检测出更多的小尺度人脸，但也有更大可能带来误判。

#### 3.2.2 windows-gpu测试

**Windows下GPU环境配置：**

表 3.5 RetinaFace单张windows-gpu配置

| GPU环境测试 | MTCNN-Windows-GPU                                                                                   |
|-------------|-----------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i7-9750H Memory：16GB Graphics card：GeForce RTX 2080 with Max-Q Design 8G              |
| 软件配置    | 系统OS：Windows10 1809 Python：3.7.3 CUDA 10.0 框架：MxNet-Cu100 1.5.0                              |
| 依赖包      | Opencv-python 4.1.1 numpy 1.16.4 scipy 1.2.1 scikit-learn 0.21.3 mxnet-cu100 1.5.0 Cython = 0.29.13 |

兼容性解释：兼容python3,OpenCV\>=3.1.0，注意MeNet匹配CUDA版本。

##### (1)加载不同预训练模型的影响测试

###### 测试结论（同CPU 环境）

对于相同的极大值抑制参数，人脸检测准确度加载使用‘ResNet-50’模型优于‘MobileNet0.25’，但加载轻量型模型‘MobileNet0.25’单张耗时却增加了。但在后面的连续视频帧测试中发现，轻量模型实际可以在连续视频帧中提高检测速度。

#####  (2)不同极大值抑制参数的影响测试

###### 测试结论

适当调节极大值抑制参数（nms）可以改变测试结果的准确率，但是能会同样会倒置较多漏检和检测框重复标注的情况。

##### (2)网络阈值影响的测试

###### 测试结论（同CPU 环境）

对于网络阈值参数的值，适当降低网络参数阈值thresh，可以检测出更多的小尺度人脸，但也有更大可能带来误判。

#### 3.2.3 Linux-GPU测试

**Linux下GPU环境配置：**

表 3.6 RetinaFace张Linux-gpu配置

| GPU环境测试 | MTCNN-Linux-GPU                                                                                       |
|-------------|-------------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® Xeon® Gold 6136 \@ 3.00GHz Memory：64GB Graphics card：GeForce RTX 2080 Ti                |
| 软件配置    | 系统OS：Red Hat Enterprise Linux Python：3.6.8 CUDA 9.0 框架：MxNet-Cu90 1.5.0                        |
| 依赖包      | Opencv-python 3.4.4 numpy 1.16.1 graphviz 0.8.1 scikit-learn 0.20.2 mxnet-cu100 1.5.0 request 2.20..1 |

兼容性解释：兼容python3，OpenCV\>=3.1.0， MeNet匹配CUDA版本。

**注意：**

-   **mxnet-cu90
    1.5.0:要求0.9.0\>graphviz\>=0.8.1,numpy\>1.15.0;3.0.0\>requests\>=2.20.0;**

-   **Mxnet-cu90 1.4.1:要求0.9.0\>graphviz\>=0.8.1, 1.15.0numpy\>=1.8.2;**

**测试结果：**

| 默认参数 | 网络模型  | 非极大值抑制（nms） | 网络阈值（thresh） | 检测到人脸faces（张） | 测试用时（s） |
|----------|-----------|---------------------|--------------------|-----------------------|---------------|
| 参数值   | ResNet-50 | 0.4                 | 0.8                | 462                   | 7.61          |

**BoardingBox人脸框检测结果：**

![](/styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/92bdf7a4dc1e3074fbab9057e610d5ff.png)

图 3.14 RetinaFace-Linux-GPU人脸检测框输出

###### 测试结论

\*加载不同预训练模型的影响测试、网络阈值影响的测试，结论同Windows环境。

四、多张测试
------------

### 4.1 MTCNN多张测试

测试图片来自LFW（Labeled Faces in the
Wild）人脸数据库，LFW人脸数据库主要用来研究非受限情况下的人脸识别问题，LFW数据库中的人脸的光照条件、姿态多种多样，有的还存在部分遮挡，因此识别难度较大。

测试500张LFW人脸数据库中的图片，记录MTCNN和RetinaFace在不同测试环境下的检测到人脸数量和测试的总用时。

#### 4.1.1 Windows-CPU测试

**Windows下CPU环境配置（同单张测试）：**

表 4.1 MTCNN多张windows-cpu配置

| CPU环境测试 | MTCNN-Windows-CPU                                                                               |
|-------------|-------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i5-7500 Memory：8GB                                                                 |
| 软件配置    | 系统OS：Windows7 SP1 Python：3.6.5 框架：Tensorflow-cpu 1.12.0                                  |
| 依赖包      | Opencv-python 3.4.4 numpy 1.16.1 scipy 1.1.0 scikit-learn 0.19.1 tensorflow 1.12.0 Pillow 5.1.0 |

兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0,

默认测试参数如下：

| 默认参数 | 网络阈值        | 影响因子factor | 最小面容minisize |
|----------|-----------------|----------------|------------------|
| 参数值   | [0.8, 0.8, 0.8] | 0.7            | 20               |

**测试结果：**

| MTCNN-多张测试  | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|-----------------|--------------------|-----------------|---------------------|----------------------|
| Win-CPU测试结果 | 500                | 21.29           | 578                 | 23.5                 |

#### 4.1.2 Windows-GPU测试

**Windows下GPU环境配置（同单张测试）：**

表 4.2 MTCNN多张windows-gpu配置

| GPU环境测试 | MTCNN-Windows-GPU                                                                                   |
|-------------|-----------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i7-9750H Memory：16GB Graphics card：GeForce RTX 2080 with Max-Q Design 8G              |
| 软件配置    | 系统OS：Windows10 1809 Python：3.7.3 CUDA 10.0 框架：Tensorflow-gpu 1.14.0                          |
| 依赖包      | Opencv-python 4.1.1 numpy 1.16.4 scipy 1.2.1 scikit-learn 0.21.3 tensorflow-gpu 1.14.0 Pillow 6.1.0 |

兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0.

默认测试参数如下：

| 默认参数 | 网络阈值        | 影响因子factor | 最小面容minisize |
|----------|-----------------|----------------|------------------|
| 参数值   | [0.8, 0.8, 0.8] | 0.7            | 20               |

**测试结果：**

| MTCNN-多张测试  | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|-----------------|--------------------|-----------------|---------------------|----------------------|
| Win-GPU测试结果 | 500                | 17.11           | 578                 | 29.2                 |

#### 4.1.3 Linux-GPU测试

**Linux下GPU环境配置（同单张测试）：**

表 4.3 MTCNN多张Linux-gpu配置

| GPU环境测试 | MTCNN-Linux-GPU                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® Xeon® Gold 6136 \@ 3.00GHz Memory：64GB Graphics card：GeForce RTX 2080 Ti |
| 软件配置    | 系统OS：Red Hat Enterprise Linux Python：3.6.8 CUDA 9.0 框架：Tensorflow-gpu 1.12.0    |
| 依赖包      | Opencv-python 3.4.4 numpy 1.16.1 scipy 1.2.1 scikit-learn 0.20.2 tensorflow-gpu 1.12.0 |

>   兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0,numpy\<=1.16.2。

默认测试参数如下：

| 默认参数 | 网络阈值        | 影响因子factor | 最小面容minisize |
|----------|-----------------|----------------|------------------|
| 参数值   | [0.8, 0.8, 0.8] | 0.7            | 20               |

**测试结果：**

| MTCNN-多张测试    | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|-------------------|--------------------|-----------------|---------------------|----------------------|
| Linux-GPU测试结果 | 500                | 18.44           | 564                 | 27.1                 |

#### 测试结论

对500张LFW数据库中的人脸检测，速度约在0.04s/张，推测其可以满足实时视频流检测要求，这在随后的视频流检测测试中得到验证。

### 4.2 RetinaFace多张测试

测试图片来自LFW（Labeled Faces in the
Wild）人脸数据库，LFW人脸数据库主要用来研究非受限情况下的人脸识别问题，LFW数据库中的人脸的光照条件、姿态多种多样，有的还存在部分遮挡，因此识别难度较大。

#### 4.2.1 Windows-CPU测试

**Windows下CPU环境配置（同单张测试）：**

表 4.4 RetinaFace多张windows-cpu配置

| CPU环境测试 | RetinaFace-Windows-CPU                                                                        |
|-------------|-----------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i5-7500 Memory：8GB                                                               |
| 软件配置    | 系统OS：Windows7 SP1 Python：3.6.5 框架：MxNet 1.2.0                                          |
| 依赖包      | Opencv-python 3.4.4 numpy 1.16.1 scipy 1.1.0 scikit-learn 0.19.1 mxnet 1.2.0 Cython = 0.29.13 |

兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0.

**测试结果：**

| RetinaFace-多张测试 | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|---------------------|--------------------|-----------------|---------------------|----------------------|
| Win-CPU测试结果     | 500                | 2831.31         | 678                 | 0.18                 |

测试轻量级网络MobileNet0.25的模型。

**测试结果（MobileNet0.25）：**

| RetinaFace-多张测试           | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|-------------------------------|--------------------|-----------------|---------------------|----------------------|
| Win-CPU-MobileNet0.25测试结果 | 500                | 228.96          | 658                 | 2.2                  |

##### 测试结论

轻量级模型在Windows平台下CPU环境有了十几倍提高，但仍不能满足实时需求。

#### 4.2.2 Windows-GPU测试

**Windows下GPU环境配置（同单张测试）：**

表 4.5 RetinaFace多张windows-gpu配置

| GPU环境测试 | MTCNN-Windows-GPU                                                                                   |
|-------------|-----------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i7-9750H Memory：16GB Graphics card：GeForce RTX 2080 with Max-Q Design 8G              |
| 软件配置    | 系统OS：Windows10 1809 Python：3.7.3 CUDA 10.0 框架：MxNet-Cu100 1.5.0                              |
| 依赖包      | Opencv-python 4.1.1 numpy 1.16.4 scipy 1.2.1 scikit-learn 0.21.3 mxnet-cu100 1.5.0 Cython = 0.29.13 |

兼容性解释：兼容python3,OpenCV\>=3.1.0，注意MeNet匹配CUDA版本。

**测试结果：**

| RetinaFace-多张测试 | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|---------------------|--------------------|-----------------|---------------------|----------------------|
| Win-GPU测试结果     | 500                | 80.30           | 678                 | 6.2                  |

测试轻量级网络MobileNet0.25的模型。

**测试结果（MobileNet0.25）：**

| RetinaFace-多张测试           | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|-------------------------------|--------------------|-----------------|---------------------|----------------------|
| Win-GPU-MobileNet0.25测试结果 | 500                | 44.36           | 11.3                | 11.3                 |

##### 测试结论

轻量级模型在Windows平台下GPU环境检测速度提高近一倍，基本满足实时需求。

#### 4.2.3 Linux-GPU测试

**Linux下GPU环境配置（同单张测试）：**

表 4.6 RetinaFace多张Linux-gpu配置

| GPU环境测试 | MTCNN-Linux-GPU                                                                                       |
|-------------|-------------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® Xeon® Gold 6136 \@ 3.00GHz Memory：64GB Graphics card：GeForce RTX 2080 Ti                |
| 软件配置    | 系统OS：Red Hat Enterprise Linux Python：3.6.8 CUDA 9.0 框架：MxNet-Cu90 1.5.0                        |
| 依赖包      | Opencv-python 3.4.4 numpy 1.16.1 graphviz 0.8.1 scikit-learn 0.20.2 mxnet-cu100 1.5.0 request 2.20..1 |

兼容性解释：兼容python3，OpenCV\>=3.1.0， MeNet需匹配CUDA版本。

**注意：**

-   **mxnet-cu90
    1.5.0:要求0.9.0\>graphviz\>=0.8.1,numpy\>1.15.0;3.0.0\>requests\>=2.20.0;**

-   **Mxnet-cu90 1.4.1:要求0.9.0\>graphviz\>=0.8.1, 1.15.0numpy\>=1.8.2;**

**测试结果（ResNet-50）：**

| RetinaFace-多张测试        | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|----------------------------|--------------------|-----------------|---------------------|----------------------|
| Linux-GPU-ResNet50测试结果 | 500                | 49.73           | 638                 | 10.1                 |

测试轻量级网络MobileNet0.25的模型。

**测试结果（MobileNet0.25）：**

| RetinaFace-多张测试             | 检测图片总数（张） | 检测总耗时（s） | 检测到人脸总数faces | 每秒可检测图片（张） |
|---------------------------------|--------------------|-----------------|---------------------|----------------------|
| Linux-GPU-MobileNet0.25测试结果 | 500                | 23              | 620                 | 21.7                 |

##### 测试结论

轻量级模型在Linux平台下GPU环境检测速度提高近一倍，基本满足实时需求。

#### 测试结论

对500张LFW数据库中的人脸检测，当加载‘ResNet-50’模型测试时，CPU测试速度很慢，速度只有每秒0.15-0.2张，不能满足实时要求。GPU环境测试较快，速度约在每秒6-11张，推测其在GPU环境下基本满足实时视频流检测要求，这在随后的视频流检测测试中得到验证。

当加载轻量级网络的模型‘MobileNet0.25’测试时，无论是Windows平台的CPU或GPU还是Linux平台，速度都有了较大的提升。这样，Windows下速度约在每秒2.2张，还是不能够满足实时要求，但有了十几倍的提升。GPU环境速度可以在每秒11-22张，基本满足实时需求。加载轻量级网络的模型同样也带来了问题，检测出人脸数量减少了，也就是模型的泛化能力不如‘ResNet-50’。

五、从摄像头加载视频流测试
--------------------------

利用Windows笔记本的前置摄像头加载视频流，测试两种人脸检测算法的检测速度和准确率。这里由于测试笔记本只有GPU环境，故只对GPU环境下的视频流进行测试。

### 5.1 MTCNN视频流测试

**Windows下GPU环境配置（同单张测试）：**

表 3.7 MTCNN视频流windows-gpu配置

| GPU环境测试 | MTCNN-Windows-GPU                                                                                   |
|-------------|-----------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i7-9750H Memory：16GB Graphics card：GeForce RTX 2080 with Max-Q Design 8G              |
| 软件配置    | 系统OS：Windows10 1809 Python：3.7.3 CUDA 10.0 框架：Tensorflow-gpu 1.14.0                          |
| 依赖包      | Opencv-python 4.1.1 numpy 1.16.4 scipy 1.2.1 scikit-learn 0.21.3 tensorflow-gpu 1.14.0 Pillow 6.1.0 |

兼容性解释：兼容python3，tensorflow\>=1.4.0,OpenCV\>=3.1.0.

默认测试参数如下：

| 默认参数 | 网络阈值        | 影响因子factor | 最小面容minisize |
|----------|-----------------|----------------|------------------|
| 参数值   | [0.8, 0.8, 0.8] | 0.7            | 20               |

MTCNN在GPU环境下对视频流的测试可以做到实时处理，fps大于20。

| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image8.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image8.png)   | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image9.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image9.png)   |
|--------------------------------------------|--------------------------------------------|
| mtcnn 侧脸检测测试                         | mtcnn 人脸倾斜检测测试                     |
| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image10.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image10.png) | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image11.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image11.png) |
| mtcnn 人脸遮挡检测测试                     | mtcnn 小尺度人脸检测测试                   |
| 图 5.1 mtcnn视频流测试                     |                                            |

##### **测试结论**

MTCNN能够在GPU下做到实时人脸检测，但是在人脸倾斜，人脸有较大遮挡的，以及小尺度人脸检测的过程中会出现漏检的情况。能够满足实时性要求高的应用，不能够满足准确度要求高的应用。

### 5.2 RetinaFace视频流测试

**Windows下GPU环境配置：**

>   表 3.8 RetinaFace视频流windows-gpu配置

| GPU环境测试 | MTCNN-Windows-GPU                                                                                   |
|-------------|-----------------------------------------------------------------------------------------------------|
| 硬件配置    | CPU：Intel® i7-9750H Memory：16GB Graphics card：GeForce RTX 2080 with Max-Q Design 8G              |
| 软件配置    | 系统OS：Windows10 1809 Python：3.7.3 CUDA 10.0 框架：MxNet-Cu100 1.5.0                              |
| 依赖包      | Opencv-python 4.1.1 numpy 1.16.4 scipy 1.2.1 scikit-learn 0.21.3 mxnet-cu100 1.5.0 Cython = 0.29.13 |

兼容性解释：兼容python3,OpenCV\>=3.1.0，注意MeNet匹配CUDA版本。

默认测试参数如下：

| 默认参数 | 非极大值抑制（nms） | 网络阈值（thresh） |
|----------|---------------------|--------------------|
| 参数值   | 0.4                 | 0.8                |

##### (1)MobileNet0.25模型测试

加载轻量级模型‘MobileNet0.25’测试结果：

| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image12.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image12.png) | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image13.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image13.png) |
|--------------------------------------------|--------------------------------------------|
| 侧脸检测测试                               | 人脸倾斜检测测试                           |
| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image14.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image14.png) | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image15.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image15.png) |
| 人脸遮挡检测测试                           | 小尺度人脸检测测试（有漏检）               |
| 图 5.2 RetinaFace+MobileNet0.25视频流测试  |                                            |

针对RetinaFace+MobileNet0.25的小尺度漏检情况，通过调节网络阈值thresh参数可以减轻漏检：

| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image15.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image15.png)        | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image16.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image16.png) |
|---------------------------------------------------|--------------------------------------------|
| 小尺度人脸检测测试（有漏检）                      | 小尺度人脸检测测试（修改阈值，无漏检）     |
| 图 5.3 RetinaFace+MobileNet0.25小尺度修改阈值测试 |                                            |

##### (2)ResNet-50模型测试

加载标准模型‘ResNet-50’测试结果：

| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image17.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image17.png) | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image18.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image18.png) |
|--------------------------------------------|--------------------------------------------|
| 侧脸检测测试                               | 人脸倾斜检测测试                           |
| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image19.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image19.png) | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image20.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image20.png) |
| 人脸遮挡检测测试                           | 小尺度人脸检测测试                         |
| 图 5.4 RetinaFace+ResNet-50视频流测试      |                                            |

另外对于倾斜为180度的倒置图片，使用‘MobileNet0.25’的模型并不能检测到：

| [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image21.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image21.png) | [.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image22.png](.//styles/images/2019-09-20-MTCNN-RetinaFace-faceDetection-test/image22.png) |
|--------------------------------------------|--------------------------------------------|
| 倾斜检测（倒置，MobileNet0.25漏检）        | 倾斜检测（倒置，ResNet-50无漏检）          |
| 图 5.5 不同模型下倾斜（倒置）测试          |                                            |

##### **测试结论**

RetinaFace测试了两个预训练模型‘ResNet-50’和‘MobileNet0.25’。在视频流测试中，加载轻量模型‘MobileNet0.25’可以提高一倍左右的视频帧数。测试发现GPU环境下，‘ResNet-50’帧数在每秒5-8帧，‘MobileNet0.25’
帧数在每秒10-12帧，基本满足实时要求。在检测准确率方面‘ResNet-50’模型要优于轻量级的‘MobileNet0.25’模型，主要表现在人脸倾斜度较大时和小尺度人脸的检测上；但在实时性上‘MobileNet0.25’要优于‘ResNet-50’模型。

六、测试总结
------------

在对单张图片进行人脸检测测试时，使用MTCNN对单张图片进行人脸检测，在同样参数的情况下，CPU和GPU处理耗时差距不明显。主要原因时第一次启动加载模型需要大量时间，这在多张测试中看到
，无论测试环境中的哪个平台或硬件都可以到达每秒20张以上的检测速度，足以满足实时检测需求。

影响MTCNN单张测试结果的准确度和测试用时的主要因素为：网络阈值、影响因子（原始图像的比例跨度）、要检测的最小面容参数。

对于RetinaFace，适当调节极大值抑制参数（nms）可以改变测试结果的准确率。对于相同的极大值抑制参数，加载轻量型模型MobileNet0.25单张耗时却增加了，检测准确率也下降了。但在后面的连续视频帧测试中发现，轻量模型可以在连续视频帧中提高检测速度。对于网络阈值参数的值，适当调节可以改变测试结果的准确率。但对处理用时影响不大。

因此，影响Retinaface单张测试的准确度的主要因素是，加载不同的预训练模型和网络阈值；影响速度的主要因素是加载不同预训练模型。在windows平台CPU硬件环境中甚至有十倍以上的速度提升（每秒0.18张到每秒2.2张），其中‘ResNet-50’的单张测试准确度优于‘MobileNet0.25’。

总的来说，由于测试单张需要耗费大量时间加载模型，因此速度在这里没有意义。单张测试的准确度（仅对测试图片，默认参数而言）：Retinaface
+‘ResNet-50’模型\>MTCNN\> Retinaface +‘MobileNet0.25’。

在视频流测试中，MTCNN能够在GPU下做到实时人脸检测（CPU下也可以基本做到实时），但是在人脸倾斜，人脸有较大遮挡的，以及小尺度人脸检测的过程中会出现漏检的情况。

RetinaFace在加载轻量‘MobileNet0.25’模型时比加载‘ResNet-50’模型检测速度更快。GPU环境下，‘ResNet-50’帧数在每秒5-8帧，‘MobileNet0.25’
帧数在每秒10-12帧，基本满足实时要求。在检测准确率方面‘ResNet-50’模型要优于轻量级的‘MobileNet0.25’模型，主要表现在人脸倾斜度较大时和小尺度人脸的检测上；但在实时性上‘MobileNet0.25’要优于‘ResNet-50’模型。

对于视频检测来说，检测准确度：Retinaface +‘ResNet-50’模型\> Retinaface
+‘MobileNet0.25’\>MTCNN。检测速度（实时性）：MTCNN \> Retinaface
+‘MobileNet0.25’ \>Retinaface +‘ResNet-50’模型。
