# 平衡查找树之红黑二叉查找树

---

# 定义

红黑二叉查找树是一种简单的数据结构，用来实现2-3树。

## 替换3-结点

红黑二叉查找树背后的基本思想是用标准的二叉查找树（完全由2-结点构成）和一些额外的信息（替换3-结点）来表示2-3树。

我们将树中的链接分为两种类型，红链接将两个2-结点连起来构成一个3-结点，黑链接则是2-3树中的普通链接。

![](/assets/searching/redBlackBST_define1.png)

红黑树的另一种定义是含有红黑链接并满足下列条件的二叉查找树：

* **红链接均为左链接**
* **没有任何一个结点同时和两条红链接相连**
* **该树是完美黑平衡的，即任意空链接到根结点的路径上的黑链接的数量相同**

![](/assets/searching/redBlackBST_define2.png)

![](/assets/searching/redBlackBST_define3.png)

## 颜色表示

因为每个结点都只会有一条指向自己的链接，我们将链接的颜色保存在表示结点的Node数据类型的布尔变量color中。如果指向它的链接是红色的，那么该变量就是true，黑色则为false。我们约定空链接为黑色。

![](/assets/searching/redBlackBST_define4.png)

```
private static final boolean RED   = true;
private static final boolean BLACK = false;

private class Node {
    private Key key;           // key
    private Value val;         // associated data
    private Node left, right;  // links to left and right subtrees
    private boolean color;     // color of parent link
    private int size;          // subtree count

    public Node(Key key, Value val, boolean color, int size) {
        this.key = key;
        this.val = val;
        this.color = color;
        this.size = size;
    }
}

public RedBlackBST() {
}

private boolean isRed(Node x) {
    if (x == null) return false;
    return x.color == RED;
}
```

## 旋转

在我们实现的某些操作中可能会出现红色右链接或者两条连续的红链接,但在操作完成前这些情况都会被小心地旋转并修复。旋转操作会改变红链接的指向。一条红色的右链接被转化为左链接，这个操作叫做左旋转,它对应的方法接受一条指向红黑树中的某个结点的链接作为参数。假设被指向的结点的右链接是红色的,这个方法会对树进行必要的调整并返回一个指向包含同一组键的子树且其左链接为红色的根结点的链接。

![](/assets/searching/rebBlackBST_define5.png)

## 向2-结点中插入新键

如果新键小于老键，只需要新增一个红色的结点即可。如果新键大于老键，那么新增的红色结点将会产生一条红色的右链接，需要使用root=rotateLeft\(root\)，来将其旋转为红色左链接并新修正根结点的链接。

![](/assets/searching/redBlackBST_trace1.png)

## 向树底部的2-结点插入新键

如果指向结点的是父结点的左链接，那么父结点就直接成为了一个3-结点。

如果指向新结点的是父结点的右链接，这就是一个错误的3-结点，但一次左旋转就能修正它。

![](/assets/searching/redBlackBST_trace2.png)

## 向一棵双键树（即一个3-结点）中插入新键

可以分为三种子情况：新键小于树中的两个键，在两者之间，或是大于树种的两个键。

1. 大于原树中的两个键，因此它被链接到3-结点的右链接。此时树是平衡的，根结点为中间大小的键，它有两条红链接分别和较小和较大的结点项链。如果将两条链接的颜色都由红变黑，那么我们就得到了一棵由三个结点组成、高为2的平衡树。它正好能够对应一棵2-3树。其他两种情况最终也会转化为这种情况。
2. 如果新键小于原树中的两个键，它会被连接到最左边的空链接，这样就产生了两条连续的红链接。此时我们只需要将上层的红链接右旋转即可得到第一种情况。
3. 如果新键介于原树中的两个键之间，这又会产生两条连续的红链接，一条红色左链接接一条红色右链接，此时我们只需要将下层的红链接坐旋转即可得到第二种情况。

![](/assets/searching/rebBlackBST_trace3.png)



