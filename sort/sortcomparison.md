# 各种排序算法的比较

---

## 稳定性

如果一个排序算法能够保留数组中重复元素的相对位置则可以被称为是稳定的。

这个性质在许多情况下很重要，例如一些数据先按照时间排序好了，现在再将其按照地理位置排序，如果排序算法是不稳定的，排序后的数据可能不会再按照时间排序。

## 各种排序算法的性能特点

![](/assets/sort/sortComparision_compare.png)

## 总结

**快速排序是最快的通用排序算法。**

自从数十年前快速排序发明以来，它在无数计算机系统中的无数实现已经证明了这一点。总的来说，快速排序之所以最快是因为它的内循环中的指令很少，所以它的运行时间的增长数量级为~cNlogN，而这里的c比其他线性对数级别的排序算法的相应常数都要小。

在Java系统库中，Arrays.sort\(\)根据不同的参数类型，选择不同的排序方法。**对原始数据类型使用三向切分的快速排序，对引用类型使用归并排序**。
