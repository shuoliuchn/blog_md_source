## 二分查找

二分查找是一种快速查找列表中元素的算法，只可以作用在有序序列中。

```python
def binary_search(alist, item):    # alist是一个有序序列,item是我们要在alist中查找的元素
    low = 0
    high = len(alist) - 1
    while low <= high:
        mid = (low + high) // 2    # 中间元素的下标
        if alist[mid] > item:    # 查找的元素<中间元素，查找的元素存在中间元素左侧
            # 将中间元素左侧序列作为一个新的序列，然后再基于item和当前新序列的中间值进行大小比较
            high = mid - 1    # low和high就可以表示新序列的范围
        elif alist[mid] < item:    # 查找的元素存在于中间元素右侧
            low = mid + 1    # low和high就可以表示中间元素右侧的子序列
        else:    # 查找的元素就是中间元素
            return True
    return False

alist = [1, 2, 3, 4, 5]
print(binary_search(alist, 1))    # True
print(binary_search(alist, 3))    # True
print(binary_search(alist, 5))    # True
print(binary_search(alist, 7))    # False
```

