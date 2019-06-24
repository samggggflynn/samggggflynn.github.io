---
layout: post
#标题配置
title: python图像处理-椒盐噪声
#时间配
date:   2019-06-24 9:31:00 +0800
#大类配置
categories: python
#小类配置
tag: cv笔记
---

* content
{:toc}

---

## 椒盐噪声

椒盐噪声又称为脉冲噪声，它是一种随机出现的白点或者黑点。椒盐噪声 = 椒噪声 + 盐噪声，椒盐噪声的值为0（黑色）或者255（白色），这里假设为等概率的出现0或者255。如果一张推昂的宽 × 高 = 10 × 10，那么它的像素共计100个。

## shape函数 

关于shape：

```python
from numpy import shape
a = array([[1,2,3],[2,3,4],[4,5,6]])
a.shape[0]
````

得到a的行数为4 
然后输入a.shape[1]
得到a的列数为3
也就是说 ，shape[0]用于得到数组的行，shape[1]用于得到数组的列

## 椒盐噪声加噪

代码：
```python
#coding=utf-8
import numpy as np
import cv2
import os, sys

path = "./xc/" #当前目录下的xc文件夹
dirs = os.listdir(path)
print(dirs)
for imagefile in dirs: #一张图一张图进行处理，只能一张图一张图进行显示
    print(imagefile)
    img=cv2.imread(path+imagefile) #路径+图片名读取图片
    def saltpepper(img, n):  # n为噪声比例，即噪点像素占比
        m = int((img.shape[0] * img.shape[1]) * n) # m为噪点数量
        for a in range(m):  # 随机取值赋值为255（加入盐噪声）
            i = int(np.random.random() * img.shape[1]) # 利用随机数（0~1）*图像的宽，产生需要设定黑色或者白色的点坐标（行坐标[0]，列坐标[1]）
            j = int(np.random.random() * img.shape[0])
            if img.ndim == 2:  # 单通道(图像是二维的)
                img[j, i] = 255
            elif img.ndim == 3:  # 三通道（图像是三维的）
                img[j, i, 0] = 255
                img[j, i, 1] = 255
                img[j, i, 2] = 255
                
        for b in range(m):  # 随机取值赋值为0（加入椒噪声）
            i = int(np.random.random() * img.shape[1])
            j = int(np.random.random() * img.shape[0])
            if img.ndim == 2:
                img[j, i] = 0
            elif img.ndim == 3:
                img[j, i, 0] = 0
                img[j, i, 1] = 0
                img[j, i, 2] = 0
        return img  #返回图像，以供显示
    saltImage = saltpepper(img, 0.1)  #调用函数，其中 n=0.1
    cv2.imshow('PepperandSaltImage',img) #显示图像
    cv2.waitKey(0) #等待按键结束  如果不添，在IDLE中执行窗口直接无响应。在命令行中执行的话，则是一闪而过。
    cv2.destroyAllWindows() #销毁窗口释放内存
    cv2.imwrite("./noise_images/"+"noise_"+imagefile,saltImage) #将处理后的图片保存
```

原图：
![](/styles/images/2019-06-24-pepperandsalt/image2.png)

加噪以后：
![](/styles/images/2019-06-24-pepperandsalt/noise_image2.png)

## Python os.listdir() 方法

### 概述
os.listdir() 方法用于返回指定的文件夹包含的文件或文件夹的名字的列表。这个列表以字母顺序。 它不包括 '.' 和'..' 即使它在文件夹中。

只支持在 Unix, Windows 下使用。

### 语法
listdir()方法语法格式如下：

`os.listdir(path)`

### 参数
* path -- 需要列出的目录路径

### 返回值
返回指定路径下的文件和文件夹列表。

## 另一种类似处理方法

```python
import cv2 #导入opencv模块
importnumpy as np #导入numpy模块并改名字为np方便调用

def salt(img, n): #定义函数，给图像随机加白点（输入图像，白点数）
	for k in range(n): #从k=0到k=n，默认以步长1
		i = int(np.random.random()* img.shape[1]); #产生随机数（0~1）*图像的宽
		j = int(np.random.random() *img.shape[0]); #产生随机数（0~1）*图像的高
		if img.ndim == 2: #如果图像是二维的
			img[j,i] = 255
		elif img.ndim == 3: #如果图像是三维的
			img[j,i,0]= 255 #分别给三个通道的对应像素点赋值
			img[j,i,1]= 255
			img[j,i,2]= 255
	return img #返回图像，供显示
	
if __name__ == '__main__':
	img = cv2.imread("图像路径") #读入图像
	saltImage = salt(img, 500) # 调用salt函数
	cv2.imshow("Salt", saltImage) #显示图像
	cv2.waitKey(0) #等待按键结束
	cv2.destroyAllWindows() #销毁窗口释放内存
```