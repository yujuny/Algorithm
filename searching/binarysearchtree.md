# 二叉查找树

---

## 定义

二叉查找树（BST）是一颗二叉树，其中每个结点都含有一个Comparable的键以及相关联的值，且每个结点的键都大于其左子树中的任意结点而小于右子树的任意结点的键。

![](/assets/searching/binarySearchTree_define.png)

## 实现

```
public class BST<Key extends Comparable<Key>, Value> {
    private Node root;             // root of BST

    private class Node {
        private Key key;           // sorted by key
        private Value val;         // associated data
        private Node left, right;  // left and right subtrees
        private int size;          // number of nodes in subtree

        public Node(Key key, Value val, int size) {
            this.key = key;
            this.val = val;
            this.size = size;
        }
    }
}
```

#### 查找

如果树是空的，则查找未命中；如果被查找的键和根结点的键相同，查找命中，否则就递归地在适当的子树中继续查找。如果被查找的键较小就选择左子树，较大则选择右子树。

**代码**

```
public Value get(Key key) {
    return get(root, key);
}

private Value get(Node x, Key key) {
    if (key == null) throw new IllegalArgumentException("calls get() with a null key");
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if      (cmp < 0) return get(x.left, key);
    else if (cmp > 0) return get(x.right, key);
    else              return x.val;
}
```

**轨迹图**

![](/assets/searching/binarySearchTree_trace1.png)

#### 插入

如果树是空的，就返回一个含有该键值对的新结点；如果被查找的键小于根结点的键，继续在左子树中插入该键，否则在右子树中插入该键。

**代码**

```
public void put(Key key, Value val) {
    if (key == null) throw new IllegalArgumentException("calls put() with a null key");
    if (val == null) {
        delete(key);
        return;
    }
    root = put(root, key, val);
}

private Node put(Node x, Key key, Value val) {
    if (x == null) return new Node(key, val, 1);
    int cmp = key.compareTo(x.key);
    if      (cmp < 0) x.left  = put(x.left,  key, val);
    else if (cmp > 0) x.right = put(x.right, key, val);
    else              x.val   = val;
    x.size = 1 + size(x.left) + size(x.right);
    return x;
}
```

**轨迹图**

![](/assets/searching/binarySearchTree_trace2.png)

## 复杂度

使用二叉查找树的算法的运行时间取决于树的形状，而树的形状又取决于键被插入的先后顺序。在最好的情况下，一颗含有N个结点的树是完全平衡的，每条空链接和根结点的距离都为~logN。在最坏情况下，搜索路径上可能有N个结点。但在一般情况下树的形状和最好情况更接近。

![](/assets/searching/binarySearchTree_time.png)

在由N个随机键构造的二叉查找树中，查找命中、查找未命中、插入操作平均所需的比较次数为**~2lnN**（约1.39logN）。

## 其它操作

#### 最大键和最小键

如果根结点的左链接为空，那么一颗二叉查找树中最小键就是根结点；如果左链接非空，那么树中的最小键就是左子树中的最小键。

最大键的方法也是类似，只是变为查找右子树而已。

**代码**

```
public Key min() {
    if (isEmpty()) throw new NoSuchElementException("calls min() with empty symbol table");
    return min(root).key;
} 

private Node min(Node x) { 
    if (x.left == null) return x; 
    else                return min(x.left); 
} 

public Key max() {
    if (isEmpty()) throw new NoSuchElementException("calls max() with empty symbol table");
    return max(root).key;
} 

private Node max(Node x) {
    if (x.right == null) return x; 
    else                 return max(x.right); 
}
```

#### 向上取整和向下取整

如果给定的键key小于二叉查找树的根结点的键，那么小于等于key的最大键（floor）一定在根结点的左子树中。

如果给定的键key大于二叉查找树的根结点，那么只有当根结点右子树中存在小于等于key的结点时，小于等于key的最大键才会出现在右子树中，否则根结点就是小于等于key的最大键。

ceiling的算法与此类似，只要将“左”变为“右”，将小于变为大于。

**代码**

```
public Key floor(Key key) {
    if (key == null) throw new IllegalArgumentException("argument to floor() is null");
    if (isEmpty()) throw new NoSuchElementException("calls floor() with empty symbol table");
    Node x = floor(root, key);
    if (x == null) return null;
    else return x.key;
} 

private Node floor(Node x, Key key) {
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if (cmp == 0) return x;
    if (cmp <  0) return floor(x.left, key);
    Node t = floor(x.right, key); 
    if (t != null) return t;
    else return x; 
} 

public Key ceiling(Key key) {
    if (key == null) throw new IllegalArgumentException("argument to ceiling() is null");
    if (isEmpty()) throw new NoSuchElementException("calls ceiling() with empty symbol table");
    Node x = ceiling(root, key);
    if (x == null) return null;
    else return x.key;
}

private Node ceiling(Node x, Key key) {
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if (cmp == 0) return x;
    if (cmp < 0) { 
        Node t = ceiling(x.left, key); 
        if (t != null) return t;
        else return x; 
    } 
    return ceiling(x.right, key); 
}
```

**轨迹图**

![](/assets/searching/binarySearchTree_trace3.png)

#### 选择

假设我们想找到排名为k的键（即树中正好有k个小于它的键）。如果左子树中的结点数t大于k，那么我们就继续递归地在左子树中查找排名为k的键；如果t等于k，就返回根结点中的键；如果t小于k，就递归地在右子树中查找排名为k-t-1的键。

**代码**

```
public Key select(int k) {
    if (k < 0 || k >= size()) {
        throw new IllegalArgumentException("argument to select() is invalid: " + k);
    }
    Node x = select(root, k);
    return x.key;
}

private Node select(Node x, int k) {
    if (x == null) return null; 
    int t = size(x.left); 
    if      (t > k) return select(x.left,  k); 
    else if (t < k) return select(x.right, k-t-1); 
    else            return x; 
}
```

**轨迹图**

![](/assets/searching/binarySearchTree_trace4.png)

#### 排名

如果给定的键和根结点的键相等，就返回左子树中的节点总数t；如果给定的键小于根结点，就返回该键在左子树中的排名；如果给定的键大于根结点，就返回t+1加上他在右子树中的排名。

**代码**

```
public int rank(Key key) {
    if (key == null) throw new IllegalArgumentException("argument to rank() is null");
    return rank(key, root);
} 

private int rank(Key key, Node x) {
    if (x == null) return 0; 
    int cmp = key.compareTo(x.key); 
    if      (cmp < 0) return rank(key, x.left); 
    else if (cmp > 0) return 1 + size(x.left) + rank(key, x.right); 
    else              return size(x.left); 
}
```

#### 删除最大键和删除最小键

删除最小键（deleteMin）时，需要不断地深入根结点的左子树中直至遇见一个空连接，然后将指向该结点的链接指向该节点的右子树。

删除最大键与其类似。

**代码**

```
public void deleteMin() {
    if (isEmpty()) throw new NoSuchElementException("Symbol table underflow");
    root = deleteMin(root);
}

private Node deleteMin(Node x) {
    if (x.left == null) return x.right;
    x.left = deleteMin(x.left);
    x.size = size(x.left) + size(x.right) + 1;
    return x;
}
```

**轨迹图**

![](/assets/searching/binarySearchTree_trace5.png)

#### 删除

在删除结点后用它的后继节点填补它的位置。简单步骤如下：

1. 将指向即将被删除的结点的链接保存为t
2. 将x指向它的后继结点min\(t.right\)
3. 将x的右链接指向deleteMin\(t.right\)
4. 将x的左链接设为t.left

**轨迹图**

![](/assets/searching/binarySearchTree_trace6.png)

**代码**

```
public void delete(Key key) {
    if (key == null) throw new IllegalArgumentException("calls delete() with a null key");
    root = delete(root, key);
}

private Node delete(Node x, Key key) {
    if (x == null) return null;

    int cmp = key.compareTo(x.key);
    if      (cmp < 0) x.left  = delete(x.left,  key);
    else if (cmp > 0) x.right = delete(x.right, key);
    else { 
        if (x.right == null) return x.left;
        if (x.left  == null) return x.right;
        Node t = x;
        x = min(t.right);
        x.right = deleteMin(t.right);
        x.left = t.left;
    } 
    x.size = size(x.left) + size(x.right) + 1;
    return x;
}
```

#### 范围查找

```
public Iterable<Key> keys() {
    if (isEmpty()) return new Queue<Key>();
    return keys(min(), max());
}

public Iterable<Key> keys(Key lo, Key hi) {
    if (lo == null) throw new IllegalArgumentException("first argument to keys() is null");
    if (hi == null) throw new IllegalArgumentException("second argument to keys() is null");

    Queue<Key> queue = new Queue<Key>();
    keys(root, queue, lo, hi);
    return queue;
} 

private void keys(Node x, Queue<Key> queue, Key lo, Key hi) { 
    if (x == null) return; 
    int cmplo = lo.compareTo(x.key); 
    int cmphi = hi.compareTo(x.key); 
    if (cmplo < 0) keys(x.left, queue, lo, hi); 
    if (cmplo <= 0 && cmphi >= 0) queue.enqueue(x.key); 
    if (cmphi > 0) keys(x.right, queue, lo, hi); 
}
```

## 成本总结

![](/assets/searching/binarySearchTree_compare.png)



