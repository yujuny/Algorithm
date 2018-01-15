# 散列表

---

# 定义

如果所有的键都是小整数，可以用一个数组来实现无序的符号表，将键作为数组的索引而数组中键i处所储存的就是它对应的值。这样我们就可以快速访问任意键的值。

散列表是这种简易方法的扩展并能处理更加复杂的类型的键。我们需要用算术操作将键转化为数组的索引来访问数组中的键值对。

使用散列的查找算法分为两步。第一步是用**散列函数**将被查找的键转化为数组的一个索引。第二步是**处理碰撞冲突**的过程。

![](/assets/searching/hashTable_define1.png)

## 散列函数

散列函数的计算，会将键转化为数组的索引。如果有一个能够保存M个键值对的数组，那么就需要一个能够将任意键转化为该数组范围内索引的散列函数。

对于每种类型的键，都需要一个与之对应的散列函数。

#### 正整数

将整数散列最常用的方法是**除留余数法**。选择大小为素数M的数组，对任意正整数，计算k除以M的余数。

#### 浮点数

如果键是0到1之间的实数，我们可以讲它乘以M并四舍五入得到一个0至M-1的索引值。

但是这个方法有缺陷，因为这种情况下键的高位起的作用更大，最低位对散列的结果没有影响。修正这个问题的办法是将键表示为二进制数然后再用除留余数法（JAVA的做法）。

#### 字符串

```
int hash = 0;
for (int i = 0; i < s.length(); i++)
    hash = (R * hash + s.charAt(i)) % M;
```

相当于将字符串当作一个N位的R进制，将它除以M取余。R为一个较小的素数31。

#### 组合键

如果键的类型含有多个整形变量，我们可以和String类型一样将它们混合起来。

假设被查找的键的类型是Date，其中含有几个整形的域：day、month、year。我们可以这样计算它的散列值。

int hash = \(\(\(day\*R+month\)%M\)\*R+year\)%M

#### JAVA中的hashCode

java令所有数据类型都继承了一个能够返回一个32位整数的hashCode方法。

我们需要的是数组的索引而不是一个32位的整数，我们在实现中会将默认的hashCode方法和除留余数法结合起来产生一个0到M-1的整数，方法如下：

```
private int hash(Key key) {
   return (key.hashCode() & 0x7fffffff) % M;
}
```

#### 自定义的hashCode方法

对于自己定义的数据类型，必须实现hashCode方法。简单的做法是，将对象中的每个变量的hashCode返回值转化为32位整数并计算得到散列值。例如：

```
public class Transaction {
    private final String  who;      // customer
    private final Date    when;     // date
    private final double  amount;   // amount

    public int hashCode() {
        int hash = 1;
        hash = 31*hash + who.hashCode();
        hash = 31*hash + when.hashCode();
        hash = 31*hash + ((Double) amount).hashCode();
        return hash;
    }
｝
```

## 基于拉链法的散列表

散列算法的第二步就是**碰撞处理**，也就是处理两个或多个键的散列值相同的情况。一种直接的办法是将大小为M的数组中的每个元素指向一条链表，链表中的每个结点都存储了散列值为该元素的索引的键值对。

#### 代码

```
public class SeparateChainingHashST<Key, Value> {
    private static final int INIT_CAPACITY = 4;

    private int n;                                // number of key-value pairs
    private int m;                                // hash table size
    private SequentialSearchST<Key, Value>[] st;  // array of linked-list symbol tables

    public SeparateChainingHashST() {
        this(INIT_CAPACITY);
    } 

    public SeparateChainingHashST(int m) {
        this.m = m;
        st = (SequentialSearchST<Key, Value>[]) new SequentialSearchST[m];
        for (int i = 0; i < m; i++)
            st[i] = new SequentialSearchST<Key, Value>();
    } 

    private void resize(int chains) {
        SeparateChainingHashST<Key, Value> temp = new SeparateChainingHashST<Key, Value>(chains);
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                temp.put(key, st[i].get(key));
            }
        }
        this.m  = temp.m;
        this.n  = temp.n;
        this.st = temp.st;
    }

    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    } 

    public Value get(Key key) {
        if (key == null) throw new IllegalArgumentException("argument to get() is null");
        int i = hash(key);
        return st[i].get(key);
    } 

    public void put(Key key, Value val) {
        if (key == null) throw new IllegalArgumentException("first argument to put() is null");
        if (val == null) {
            delete(key);
            return;
        }

        // double table size if average length of list >= 10
        if (n >= 10*m) resize(2*m);

        int i = hash(key);
        if (!st[i].contains(key)) n++;
        st[i].put(key, val);
    } 

    public void delete(Key key) {
        if (key == null) throw new IllegalArgumentException("argument to delete() is null");

        int i = hash(key);
        if (st[i].contains(key)) n--;
        st[i].delete(key);

        // halve table size if average length of list <= 2
        if (m > INIT_CAPACITY && n <= 2*m) resize(m/2);
    } 

    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<Key>();
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys())
                queue.enqueue(key);
        }
        return queue;
    } 
}
```

#### 轨迹图

![](/assets/searching/hashTable_trace1.png)

#### 复杂度

在一张含有M条链表和N个键的散列表中，未命中查找和插入操作所需的比较次数为~N/M。

## 基于线性探测法的散列表

实现散列表的另一种方式就是用大小为M的数组保存N个键值对，其中M&gt;N。我们需要依靠数组中的空位解决碰撞冲突。基于这种策略的所有方法被统称为开放地址散列表。

开放地址散列表中最简单的方法叫做线性探测法：当碰撞发生时，我们直接检查散列表中下一个位置。

#### 代码

```
public class LinearProbingHashST<Key, Value> {
    private static final int INIT_CAPACITY = 4;

    private int n;           // number of key-value pairs in the symbol table
    private int m;           // size of linear probing table
    private Key[] keys;      // the keys
    private Value[] vals;    // the values

    public LinearProbingHashST() {
        this(INIT_CAPACITY);
    }

    public LinearProbingHashST(int capacity) {
        m = capacity;
        n = 0;
        keys = (Key[])   new Object[m];
        vals = (Value[]) new Object[m];
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

    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    }

    private void resize(int capacity) {
        LinearProbingHashST<Key, Value> temp = new LinearProbingHashST<Key, Value>(capacity);
        for (int i = 0; i < m; i++) {
            if (keys[i] != null) {
                temp.put(keys[i], vals[i]);
            }
        }
        keys = temp.keys;
        vals = temp.vals;
        m    = temp.m;
    }

    public void put(Key key, Value val) {
        if (key == null) throw new IllegalArgumentException("first argument to put() is null");

        if (val == null) {
            delete(key);
            return;
        }

        // double table size if 50% full
        if (n >= m/2) resize(2*m);

        int i;
        for (i = hash(key); keys[i] != null; i = (i + 1) % m) {
            if (keys[i].equals(key)) {
                vals[i] = val;
                return;
            }
        }
        keys[i] = key;
        vals[i] = val;
        n++;
    }

    public Value get(Key key) {
        if (key == null) throw new IllegalArgumentException("argument to get() is null");
        for (int i = hash(key); keys[i] != null; i = (i + 1) % m)
            if (keys[i].equals(key))
                return vals[i];
        return null;
    }

    public void delete(Key key) {
        if (key == null) throw new IllegalArgumentException("argument to delete() is null");
        if (!contains(key)) return;

        // find position i of key
        int i = hash(key);
        while (!key.equals(keys[i])) {
            i = (i + 1) % m;
        }

        // delete key and associated value
        keys[i] = null;
        vals[i] = null;

        // rehash all keys in same cluster
        i = (i + 1) % m;
        while (keys[i] != null) {
            // delete keys[i] an vals[i] and reinsert
            Key   keyToRehash = keys[i];
            Value valToRehash = vals[i];
            keys[i] = null;
            vals[i] = null;
            n--;
            put(keyToRehash, valToRehash);
            i = (i + 1) % m;
        }

        n--;

        // halves size of array if it's 12.5% full or less
        if (n > 0 && n <= m/8) resize(m/2);
    }

    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<Key>();
        for (int i = 0; i < m; i++)
            if (keys[i] != null) queue.enqueue(keys[i]);
        return queue;
    }
}
```

#### 轨迹图

![](/assets/searching/hashTable_trace2.png)

#### 复杂度

能够动态调整数组大小的散列表，执行任意顺序的t次查找、插入和删除所需时间和t成正比，所使用的内存量总是在表中的键的总数的常数因子范围内。

![](/assets/searching/hashTable_compare.png)

