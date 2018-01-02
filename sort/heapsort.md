# 堆排序

---

## 定义

我们可以把任意优先队列变成一种排序方法。将所有元素插入一个查找最小元素的优先队列，然后再重复调用删除最小元素的操作来将它们按顺序删去。基于堆的优先队列的这种操作就是堆排序。

堆排序可以分为两个阶段。在**堆的构造阶段**中，我们将原始数组重新组织安排进一个堆中；然后在**下沉排序阶段**，我们从堆中按递减顺序取出所有元素并得到排序结果。

#### 堆的构造

从右至左用sink函数构造子堆。开始时只需要扫描数组中的一半元素，可以跳过大小为1的子堆。

#### 下沉排序

将堆中的最大元素删除，然后放入堆缩小后数组中空出的位置。

轨迹图，左边为堆的构造，右边为下沉排序。

![](/assets/sort/heapSort_trace1.png)

## 代码

```
public static void sort(Comparable[] pq) {
    int n = pq.length;
    for (int k = n/2; k >= 1; k--)
        sink(pq, k, n);
    while (n > 1) {
        exch(pq, 1, n--);
        sink(pq, 1, n);
    }
}

private static void sink(Comparable[] pq, int k, int n) {
    while (2*k <= n) {
        int j = 2*k;
        if (j < n && less(pq, j, j+1)) j++;
        if (!less(pq, k, j)) break;
        exch(pq, k, j);
        k = j;
    }
}
```

## 轨迹图

![](/assets/sort/heapSort_trace2.png)

## 复杂度

用下沉操由N个元素构造堆只需要少于2N次比较以及少于N次交换

将N个元素排序，堆排序只需要少于**2NlogN+2N**次比较以及一半次数的交换。

