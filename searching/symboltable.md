# 符号表

---

## 定义

符号表是一种存储键值对的数据结构，支持两种操作：**插入**（put）即将一组新的键值对存入表中，**查找**（get）即根据指定的键得到相应的值。

## 接口

**基础API：**

![](/assets/searching/symbolTable_interface1.png)

**有序符号表API：**

![](/assets/searching/symbolTable_interface2.png)

## 实现

#### 无序链表中的顺序查找

符号表中使用的数据结构的一个简单的选择是链表，每个结点存储一个键值对。

get的实现即为遍历链表，用equals方法比较需被查找的键和每个结点中的键，如果匹配成功则返回相应的键，否则返回null。

put的实现也是遍历链表，用equals方法比较需被查找的键和每个结点中的键，如果匹配成功则用第二个参数指定的值更新和该键相关联的值，否则用给定的键值对创建一个新的结点并将其插入到链表的开头。

**代码**

```
public class SequentialSearchST<Key, Value> {
    private int n;           // number of key-value pairs
    private Node first;      // the linked list of key-value pairs

    private class Node {
        private Key key;
        private Value val;
        private Node next;

        public Node(Key key, Value val, Node next)  {
            this.key  = key;
            this.val  = val;
            this.next = next;
        }
    }

    public int size() {
        return n;
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    public boolean contains(Key key) {
        if (key == null) throw new IllegalArgumentException("argument to contains() is null");
        return get(key) != null;
    }
    public Value get(Key key) {
        if (key == null) throw new IllegalArgumentException("argument to get() is null"); 
        for (Node x = first; x != null; x = x.next) {
            if (key.equals(x.key))
                return x.val;
        }
        return null;
    }

    public void put(Key key, Value val) {
        for (Node x = first; x != null; x = x.next) {
            if (key.equals(x.key)) {
                x.val = val;
                return;
            }
        }
        first = new Node(key, val, first);
        n++;
    }

    public void delete(Key key) {
        if (key == null) throw new IllegalArgumentException("argument to delete() is null"); 
        first = delete(first, key);
    }

    private Node delete(Node x, Key key) {
        if (x == null) return null;
        if (key.equals(x.key)) {
            n--;
            return x.next;
        }
        x.next = delete(x.next, key);
        return x;
    }
}
```

**轨迹图**

![](/assets/searching/symbolTable_trace1.png)

**复杂度**

在含有N对键值的基于无序链表的符号表中，未命中的查找和插入操作都需要N次比较。命中的查找在最坏情况下需要N次比较。特别地，向一个空表中插入N个不同的键需要~N²/2次比较。

#### 有序数组中的二分查找

使用的数据结构是一对平行数组，一个存储键一个存储值。通过保证数组中的键有序，然后使用数组的索引来高效地实现get和其他操作。

核心是rank方法，它返回表中小于给定键的数量。对于get方法，只要给定的键存在于表中，rank方法就能够精确地找到它（如果找不到，那么它肯定就不在表中）。对于put方法，只要给定的键存在表中，rank方法就能够精确地告诉我们到哪里去更新它的值，以及当键不在表中时将键存储到表的何处。

