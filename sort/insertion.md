# 插入排序

---

## 概念

将一个元素插入到已经排好序的有序数据中。插入位置后方的元素都要向右移动一位。

## 代码

```
public static void sort(Object[] a, Comparator comparator) {
    int n = a.length;
    for (int i = 0; i < n; i++) {
        for (int j = i; j > 0 && less(a[j], a[j-1], comparator); j--) {
            exch(a, j, j-1);
        }
    }
}
```

## 轨迹图

![](/assets/sort/insertion_trace.png)

# 复杂度

对于随机排列的长度为N且主鍵不重复的数组，平均情况下插入排序需要~N²/4次比较以及~N²/4次交换。最坏情况下需要~N²/2次比较和~N²/2次交换，最好情况下需要N-1次比较和0次交换。

