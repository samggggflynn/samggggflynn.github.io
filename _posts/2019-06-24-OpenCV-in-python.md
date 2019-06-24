---
layout: post
#标题配置
title: OpenCV Python教程（1、图像的载入、显示和保存）
#时间配
date:   2019-06-24 11:05:00 +0800
#大类配置
categories: OpenCV
#小类配置
tag: cv笔记
---

* content
{:toc}

---

注意，现在OpenCV for Python就是通过NumPy进行绑定的。所以在使用时必须掌握一些NumPy的相关知识！

图像就是一个矩阵，在OpenCV for Python中，图像就是NumPy中的数组！

如果读取图像首先要导入OpenCV包，方法为：

`import cv2`

## 函数简介
1、imread—读取图像
函数原型：imread(filename, flags=None)
filename:读取的图像路径名；例如：”H:\img\lena.jpg”。
flags:彩色图or灰色图，1：表示彩色图；0：表示灰色图。

2、imshow—显示图像
函数原型：imshow(winname, mat)
winname:窗口名字；例如：”Lena”。
mat:要显示的图像矩阵。

3、imwrite—保存图像
函数原型：imwrite(filename, img, params=None)
filename:保存到的图像路径名；例如：”H:\img\Newlena.jpg”。
img:要保存的图像矩阵；例如：image。
params:缺省的参数。

## 读取并显示图像
在Python中不需要声明变量，所以也就不需要C++中的cv::Mat xxxxx了。只需这样：

`img = cv2.imread("D:\cat.jpg")`
OpenCV目前支持读取bmp、jpg、png、tiff等常用格式。更详细的请参考OpenCV的参考文档。

接着创建一个窗口

`cv2.namedWindow("Image")`
然后在窗口中显示图像

`cv2.imshow("Image", img)`
最后还要添上一句：

`cv2.waitKey (0)`
如果不添最后一句，在IDLE中执行窗口直接无响应。在命令行中执行的话，则是一闪而过。

完整的程序为：
```python
import cv2 
 
img = cv2.imread("D:\\cat.jpg") 
cv2.namedWindow("Image") 
cv2.imshow("Image", img) 
cv2.waitKey (0)
cv2.destroyAllWindows()
```
最后释放窗口是个好习惯！
