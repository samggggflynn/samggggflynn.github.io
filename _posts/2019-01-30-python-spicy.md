---
layout: post
#标题配置
title: Python-SciPy笔记
#时间配置
date:   2019-01-31 23:43:00 +0800
#大类配置
categories: Python-SciPy
#小类配置
tag: 笔记
---

* content
{:toc}
## SciPy
Numpy提供了高性能的多维数组，以及计算和操作数组的基本工具。SciPy基于Numpy，提供了大量的计算和操作数组的函数，这些函数对于不同类型的科学和工程计算非常有用。

熟悉SciPy的最好方法就是阅读[SciPy文档](https://docs.scipy.org/doc/scipy/reference/index.html)。我们会强调对于本课程有用的部分。
```Python
from scipy.misc import imread, imsave, imresize
# 从模块中导入图片读 图片存 图片重新整型函数

# Read an JPEG image into a numpy array
# 将JPEG图像读入numpy数组
img = imread('assets/cat.jpg')
print(img.dtype, img.shape)  # Prints "uint8 (400, 248, 3)"

# 我们可以通过缩放每个颜色通道来着色图像，通过不同的标量常量。
# 图像具有形状（400,248,3）我们将它乘以 数组[1,0.95,9.9]（3，）；
# numpy广播意味着这会使红色通道保持不变，并分别将绿色和蓝色通道乘以0.95和0.9
# We can tint the image by scaling each of the color channels
# by a different scalar constant. The image has shape (400, 248, 3);
# we multiply it by the array [1, 0.95, 0.9] of shape (3,);
# numpy broadcasting means that this leaves the red channel unchanged,
# and multiplies the green and blue channels by 0.95 and 0.9
# respectively.
img_tinted = img * [1, 0.95, 0.9]

# 将着色图像的大小调整为300乘300像素。
# Resize the tinted image to be 300 by 300 pixels.
img_tinted = imresize(img_tinted, (300, 300))

# 将有色图像写回磁盘（保存）
# Write the tinted image back to disk
imsave('assets/cat_tinted.jpg', img_tinted)
```
![cat]({{ '/styles/images/2019-01-31-SciPy/cat.jpg' | prepend: site.baseurl  }})![cattinted]({{ '/styles/images/2019-01-31-SciPy/cat_tinted.jpg' | prepend: site.baseurl  }})
左：原始图像。右图：着色和调整大小的图像。