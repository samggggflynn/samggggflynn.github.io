---
layout: post
#标题配置
title: OpenCV-Python图像加椒盐噪声与去噪算法对比
date:   2019-06-27 10:35:00 +0800
#大类配置
categories: python
#小类配置
tag: opencv笔记
---

* content
{:toc}

---

（本项目涉及图像数字化、图像高斯平滑、均值平滑、中值平滑和双边滤波）
## 1、目标：
了解图像的基本原理，学会使用numpy对数组进行操作，结合Python和opencv对图像进行处理。了解椒盐噪声的特点并对图片加上椒盐噪声，同时使用多种图像去噪算子去噪并将结果进行对比。

## 2、椒盐噪声
椒盐噪声也称为脉冲噪声，是图像中经常见到的一种噪声，它是一种随机出现的白点或者黑点，可能是亮的区域有黑色像素或是在暗的区域有白色像素（或是两者皆有）。椒盐噪声的成因可能是影像讯号受到突如其来的强烈干扰而产生、类比数位转换器或位元传输错误等。

	给图片加上椒盐噪声的原理：随机取一张图像（分为单通道图与双通道图）的像素点，给该随机的每个通道的像素值赋值为0（黑）或者255（白），核心代码如下：

```python
def saltpepper(img,n): #n为噪声比例，n的范围为[0,1],n越大噪声越多
    m=int((img.shape[0]*img.shape[1])*n)
    for a in range(m): #随机取值赋值为255（白色）
        i=int(np.random.random()*img.shape[1])
        j=int(np.random.random()*img.shape[0])
        if img.ndim==2: #单通道
            img[j,i]=255
        elif img.ndim==3: #三通道
            img[j,i,0]=255
            img[j,i,1]=255
            img[j,i,2]=255
    for b in range(m): #随机取值赋值为0（黑色）
        i=int(np.random.random()*img.shape[1]) #image.shape[0]获取图像的高度
        j=int(np.random.random()*img.shape[0]) #image.shape[1]获取图像的宽度
        if img.ndim==2:                  #image.shape[2]获取图像的通道数
            img[j,i]=0
        elif img.ndim==3:
            img[j,i,0]=0
            img[j,i,1]=0
            img[j,i,2]=0
    return img
```

效果图展示：

原图：

![原图](/styles/images/2019-06-26-summary-image-filter/original.png)

加入椒盐噪声：

![噪声](/styles/images/2019-06-26-summary-image-filter/noise.png)
 

## 3、去噪算子

### 3.1均值滤波

均值滤波是典型的线性滤波算法，它是指在图像上对目标像素给一个模版，该模版包括了周围的临近像素，再用模版中的全体像素的均值来代替原像素值。

均值是由一个归一化卷积框完成的，用卷积框覆盖区域所有像素的均值来代替中心元素值。
但是均值滤波本身存在这固有的缺陷，它不能很好的保护图像细节，在图像去噪的同时也破坏了图像的细节部分，从而使图像变得模糊，不能很好的去除噪点。

`cv2.blur(scr,ksize,dst,anchor,borderType)`

	scr：待处理的原图像
	
	ksize:卷积核大小
	
	dst:目标图像，一般默认值为None
	
	anchor:锚点位置，缺省值表示位于模糊核的正中心
	
	borderType：边界类型，默认值为None
	
### 3.2高斯滤波

  高斯滤波是一种线性平滑滤波，适用于消除高斯噪声，高斯滤波对整幅图像进行加权平均，每个像素的值都由其本身和邻域内的其他像素值经过加权平均后得到。
  
高斯滤波的具体操作是：用一个卷积框扫描图像中的每一个像素，用卷积框确定邻域内像素的加权平均灰度值去代替卷积框像素中心点的像素值。

高斯滤波后图像被平滑的程度取决于标准差，他的输出是邻域像素的加权平均，同时离中心越近的像素权重越高，因此，相对于均值滤波，它的平滑效果更加柔和且边缘保留也更好。

`cv2.GassianBlur(scr,ksize,sigmaX,dst,sigmaY,borderType)`

	scr：待处理的原图像
	
	ksize:卷积核大小
	
	dst:目标图像，一般默认值为None 
	
	sigmaX、sigmaY:X、Y方向上的方差，一般默认设置为None
	
	borderType：边界类型，默认值为None
	
### 3.3中值滤波

中值滤波是一种非线性平滑技术，它将每个像素点的灰度值设置为该点某邻域窗口内的所有像素点的灰度值，中值滤波的基本原理是把图像像素点的值用该点的一个邻域中各点值的中值代替，让周围的像素值接近真实值，从而孤立噪声点。

`cv2.medianBlur(scr,ksize,dst)`

	scr：待处理的原图像
	
	ksize:卷积核大小
	
	dst:目标图像，一般默认值为None 
	
### 3.4双边滤波

双边滤波是一种非线性的滤波方法，是结合图像的空间邻近度和像素值双边滤波（Bilateral filter）是一种非线性的滤波方法，是结合图像的空间邻近度和像素值相似度的一种折衷处理，同时考虑空域信息和灰度相似性，达到保边去噪的目的。具有简单、非迭代、局部的特点。 

双边滤波器的好处是可以做边缘保存（edge preserving），一般过去用的维纳滤波或者高斯滤波去降噪，都会较明显地模糊边缘，对于高频细节的保护效果并不明显。但是由于保存了过多的高频信息，对于彩色图像里的高频噪声，双边滤波器不能够干净的滤掉，只能够对于低频信息进行较好的滤波。

`cv2.bilateralFilter(src, d, sigmaColor, sigmaSpace, dst, borderType)`

	d:像素邻域直径，若为非正值，则从sigmaSpace自动计算得出。一般建议取d=5用于实时图像处理，d=9用于非实时图像邻域
	
	sigmaColor：颜色空间的标注方差，一般在10~150之间
	
	sigmaSpace：坐标空间的标准差，一般取值在10~150之间
	
### 3.4二维卷积

Opencv提供的一个通用的2D滤波函数为cv2.filter2D()，滤波函数的使用需要一个核模板，对图像的滤波操作过程为：将和模板放在图像的一个像素A上，求与之对应的图像上的每个像素点的和，核不同，得到的结果不同，而滤波的使用核心也是对于这个核模板的使用。

`cv2.filter2D(scr,dst,int ddepth,kernel,anchor)`

	ddepth:目标图像深度，当值为-1时，输出图与原图深度一致
	
	Kernel:卷积核（或者是相关核）,一个单通道浮点型矩阵。如果想在图像不同的通道使用不同的kernel，可以先使用split()函数将图像通道事先分开。
	
### 3.5各滤波器结果展示：

二维卷积： 

![](/styles/images/2019-06-26-summary-image-filter/result0.png)

均值滤波：

![](/styles/images/2019-06-26-summary-image-filter/result1.png)

高斯滤波：

![](/styles/images/2019-06-26-summary-image-filter/result2.png)

中值滤波：

![](/styles/images/2019-06-26-summary-image-filter/result3.png)

双边滤波：

![](/styles/images/2019-06-26-summary-image-filter/result4.png)
 
由滤波结果可见：中值滤波对椒盐噪声作为适用，其他滤波器对图像边缘保留效果差。

## 4、应用场景
实际应用中因为图像采集设备、自然环境因素等诸多原因，导致所处理的图像和“本真”图像有差异，这一部分差异就是噪声。

需要在“本真”图像上加上噪声，才能达到类似于“实际”图像的效果，也只有考虑了噪声，算法仿真结果才具有说服力。

图像平滑是一种图像空间滤波方法（低通滤波），可以对图像进行去噪或者模糊化，提高图像信噪比，最大限度保持图像的原有信号，用于图像增强。

均值滤波、高斯滤波都属于线性滤波，速度相对较快，在图像存在斑点和椒盐噪声时优先使用中值滤波，想要去除噪点的同时尽可能保留更多的边缘信息，可以使用双边滤波。

-----
## 完整代码

完整代码如下：

```python
import cv2
import numpy as np

img = cv2.imread("noise.png") #路径+图片名读取图片
cv2.imshow("noise",img) #显示噪声图
# 二维卷积滤波
kernel = np.ones((5,5), np.float32)/25  #滤波器
img_2Dfilter = cv2.filter2D(img, -1, kernel)     #卷积
cv2.imwrite('result0.png',img_2Dfilter) #将处理后的图片保存
# 均值滤波
img_mean = cv2.blur(img, (5,5))
cv2.imwrite('result1.png',img_mean) #将处理后的图片保存
# 高斯滤波
img_Guassian = cv2.GaussianBlur(img,(5,5),0)
cv2.imwrite('result2.png',img_Guassian) #将处理后的图片保存
# 中值滤波
img_median = cv2.medianBlur(img, 5)
cv2.imwrite('result3.png',img_median) #将处理后的图片保存
# 双边滤波
img_bilater = cv2.bilateralFilter(img,9,75,75)
cv2.imwrite('result4.png',img_bilater) #将处理后的图片保存
# 展示不同的图片
cv2.imshow("img_mean",img_2Dfilter)
cv2.imshow("img_mean",img_mean)
cv2.imshow("img_Guassian",img_Guassian)
cv2.imshow("img_median",img_median)
cv2.imshow("img_bilater",img_bilater)

cv2.waitKey(0)
```


