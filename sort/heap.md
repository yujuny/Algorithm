# 堆

---

## 定义

数据结构二叉堆能够很好地实现优先队列的基本操作。在二叉堆数组中，每个元素都要保证大于等于另两个特定位置的元素。相应地，这些位置的元素又至少要大于等于数组中的另两个元素，以此类推。

* **当一棵二叉树的每个结点都大于等于它的两个子结点时，它被称为堆有序。**
* **根结点是堆有序的二叉树中最大结点。**

![](/assets/sort/heap_define.png)

#### 二叉堆的表示

完全二叉树只用数组而不需要指针就可以表示。具体方法就是将二叉树的结点按照层级顺序放入数组中。（不使用数组的第一个位置）

![](/assets/sort/heap_define2.png)

* **位置K的结点的父结点的位置为K/2，而它的两个子结点的位置则分别为2K和2K+1。**
* **一棵大小为N的完全二叉树的高度为logN。**

## 算法

#### 由下至上的堆有序化（上浮）

如果堆的有序状态因为某个结点变得比它的父结点更大而被打破，那么我们就需要通过交换它和它的父结点来修复堆。交换后这个结点比它的两个子结点都大，但这个结点仍然可能比它现在的父结点更大。我们可以一遍遍地用同样的办法来回复秩序。

**代码：**

```
private void swim(int k) {
   while (k > 1 && less(k/2, k)) {
      exch(k, k/2);
      k = k/2;
   }
}
```

**轨迹图：**

![](/assets/sort/heap_trace1.png)

#### 由上至下的堆有序化（下沉）

如果堆的有序状态因为某个结点变得比它的两个子节点或者是其中之一更小而被打破了，那么我们可以通过将它和它的两个子节点中较大者交换来恢复堆。交换可能会在子结点处继续打破堆的有序状态，因此我们需要不断地用相同的方式将其修复。

**代码：**

```
private void sink(int k) {
   while (2*k <= N) {
      int j = 2*k;
      if (j < N && less(j, j+1)) j++;
      if (!less(k, j)) break;
      exch(k, j);
      k = j;
   }
}
```

**轨迹图：**

![](/assets/sort/heap_trace2.png)

## 基于堆的优先队列

swim和sink方法是高效实现优先队列的基础。

**插入元素。**我们将新元素加到数组末尾，增加堆的大小并让这个新元素上浮到合适的位置。

**删除最大元素**。我们从数组顶端删去最大的元素并将数组的最后一个元素放到顶端，减小堆的大小并让这个元素下沉到合适的位置。

![](/assets/sort/heap_trace3.png)

#### 代码

```
public class MaxPQ<Key> implements Iterable<Key> {
    private Key[] pq;                    // store items at indices 1 to n
    private int n;                       // number of items on priority queue
    private Comparator<Key> comparator;  // optional comparator

    public MaxPQ(int initCapacity) {
        pq = (Key[]) new Object[initCapacity + 1];
        n = 0;
    }

    public MaxPQ() {
        this(1);
    }

    public boolean isEmpty() {
        return n == 0;
    }

    public int size() {
        return n;
    }

    public Key max() {
        if (isEmpty()) throw new NoSuchElementException("Priority queue underflow");
        return pq[1];
    }

    private void resize(int capacity) {
        assert capacity > n;
        Key[] temp = (Key[]) new Object[capacity];
        for (int i = 1; i <= n; i++) {
            temp[i] = pq[i];
        }
        pq = temp;
    }

    public void insert(Key x) {
        // double size of array if necessary
        if (n == pq.length - 1) resize(2 * pq.length);

        // add x, and percolate it up to maintain heap invariant
        pq[++n] = x;
        swim(n);
        assert isMaxHeap();
    }

    public Key delMax() {
        if (isEmpty()) throw new NoSuchElementException("Priority queue underflow");
        Key max = pq[1];
        exch(1, n--);
        sink(1);
        pq[n+1] = null;     // to avoid loiterig and help with garbage collection
        if ((n > 0) && (n == (pq.length - 1) / 4)) resize(pq.length / 2);
        return max;
    }

    private void swim(int k) {
        while (k > 1 && less(k/2, k)) {
            exch(k, k/2);
            k = k/2;
        }
    }

    private void sink(int k) {
        while (2*k <= n) {
            int j = 2*k;
            if (j < n && less(j, j+1)) j++;
            if (!less(k, j)) break;
            exch(k, j);
            k = j;
        }
    }

    private boolean less(int i, int j) {
        if (comparator == null) {
            return ((Comparable<Key>) pq[i]).compareTo(pq[j]) < 0;
        }
        else {
            return comparator.compare(pq[i], pq[j]) < 0;
        }
    }

    private void exch(int i, int j) {
        Key swap = pq[i];
        pq[i] = pq[j];
        pq[j] = swap;
    }

    public Iterator<Key> iterator() {
        return new HeapIterator();
    }

    private class HeapIterator implements Iterator<Key> {

        private MaxPQ<Key> copy;

        public HeapIterator() {
            if (comparator == null) copy = new MaxPQ<Key>(size());
            else                    copy = new MaxPQ<Key>(size(), comparator);
            for (int i = 1; i <= n; i++)
                copy.insert(pq[i]);
        }

        public boolean hasNext()  { return !copy.isEmpty();                     }
        public void remove()      { throw new UnsupportedOperationException();  }

        public Key next() {
            if (!hasNext()) throw new NoSuchElementException();
            return copy.delMax();
        }
    }
}
```

#### 轨迹图

![](/assets/sort/heap_trace4.png)

#### 复杂度

对于一个含有N个元素的基于堆的优先队列，插入元素操作只需不超过**logN+1**次比较，删除最大元素的操作需要不超过**2logN**次比较。

