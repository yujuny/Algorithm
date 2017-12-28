# 二分查找（整数）

---

## 定义

接受一个整数键和一个已经有序的int数组作为参数。如果该键存在于数组中则返回它的索引，否则返回-1。

将数组的中间键和被查找的键比较，如果被查找的键等于中间键，则返回中间键的索引；如果被查找的键小于中间键，则在左半部分继续查找；如果被查找的键大于中间键，则在右半部分查找。直到找到或者范围为空。

## 代码

```
public static int indexOf(int[] a, int key) {
    int lo = 0;
    int hi = a.length - 1;
    while (lo <= hi) {
        // Key is in a[lo..hi] or not present.
        int mid = lo + (hi - lo) / 2;
        if      (key < a[mid]) hi = mid - 1;
        else if (key > a[mid]) lo = mid + 1;
        else return mid;
    }
    return -1;
}
```

## 轨迹图

![](/assets/basic/binarySearch_trace.png)

# 复杂度

复杂度为logN

# 应用

#### 2-sum问题

找出一个数组中所有和为0的整数对的数量。

原始解法，代码如下。

```
public static int count(int[] a) {
    int n = a.length;
    int count = 0;
    for (int i = 0; i < n; i++) {
        for (int j = i+1; j < n; j++) {
            if (a[i] + a[j] == 0) {
                count++;
            }
        }
    }
    return count;
}
```

上述代码，使用了两次循环，时间复杂度为平方级别。\(N²\)

使用二分法解法，代码如下。

```
public static int count(int[] a) {
    int n = a.length;
    Arrays.sort(a);
    int count = 0;
    for (int i = 0; i < n; i++) {
        int j = Arrays.binarySearch(a, -a[i]);
        if (j > i) count++;
    }
    return count;
}
```

首先需要对数组进行排序，然后通过一次循环，使用二分法依次查找该元素的相反数是否存在数组中。时间复杂度缩短为NlogN。

