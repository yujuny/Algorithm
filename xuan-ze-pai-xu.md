# 选择排序

---

## 概念

首先，找到数组中最小的那个元素，其次将它和数组的第一个元素交换位置。再次，在剩下的元素中找到最小的元素，将它与数组的第二个元素交换位置。如此往复，直到将整个数组排序。

## 代码

```
public static void sort(Comparable[] a) {
    int n = a.length;
    for (int i = 0; i < n; i++) {
        int min = i;
        for (int j = i+1; j < n; j++) {
            if (less(a[j], a[min])) min = j;
        }
        exch(a, i, min);
    }
}
```

## 轨迹图

![](/assets/trace)

# 复杂度

对于长度为N的数组，选择排序需要大约N²/2次比较和N次交换。

