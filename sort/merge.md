# 归并排序

---

## 概念

即将两个有序的数组归并成一个更大的有序数组。

要将一个数组排序，可以先（递归地）将它分成两半分别排序，然后将结果归并起来。

## 代码（自顶向下）

以下代码为自顶向下的归并排序算法。

```
public static void sort(Comparable[] a) {
    // 一个额外的空间，复制归并前的数组
    Comparable[] aux = new Comparable[a.length];
    sort(a, aux, 0, a.length-1);
}

private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi) {
    if (hi <= lo) return;
    int mid = lo + (hi - lo) / 2;
    sort(a, aux, lo, mid);
    sort(a, aux, mid + 1, hi);
    merge(a, aux, lo, mid, hi);
}

private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
    // copy to aux[]
    for (int k = lo; k <= hi; k++) {
        aux[k] = a[k]; 
    }

    // merge back to a[]
    int i = lo, j = mid+1;
    for (int k = lo; k <= hi; k++) {
        if      (i > mid)              a[k] = aux[j++];
        else if (j > hi)               a[k] = aux[i++];
        else if (less(aux[j], aux[i])) a[k] = aux[j++];
        else                           a[k] = aux[i++];
    }
}
```

## 轨迹图（自顶向下）

下图展示了一次归并的轨迹，aux\[\]为额外空间，用于存放归并前的数组。

归并的原理是从两个部分中找出最小的元素，存放到结果数组中。

![](/assets/sort/merge_trace1.png)

下图展示了一次完整的归并排序过程

![](/assets/sort/merge_trace2.png)

![](/assets/sort/merge_trace3.png)

## 代码（自底向上）

```
public static void sort(Comparable[] a) {
    int n = a.length;
    Comparable[] aux = new Comparable[n];
    for (int len = 1; len < n; len *= 2) {
        for (int lo = 0; lo < n-len; lo += len+len) {
            int mid  = lo+len-1;
            int hi = Math.min(lo+len+len-1, n-1);
            merge(a, aux, lo, mid, hi);
        }
    }
}
```

## 轨迹图（自底向上）

![](/assets/sort/merge_trace4.png)

# 复杂度

对于长长度为N的数组，自顶向下的归并排序需要1/2NlgN至NlgN次比较。需要6NlgN次数组访问。

