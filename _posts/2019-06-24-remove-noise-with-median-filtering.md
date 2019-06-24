---
layout: post
#标题配置
title: python图像处理-中值滤波（去噪）
#时间配
date:   2019-06-24 14:31:00 +0800
#大类配置
categories: python
#小类配置
tag: cv笔记
---

* content
{:toc}

---

## 中值滤波

` 在图像处理中，在进行如边缘检测这样的进一步处理之前，通常需要首先进行一定程度的降噪。中值滤波是一种非线性数字滤波器技术，经常用于去除图像或者其它信号中的噪声。这个设计思想就是检查输入信号中的采样并判断它是否代表了信号，使用奇数个采样组成的观察窗实现这项功能。观察窗口中的数值进行排序，位于观察窗中间的中值作为输出。然后，丢弃最早的值，取得新的采样，重复上面的计算过程。`
--[wiki]（https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%80%BC%E6%BB%A4%E6%B3%A2%E5%99%A8）

`中值滤波法是一种非线性平滑技术，它将每一像素点的灰度值设置为该点某邻域窗口内的所有像素点灰度值的中值。中值滤波是基于排序统计理论的一种能有效抑制噪声的非线性信号处理技术，中值滤波的基本原理是把数字图像或数字序列中一点的值用该点的一个邻域中各点值的中值代替，让周围的像素值接近的真实值，从而消除孤立的噪声点。方法是用某种结构的二维滑动模板，将板内像素按照像素值的大小进行排序，生成单调上升（或下降）的为二维数据序列。二维中值滤波输出为g（x,y）=med{f(x-k,y-l),(k,l∈W)} ，其中，f(x,y)，g(x,y)分别为原始图像和处理后图像。W为二维模板，通常为3*3，5*5区域，也可以是不同的的形状，如线状，圆形，十字形，圆环形等。`
--[百度百科](https://baike.baidu.com/item/%E4%B8%AD%E5%80%BC%E6%BB%A4%E6%B3%A2)

中值滤波是图像处理中的一个常用步骤，**它对于斑点噪声和椒盐噪声来说尤其有用**。保存边缘的特性使它在不希望出现边缘模糊的场合也很有用。

在去除椒盐噪声方面均值滤波会模糊图像，并且去除效果较差。相比之下，中值滤波能够较好地去除椒盐噪声。
代码实现：
```python
import numpy as np
import cv2
#椒盐噪声
def saltpepper(img,n): #n为噪声比例
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
        if img.ndim==2:                         #image.shape[2]获取图像的通道数
            img[j,i]=0
        elif img.ndim==3:
            img[j,i,0]=0
            img[j,i,1]=0
            img[j,i,2]=0
    return img
img=cv2.imread('li.png')
cv2.imshow('origial',img)
saltImage=saltpepper(img,0.1)
cv2.imshow('saltImage',saltImage)
cv2.imwrite('saltImage.png',saltImage)

#使用自带中值滤波函数
#medianBlur = cv2.medianBlur(img, 3)
#cv2.imshow('img_median', medianBlur)

#中值滤波函数实现
img_copy = cv2.imread('saltImage.png')
for i in range(0,saltImage.shape[0]):
   for j in range(0,saltImage.shape[1]):#取维度
       img_copy[i][j] = saltImage[i][j]
#用3*3的中值滤波器
step=3
def median_filter(x,y):
    sum_s=[]
    for k in range(-int(step/2),int(step/2)+1):
        for m in  range(-int(step/2),int(step/2)+1):
            sum_s.append(saltImage[x+k,y+m,1])
    sum_s.sort()
    return sum_s[(int(step*step/2)+1)] #取中值
for i in range(int(step/2),saltImage.shape[0]-int(step/2)):
    for j in range(int(step/2),img.shape[1]-int(step/2)):
        img_copy[i][j] = median_filter(i,j)

cv2.imshow('img_median', img_copy)
cv2.waitKey(0)
cv2.destroyAllWindows()
```


