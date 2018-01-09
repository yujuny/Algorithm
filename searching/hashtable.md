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



