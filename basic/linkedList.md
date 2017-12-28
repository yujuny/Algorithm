# 链表

---

## 定义

链表是一种递归的数据结构，它或者为空，或者是指向一个节点的引用，该节点含有一个泛型的元素和一个指向另一条链表的引用。

## 代码

```
private class Node {
   Item item;
   Node next;
}
```

## 操作

#### 构建

![](/assets/basic/linkedList_p1.png)

#### 在表头插入节点

![](/assets/basic/linkedList_p2.png)

#### 从表头删除节点

![](/assets/basic/linkedList_p3.png)

#### 在表尾插入节点

![](/assets/basic/linkedList_p4.png)

其它操作比如删除某个节点、在某个节点前插入一个节点，就不是那么容易了，需要遍历整个链表，需要消耗的时间与链表的长度成正比。利用双向链表可以解决这个问题。

## 双向链表

每个节点都含有两个连接，分别指向不同的方向，可以简单的实现任意节点的删除和插入操作。

## 代码

```
public class DoublyLinkedList<Item> implements Iterable<Item> {
    private int n;        // number of elements on list
    private Node pre;     // sentinel before first item
    private Node post;    // sentinel after last item

    public DoublyLinkedList() {
        pre  = new Node();
        post = new Node();
        pre.next = post;
        post.prev = pre;
    }

    // linked list node helper data type
    private class Node {
        private Item item;
        private Node next;
        private Node prev;
    }

    public boolean isEmpty()    { return n == 0; }
    public int size()           { return n;      }

    // add the item to the list
    public void add(Item item) {
        Node last = post.prev;
        Node x = new Node();
        x.item = item;
        x.next = post;
        x.prev = last;
        post.prev = x;
        last.next = x;
        n++;
    }

    ......

｝
```



