---
layout: post
#标题配置
title: Python-OpenCV图像透视矫正
date:   2019-06-26 08:31:00 +0800
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
![](/styles/images/2019-06-25/1.png)

校正后：

![](/styles/images/2019-06-25/2.png)

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

完整代码：
```python
import numpy as np
import cv2

def CrossPoint(line1, line2): 
    x0, y0, x1, y1 = line1[0]
    x2, y2, x3, y3 = line2[0]

    dx1 = x1 - x0
    dy1 = y1 - y0

    dx2 = x3 - x2
    dy2 = y3 - y2
    
    D1 = x1 * y0 - x0 * y1
    D2 = x3 * y2 - x2 * y3

    y = float(dy1 * D2 - D1 * dy2) / (dy1 * dx2 - dx1 * dy2)
    x = float(y * dx1 - D1) / dy1

    return (int(x), int(y))

def SortPoint(points):
    sp = sorted(points, key = lambda x:(int(x[1]), int(x[0])))
    if sp[0][0] > sp[1][0]:
        sp[0], sp[1] = sp[1], sp[0]
    
    if sp[2][0] > sp[3][0]:
        sp[2], sp[3] = sp[3], sp[2]
    
    return sp

def imgcorr(src):
    rgbsrc = src.copy()
    graysrc = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)
    blurimg = cv2.GaussianBlur(src, (3, 3), 0)
    Cannyimg = cv2.Canny(blurimg, 35, 189)

    lines = cv2.HoughLinesP(Cannyimg, 1, np.pi / 180, threshold = 30, minLineLength = 320, maxLineGap = 40)
   
    for i in range(int(np.size(lines)/4)):
        for x1, y1, x2, y2 in lines[i]:
            cv2.line(rgbsrc, (x1, y1), (x2, y2), (255, 255, 0), 3)
    
    points = np.zeros((4, 2), dtype = "float32")
    points[0] = CrossPoint(lines[0], lines[2])
    points[1] = CrossPoint(lines[0], lines[3])
    points[2] = CrossPoint(lines[1], lines[2])
    points[3] = CrossPoint(lines[1], lines[3])
    
    sp = SortPoint(points)

    width = int(np.sqrt(((sp[0][0] - sp[1][0]) ** 2) + (sp[0][1] - sp[1][1]) ** 2))
    height = int(np.sqrt(((sp[0][0] - sp[2][0]) ** 2) + (sp[0][1] - sp[2][1]) ** 2))

    dstrect = np.array([
        [0, 0],
        [width - 1, 0],
        [0, height - 1],
        [width - 1, height - 1]], dtype = "float32")
  
    transform = cv2.getPerspectiveTransform(np.array(sp), dstrect)
    warpedimg = cv2.warpPerspective(src, transform, (width, height))

    return warpedimg

if __name__ == '__main__':
    src = cv2.imread("input3.jpg")
    dst = imgcorr(src)
    cv2.imshow("Image", dst)

    blurimg = cv2.GaussianBlur(src, (3, 3), 0)
    Cannyimg2 = cv2.Canny(blurimg, 35, 189)
    cv2.imshow("Cannyimg2.jpg", Cannyimg2)

    lines2 = cv2.HoughLinesP(Cannyimg2, 1, np.pi / 180, threshold=30, minLineLength=320, maxLineGap=40)
#    cv2.imshow("linedimg2.jpg", lines2)

    cv2.waitKey(0)
    cv2.imwrite("linedimg3.jpg", lines2)
    cv2.imwrite("Cannyimg3.jpg", Cannyimg2)
    cv2.imwrite("output3.jpg", dst)

```