---
layout: post
#标题配置
title: Python 快速排序
#时间配置
date:   2019-01-26 22:41:00 +0800
#大类配置
categories: python
#小类配置
tag: 笔记
---

* content
{:toc}

## python实现快排序
```Python
def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quicksort(left) + middle + quicksort(right)

print(quicksort([3,6,8,10,1,2,1]))
# Prints "[1, 1, 2, 3, 6, 8, 10]"
```