---
layout: post
#标题配置
title:  OpenCV-Python 发票去印章
date:   2019-07-08 18:35:00 +0800
#大类配置
categories: python
#小类配置
tag: opencv笔记
---

* content
{:toc}

---

## 图像处理-发票去印章

摘要：对拍照后进行矫正之后的发票，发现发票印章部分遮挡文字，于是想到去除发票印章。本文以“去除图像中的红色印章”为研究对象，应用颜色特征，将图像的前景从背景中分离。

![原图](/styles/images/2019-07-08-remove-water-print/original.png)
 
上图是一带印章的发票单，红色的印章遮挡了部分内容。首先想到的肯定移除红色像素点的方法，这种方法需要查到红色的颜色范围，然后遍历全图像素点，在范围内的像素点就将它设置为白色。这种方法用起来其实不太好，毕竟这个“红色范围”的设定还是蛮困难的一件事。
思路：
1.	对彩色图分离通道，拿到红色通道图
2.	进行阈值分割

分离颜色通道：

```python
B_channel,G_channel,R_channel=cv2.split(image)        
# 注意cv2.split()返回通道顺序（因为是按照BGR读取的）

cv2.imshow('Blue channel',B_channel)
cv2.imshow('Green channel',G_channel)
cv2.imshow('Red channel',R_channel)
```

红色通道：

![Red](/styles/images/2019-07-08-remove-water-print/red_channel.png)

绿色通道：
![Green](/styles/images/2019-07-08-remove-water-print/green_channel.png)
 
蓝色通道
![Blue](/styles/images/2019-07-08-remove-water-print/blue_channel.png)

从上面各通道的图像看出，每个通道的图像是略有不同，不同的地方就在于对不同颜色的敏感度不同。看一下红色通道的图，我们发现原图中的红色基本不见了。总结一下就是，原图中颜色越接近红色的地方在红色通道越接近白色。在纯红的地方在红色通道会出现纯白。绿色、蓝色也是同样的道理。
	我们发现：印章像素为白色，灰度值接近255；纸的像素为灰色(接近白色)，灰度值接近255；字的像素为黑色，灰度值接近0。
此时，我们就可以通过一个阈值,将前景像素(字)提取出来。
公式为：
 
绘制关于红色通道的灰度统计直方图：

```python
pixelSequence=R_channel.reshape([rows*cols,1])    # 红色通道的histgram 变换成一维向量
numberBins=256                                    # 统计直方图的组数
plt.figure()                                      # 计算直方图
'''
返回的Figure实例会被传递给后端的新图像管理器new_figure_manager，
这将允许定制的Figure类到pylab接口，
额外饿参数会被传递给图形初始化函数
'''
manager = plt.get_current_fig_manager()
histogram,bins,patch=plt.hist(pixelSequence,
                              numberBins,
                              facecolor='red',
                              histtype='bar')     # facecolor设置为黑色
```

绘制结果输出如下图所示，红色通道直方图中，明显存在两个集中区域，左侧的区域灰度值较低，对应颜色为黑色，是前景像素（字）；右侧集中区域灰度值较高，对应颜色为白色区域，是背景像素（印章和纸）。
在这两个灰度集中的区域的中间部分，设定一个阈值，可以将这两个区域分割开来。这里设置阈值为160，阈值的设定对于最终的处理结果具有较大影响。

![zhifang](/styles/images/2019-07-08-remove-water-print/zhifang.png)

最终对红色通道进行阈值分割之后的结果如下图所示：
 
![result](/styles/images/2019-07-08-remove-water-print/result.png)

总结过程为：

1.	读取原始图像
2.	提取图像的红色通道，得到红色通道灰度值
3.	计算红色通道的统计直方图，确定最佳的阈值threshold
4.	根据阈值，对红色通道图进行二值化，得到最终图片
5.	（可以选择加入膨胀）

完整代码：
```python
# -*- coding: utf-8 -*-
"""
@title: 去除印章
"""
import cv2
import numpy as np
import matplotlib.pyplot as plt

image=cv2.imread("src/input1.png",cv2.IMREAD_COLOR)   # 以BGR色彩读取图片
# image = cv2.resize(image0,None,fx=0.5,fy=0.5,interpolation=cv2.INTER_CUBIC)  # 缩小图片0.5倍（图片太大了）
cols, rows, dims =image.shape                         # 获取图片高、宽、维度
B_channel,G_channel,R_channel=cv2.split(image)        # 注意cv2.split()返回通道顺序（因为是按照BGR读取的）

cv2.imshow('Blue channel',B_channel)
cv2.imshow('Green channel',G_channel)
cv2.imshow('Red channel',R_channel)

pixelSequence=R_channel.reshape([rows*cols,1])    # 红色通道的histgram 变换成一维向量
numberBins=256                                    # 统计直方图的组数
plt.figure()                                      # 计算直方图
'''
返回的Figure实例会被传递给后端的新图像管理器new_figure_manager，
这将允许定制的Figure类到pylab接口，
额外饿参数会被传递给图形初始化函数
'''
manager = plt.get_current_fig_manager()
histogram,bins,patch=plt.hist(pixelSequence,
                              numberBins,
                              facecolor='red',
                              histtype='bar')     # facecolor设置为黑色
'''
matplotlib.pyplot.hist(
    x, bins=None, range=None, 
    density=None, weights=None, cumulative=False, 
    bottom=None, histtype='bar', align='mid', 
    orientation='vertical', rwidth=None, log=False, 
    color=None, label=None, stacked=False, normed=None, 
    hold=None, data=None, **kwargs)
    参数：
    x : (n,) n维数组或者n维数组序列，多维数组长度不要求一致
    bins : 整数，序列，或者 ‘auto’, 可选
    color:颜色
    histtype : {‘bar’, ‘barstacked’, ‘step’, ‘stepfilled’}, 默认"bar",
'''
# 设置坐标范围
y_maxValue=np.max(histogram)
plt.axis([0,255,0,y_maxValue])
'''
    参数一：X轴坐标最小值
    参数二：X轴坐标最大值
    参数三：Y轴坐标最小值
    参数四：Y轴坐标最大值
    
'''
# 设置坐标轴
plt.xlabel("gray Level",fontsize=20)
plt.ylabel('number of pixels',fontsize=20)
plt.title("Histgram of red channel", fontsize=25)
plt.xticks(range(0,255,10))
# 显示直方图
# plt.pause(0.05)

# 保存直方图
plt.savefig("histgram.png", dpi=None, bbox_inches=None)
'''
参数(fname, dpi=None, facecolor=’w’, edgecolor=’w’, 
orientation=’portrait’, papertype=None, format=None, 
transparent=False, bbox_inches=None, pad_inches=0.1, 
frameon=None)
'''
plt.show()

# 红色通道阈值(调节好函数阈值为160-190时效果最好，太小显示不完整，太大黑色太多)
dims, RedThresh = cv2.threshold(R_channel,160,255,0)  # THRESH_BINARY二元阈值
'''
像素高于阈值时，给像素赋予新值，否则，赋予另外一种颜色
HRESH_BINARY二元阈值

cv2.threshold (src, thresh, maxval, type)
    第一个原图像，
    第二个进行分类的阈值，
    第三个是高于（低于）阈值时赋予的新值，
    第四个是一个方法选择参数，常用的有： 
    --------------------------------------------
    • cv2.THRESH_BINARY（黑白二值）       （或者0)
    • cv2.THRESH_BINARY_INV（黑白二值反转）(或者1） 
    • cv2.THRESH_TRUNC （得到的图像为多像素值） 
    • cv2.THRESH_TOZERO 
    • cv2.THRESH_TOZERO_INV 
    --------------------------------------------
    或者：
    --------------------------------------------
    阈值 |小于阈值的像素点  |大于阈值的像素点
    0      |置0                |置填充色
    1      |置填充色          |置0
    2      |保持原色          |置灰色
    3      |置0                |保持原色
    4      |保持原色          |置0
    --------------------------------------------
该函数有两个返回值：
第一个retVal（得到的阈值值（在后面一个方法中会用到）），
第二个就是阈值化后的图像数组。 
'''

# 显示效果
cv2.imshow('original color image',image)
cv2.imshow("RedThresh",RedThresh)

# 保存图像
cv2.imwrite('scale_image.jpg',image)
cv2.imwrite('RedThresh.jpg',RedThresh)

# 膨胀操作（可以省略）
# element = cv2.getStructuringElement(cv2.MORPH_RECT,(3, 3))
# erode = cv2.erode(RedThresh, element)
# cv2.imshow("erode",erode)
# cv2.imwrite("erode.jpg",erode)

cv2.waitKey(0)
cv2.destroyAllWindows()
```

另外一种转为HSV颜色后使用mask（掩膜），利用inpaint图像修复函数的去印章或水印

```python
import cv2
import numpy as np

np.set_printoptions(threshold=np.inf)
'''
set_printoptions(precision=None, threshold=None, edgeitems=None, linewidth=None, suppress=None, nanstr=None, infstr=None)
precision : int, optional，float输出的精度，即小数点后维数，默认8
threshold : int, optional，当数组数目过大时，设置显示几个数字，其余用省略号
edgeitems : int, optional，边缘数目
linewidth : int, optional，The number of characters per line for the purpose of inserting line breaks (default 75).
suppress : bool, optional，是否压缩由科学计数法表示的浮点数
nanstr : str, optional，String representation of floating point not-a-number (default nan).
infstr : str, optional，String representation of floating point infinity (default inf).
np.set_printoptions(threshold=np.nan) 
'''
image = cv2.imread('src/input1.png')

hsv_image = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)  # RGB转HSV

low_range = np.array([150, 103, 100])
# high_range = np.array([180, 255, 255])
# low_range = np.array([156, 43, 46])
high_range = np.array([180, 255, 255])

th = cv2.inRange(hsv_image, low_range, high_range)
'''
cv2.inRange(hsv, lower_red, upper_red) #lower20===>0,upper200==>0
第一个参数：hsv指的是原图
第二个参数：lower_red指的是图像中低于这个lower_red的值，图像值变为0
第三个参数：upper_red指的是图像中高于这个upper_red的值，图像值变为0
而在lower_red～upper_red之间的值变成255
'''
index1 = th == 255
# 给mask加膨胀操作（可以省略）
element = cv2.getStructuringElement(cv2.MORPH_RECT,(3, 3))
dilate_mask = cv2.dilate(th, element)
cv2.imshow("dilate",dilate_mask )
# cv2.imwrite("dilate.jpg",dilate)
# cv2.imshow('mask', dilate)
# cv2.imwrite('mask.png', dilate)

mask = cv2.imread('mask.png',0)
img = np.zeros(image.shape, np.uint8)
img[:, :] = (255,255,255)  # 背景全白
img[index1] = image[index1]  # (0,0,255)

dst = cv2.inpaint(image, dilate_mask , 3, cv2.INPAINT_TELEA)
# dst = cv2.inpaint(image, th, 3, cv2.INPAINT_NS)
'''
dst = cv2.inpaint（src，mask, inpaintRadius，flags）
参数：
    src：输入8位1通道或3通道图像。
    inpaintMask：修复掩码，8位1通道图像。非零像素表示需要修复的区域。
    dst：输出与src具有相同大小和类型的图像。
    inpaintRadius：算法考虑的每个点的圆形邻域的半径。
    flags：
        INPAINT_NS基于Navier-Stokes的方法
        Alexandru Telea的INPAINT_TELEA方法
'''

cv2.imshow('original_img', image)
cv2.imshow('extract_img', img)
cv2.imshow('dst',dst)
# print(cv2.__version__)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
