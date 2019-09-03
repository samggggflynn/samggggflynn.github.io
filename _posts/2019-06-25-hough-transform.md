---
layout: post
#标题配置
title: OpenCV-Python的文本透视矫正与水平矫正（霍夫变换）
#时间配
date:   2019-06-25 14:31:00 +0800
#大类配置
categories: python
#小类配置
tag: cv笔记
---

* content
{:toc}

---

## 一、透视矫正

透视矫正是什么？比如发现了一页纸，写的挺好，你想拍下来，但是拍摄时角度的问题，你拍成了下面左边的样子，但是你本来想的是从纸的正上方去拍，所以你要想办法去把照片矫正成右边的样子，矫正的过程称为透视矫正。
![](/styles/images/2019-06-25-hough-transform/1.png)![](/styles/images/2019-06-25-hough-transform/2.png)

所以为了透视矫正，首先需要的就是找到这张纸的轮廓，然后按照从大小的顺序排列，排好之后，找出最大的轮廓，如果判定找到的最大轮廓有四个点，则可以判定我们找到这张纸，然后用四点透视变换获得纸张的俯视图。

写好的函数是这样：

```python
# 透视矫正
def perspective_transformation(img):
    # 读取图像，做灰度化、高斯模糊、膨胀、Canny边缘检测
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    blurred = cv2.GaussianBlur(gray, (5, 5), 0)
    dilate = cv2.dilate(blurred, cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3)))
    # edged = cv2.Canny(dilate, 75, 200)
    edged = cv2.Canny(dilate, 30, 120, 3)
 
    cnts = cv2.findContours(edged.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    cnts = cnts[0] if imutils.is_cv2() else cnts[1]  # 判断是OpenCV2还是OpenCV3
    docCnt = None
 
    # 确保至少找到一个轮廓
    if len(cnts) > 0:
        # 按轮廓大小降序排列
        cnts = sorted(cnts, key=cv2.contourArea, reverse=True)
        for c in cnts:
            # 近似轮廓
            peri = cv2.arcLength(c, True)
            approx = cv2.approxPolyDP(c, 0.02 * peri, True)
            # 如果我们的近似轮廓有四个点，则确定找到了纸
            if len(approx) == 4:
                docCnt = approx
                break
 
    # 对原始图像应用四点透视变换，以获得纸张的俯视图
    paper = four_point_transform(img, docCnt.reshape(4, 2))
    return paper
```

然后，你找到纸以后，可能会发现上面的字会有一点点倾斜，虽然感觉上是没什么问题，但是在识别的过程中会因为这一点点的倾斜导致识别串行。数据分析的过程就会比较麻烦。所以尽可能在预处理过程中就避免这类问题的发生。

## 二、文本水平矫正
其实，这也叫做文本的水平矫正。毕竟跟普通的水平矫正还不太一样，普通的水平矫正图像都会带有自己的边缘，根据边缘可以提取出一个mask，然后进行旋转即可。文本图像的背景是白色的，所以我们没有办法像人民币发票那类有明显边界的矩形物体那样，提取出轮廓并旋转矫正。这里我们用**基于直线探测的水平矫正**。

**霍夫变换算法思想**：以直线检测为例，每个像素坐标点经过变换都变成都直线特质有贡献的统一度量，一个简单的例子如下：一条直线在图像中是一系列离散点的集合，通过一个直线的离散极坐标公式，可以表达出直线的离散点几何等式如下：

![](/styles/images/2019-06-25/3.png)

然而在实现的图像处理领域，图像的像素坐标P(x, y)是已知的，而r, theta则是我们要寻找的变量。如果我们能绘制每个(r, theta)值根据像素点坐标P(x, y)值的话，那么就从图像笛卡尔坐标系统转换到极坐标霍夫空间系统，这种从点到曲线的变换称为直线的霍夫变换。变换通过量化霍夫参数空间为有限个值间隔等分或者累加格子。当霍夫变换算法开始，每个像素坐标点P(x, y)被转换到(r, theta)的曲线点上面，累加到对应的格子数据点，当一个波峰出现时候，说明有直线存在。

所以我们的思路是：

1. 用霍夫线变换探测出图像中的所有直线

2. 计算出每条直线的倾斜角，求他们的平均值

3. 根据倾斜角旋转矫正

给出完整代码：

```Python
# coding=utf-8
import cv2
import numpy as np
 
input_img_file = "./test/test.png"
 
# 度数转换
def DegreeTrans(theta):
    res = theta / np.pi * 180
    return res
 
# 逆时针旋转图像degree角度（原尺寸）
def rotateImage(src, degree):
    # 旋转中心为图像中心
    h, w = src.shape[:2]
    # 计算二维旋转的仿射变换矩阵
    RotateMatrix = cv2.getRotationMatrix2D((w/2.0, h/2.0), degree, 1)
    print(RotateMatrix)
    # 仿射变换，背景色填充为白色
    rotate = cv2.warpAffine(src, RotateMatrix, (w, h), borderValue=(255, 255, 255))
    return rotate
 
# 通过霍夫变换计算角度
def CalcDegree(srcImage):
    midImage = cv2.cvtColor(srcImage, cv2.COLOR_BGR2GRAY)
    dstImage = cv2.Canny(midImage, 50, 200, 3)
    lineimage = srcImage.copy()
 
    # 通过霍夫变换检测直线
    # 第4个参数就是阈值，阈值越大，检测精度越高
    lines = cv2.HoughLines(dstImage, 1, np.pi/180, 300)###如果第4个参数过小，有些（例如垂直）无法矫正
    # 由于图像不同，阈值不好设定，因为阈值设定过高导致无法检测直线，阈值过低直线太多，速度很慢
    sum = 0
    # 依次画出每条线段
    for i in range(len(lines)):
        for rho, theta in lines[i]:
            # print("theta:", theta, " rho:", rho)
            a = np.cos(theta)
            b = np.sin(theta)
            x0 = a * rho
            y0 = b * rho
            x1 = int(round(x0 + 1000 * (-b)))
            y1 = int(round(y0 + 1000 * a))
            x2 = int(round(x0 - 1000 * (-b)))
            y2 = int(round(y0 - 1000 * a))
            # 只选角度最小的作为旋转角度
            sum += theta
            cv2.line(lineimage, (x1, y1), (x2, y2), (0, 0, 255), 1, cv2.LINE_AA)
            cv2.imshow("Imagelines", lineimage)
 
    # 对所有角度求平均，这样做旋转效果会更好
    average = sum / len(lines)
    angle = DegreeTrans(average) - 90
    return angle
 
if __name__ == '__main__':
    image = cv2.imread(input_img_file)
    cv2.imshow("Image", image)
    # 倾斜角度矫正
    degree = CalcDegree(image)
    print("调整角度：", degree)
    rotate = rotateImage(image, degree)
    cv2.imshow("rotate", rotate)
    cv2.imwrite("./test/recified.png", rotate, [int(cv2.IMWRITE_PNG_COMPRESSION), 0])
    cv2.waitKey(0)
    cv2.destroyAllWindows()
```

原图：

![](/styles/images/22019-06-25-hough-transform/test.png)

画出霍夫线：

![](/styles/images/2019-06-25-hough-transform/houghlines.jpg)

矫正（纠偏）以后：

![](/styles/images/2019-06-25-hough-transform/recified.png)


