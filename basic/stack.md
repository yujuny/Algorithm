# 栈

---

## 定义

栈是一种基于**先进后出**策略的集合类型。

## 接口

![](/assets/basic/stack_interface.png)

## 实现

### 定容栈

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

### 动态调整数组大小的栈

能根据实际数据大小动态修改数组大小的栈。

在push操作中，检查数组是否太小，如果数组太小，则将数组的长度加倍。（检测条件为栈的大小和数组大小是否相等）

在pop操作中，检查数组是否太大，如果数组太大，则将长度减半。（检测条件为栈的大小是否小于数组的四分之一）

**代码：**

```
public class ResizingArrayStack<Item> implements Iterable<Item> {
    private Item[] a;         // array of items
    private int n;            // number of elements on stack

    public ResizingArrayStack() {
        a = (Item[]) new Object[2];
        n = 0;
    }

    public boolean isEmpty() {
        return n == 0;
    }

    public int size() {
        return n;
    }

    private void resize(int capacity) {
        Item[] temp = (Item[]) new Object[capacity];
        for (int i = 0; i < n; i++) {
            temp[i] = a[i];
        }
        a = temp;

       // alternative implementation
       // a = java.util.Arrays.copyOf(a, capacity);
    }

    public void push(Item item) {
        if (n == a.length) resize(2*a.length);    // double size of array if necessary
        a[n++] = item;                            // add item
    }

    public Item pop() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        Item item = a[n-1];
        a[n-1] = null;                              // to avoid loitering
        n--;
        // shrink size of array if necessary
        if (n > 0 && n == a.length/4) resize(a.length/2);
        return item;
    }

    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        return a[n-1];
    }

    public Iterator<Item> iterator() {
        return new ReverseArrayIterator();
    }

    private class ReverseArrayIterator implements Iterator<Item> {
        private int i;

        public ReverseArrayIterator() {
            i = n-1;
        }

        public boolean hasNext() {
            return i >= 0;
        }

        public void remove() {
            throw new UnsupportedOperationException();
        }

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            return a[i--];
        }
    }
}
```

**轨迹图：**

![](/assets/basic/stack_trace2.png)

### 用链表实现栈：

```
public class Stack<Item> implements Iterable<Item> {
    private Node<Item> first;     // top of stack
    private int n;                // size of the stack

    private static class Node<Item> {
        private Item item;
        private Node<Item> next;
    }

    public Stack() {
        first = null;
        n = 0;
    }

    public boolean isEmpty() {
        return first == null;
    }

    public int size() {
        return n;
    }

    public void push(Item item) {
        Node<Item> oldfirst = first;
        first = new Node<Item>();
        first.item = item;
        first.next = oldfirst;
        n++;
    }

    public Item pop() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        Item item = first.item;        // save item to return
        first = first.next;            // delete first node
        n--;
        return item;                   // return the saved item
    }

    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        return first.item;
    }

    public String toString() {
        StringBuilder s = new StringBuilder();
        for (Item item : this) {
            s.append(item);
            s.append(' ');
        }
        return s.toString();
    }

    public Iterator<Item> iterator() {
        return new ListIterator<Item>(first);
    }

    private class ListIterator<Item> implements Iterator<Item> {
        private Node<Item> current;

        public ListIterator(Node<Item> first) {
            current = first;
        }

        public boolean hasNext() {
            return current != null;
        }

        public void remove() {
            throw new UnsupportedOperationException();
        }

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            Item item = current.item;
            current = current.next; 
            return item;
        }
    }
}
```



