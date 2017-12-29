# 栈

---

## 定义

栈是一种基于**先进后出**策略的集合类型。

## 接口

![](/assets/basic/stack_interface.png)

## 实现

#### 定容栈

一个用数组实现的固定容量的栈，必须事先知道最大容量，并且入栈的元素数量不能超过最大容量。

**代码：**

```
public class FixedCapacityStack<Item> implements Iterable<Item> {
    private Item[] a;    // holds the items
    private int N;       // number of items in stack

    // create an empty stack with given capacity
    public FixedCapacityStack(int capacity) {
        a = (Item[]) new Object[capacity];   // no generic array creation
        N = 0;
    }

    public boolean isEmpty()          {  return N == 0;                    }
    public void push(Item item)       {  a[N++] = item;                    }
    public Item pop()                 {  return a[--N];                    }
    public Iterator<Item> iterator()  { return new ReverseArrayIterator(); }

    public class ReverseArrayIterator implements Iterator<Item> {
        private int i = N-1;

        public boolean hasNext() {
            return i >= 0;
        }

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            return a[i--];
        }

        public void remove() {
            throw new UnsupportedOperationException();
        }
    }
}
```

**轨迹图：**

![](/assets/basic/stack_trace1.png)

**定容栈的缺点：**

1. 大小无法改变，且有最大容量限制。
2. 在栈为空或者几乎为空时，浪费大量内存。

#### 动态调整数组大小的栈



