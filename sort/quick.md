# 快速排序

---

## 定义

一种分治算法，将一个数组分成两个数组，两部分独立地排序。弥补了归并排序需要额外空间的缺点。

归并排序将数组分成两个子数组分别排序，并将有序的子数组归并以将整个数组排序。而快速排序将数组排序的方式则是当两个子数组都有序时整个数组也就自然有序了。

在快速排序中，切分数组的大致过程如下图所示。（将第一个元素插入合适位置，使其左边的元素均小于该元素，右边的元素均大于该元素）

![](/assets/sort/quick_cut.png)

## 代码

```
private static void sort(Comparable[] a, int lo, int hi) { 
    if (hi <= lo) return;
    // 切分
    int j = partition(a, lo, hi);
    sort(a, lo, j-1);
    sort(a, j+1, hi);
}

private static int partition(Comparable[] a, int lo, int hi) {
    int i = lo;
    int j = hi + 1;
    Comparable v = a[lo];
    while (true) { 
        // 找到一个比V小的元素，等待交换
        while (less(a[++i], v)) {
            if (i == hi) break;
        }

        // 找到一个比V大的元素，等待交换
        while (less(v, a[--j])) {
            if (j == lo) break;
        }

        // 判断是否越界
        if (i >= j) break;

        exch(a, i, j);
    }

    // 交换第一个元素
    exch(a, lo, j);

    return j;
}
```

## 轨迹图

**切分轨迹：**

![](/assets/sort/quick_trace1.png)

![](/assets/sort/quick_trace3.png)

**完整的快速排序轨迹：**

![](/assets/sort/quick_trace2.png)

# 复杂度

对于长度为N的无重复数组，快速排序平均需要~2NlnN次比较，以及1/6的交换。

# 三向切分的快速排序

在有大量重复元素的情况下，快速排序的递归性会使元素全部重复的子数组继续排序。三向切分的快速排序可以改进这种情况。

三向切分的快速排序将数组切分为三部分，分别对应小于、等于和大于切分元素的数组元素。这样等于切分元素的子数组就不需要再排序了。

![](/assets/sort/quick_trace4.png)

#### 代码

```
private static void sort(Comparable[] a, int lo, int hi) { 
    if (hi <= lo) return;
    int lt = lo, gt = hi;
    Comparable v = a[lo];
    int i = lo + 1;
    while (i <= gt) {
        int cmp = a[i].compareTo(v);
        if      (cmp < 0) exch(a, lt++, i++);
        else if (cmp > 0) exch(a, i, gt--);
        else              i++;
    }

    // a[lo..lt-1] < v = a[lt..gt] < a[gt+1..hi]. 
    sort(a, lo, lt-1);
    sort(a, gt+1, hi);
    assert isSorted(a, lo, hi);
}
```

#### 轨迹图

![](/assets/sort/quick_trace5.png)

#### 优化——快速三向切分

![](/assets/sort/quick_trace6.png)

