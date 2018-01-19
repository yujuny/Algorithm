# 无向图

---

## 定义

图是由一组顶点和一组能够将两个顶点相连的边组成的。

![](/assets/graph/undirectedGraph_define1.png)

当两个顶点通过一条边相连时，我们称这两个顶点是**相邻**的，并称该连接**依附于**这两个顶点。

某个顶点的**度数**即为依附于它的边的总和。**子图**是由一幅图的所有边的一个子集组成的图。

**路径**是由边顺序连接的一系列顶点。**简单路径**是一条没有重复顶点的路径。**环**是一条至少含有一条边且起点和终点相同的路径。**简单环**是一条不含有重复顶点和边的环。路径或环的**长度**为其中所包含的边数。

如果从任意一个顶点都存在一条路径到达另一个任意顶点，我们称这幅图是**连通图**。一幅**非连通的图**由若干连通的部分组成，它们都是其极大连通子图。

**无环图**是一种不包含环的图。

**树**是一幅无环连通图。互不相连的树组成的集合称为**森林**。连通图的**生成树**是它的一幅子图，它含有图中的所有顶点且是一棵树。图的**生成树森林**是它的所有连通子图的生成树的集合。

![](/assets/graph/undirectedGraph_define2.png)

## 接口

![](/assets/graph/undirectedGraph_interface.png)

## 实现

邻接表数组。将每个顶点的所有相邻顶点都保存在该顶点对应的元素所指向的一张链表中。

特点如下：

1. 使用的空间和V+E成正比
2. 添加一条边所需的时间为常数
3. 遍历顶点V的所有相邻顶点所需的时间和V的度数成正比

![](/assets/graph/undirectedGraph_trace1.png)![](/assets/graph/undirectedGraph_trace2.png)

```
public class Graph {
    private static final String NEWLINE = System.getProperty("line.separator");

    private final int V;
    private int E;
    private Bag<Integer>[] adj;

    public Graph(int V) {
        this.V = V;
        this.E = 0;
        adj = (Bag<Integer>[]) new Bag[V];
        for (int v = 0; v < V; v++) {
            adj[v] = new Bag<Integer>();
        }
    }

    public Graph(In in) {
        this.V = in.readInt();
        adj = (Bag<Integer>[]) new Bag[V];
        for (int v = 0; v < V; v++) {
            adj[v] = new Bag<Integer>();
        }
        int E = in.readInt();
        for (int i = 0; i < E; i++) {
            int v = in.readInt();
            int w = in.readInt();
            addEdge(v, w); 
        }
    }

    public Graph(Graph G) {
        this(G.V());
        this.E = G.E();
        for (int v = 0; v < G.V(); v++) {
            // reverse so that adjacency list is in same order as original
            Stack<Integer> reverse = new Stack<Integer>();
            for (int w : G.adj[v]) {
                reverse.push(w);
            }
            for (int w : reverse) {
                adj[v].add(w);
            }
        }
    }

    public int V() {
        return V;
    }

    public int E() {
        return E;
    }

    public void addEdge(int v, int w) {
        E++;
        adj[v].add(w);
        adj[w].add(v);
    }

    public Iterable<Integer> adj(int v) {
        return adj[v];
    }

    public int degree(int v) {
        return adj[v].size();
    }


    public String toString() {
        StringBuilder s = new StringBuilder();
        s.append(V + " vertices, " + E + " edges " + NEWLINE);
        for (int v = 0; v < V; v++) {
            s.append(v + ": ");
            for (int w : adj[v]) {
                s.append(w + " ");
            }
            s.append(NEWLINE);
        }
        return s.toString();
    }
}
```



