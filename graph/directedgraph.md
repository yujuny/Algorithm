# 有向图

---

## 定义

一幅有**方向性的图（有向图）**是由一组**顶点**和一组有**方向的边**组成的，每条有方向的边都连接着有序的一对顶点。

在一幅有向图中，一个顶点的**出度**为由该顶点指出的边的总数；一个顶点的**入度**为指向该顶点的边的总数。

一条有向边的第一个顶点称为它的**头**，第二个顶点则被称为它的**尾**。

在一幅有向图中，**有向路径**由一系列顶点组成，对于其中的每个顶点都存在一条边从它指向序列中的下一个顶点。

**有向环**为一条至少含有一条边且起点和终点相同的有向路径。**简单有向环**是一条不含有重复顶点和边的环。

路径或者环的**长度**即为其中所包含的边数。

![](/assets/graph/directedGraph_define1.png)

## 接口

![](/assets/graph/directedGraph_interface1.png)

## 实现

![](/assets/graph/directedGraph_trace1.png)

![](/assets/graph/directedGraph_trace2.png)

```
public class Digraph {
    private static final String NEWLINE = System.getProperty("line.separator");

    private final int V;           // number of vertices in this digraph
    private int E;                 // number of edges in this digraph
    private Bag<Integer>[] adj;    // adj[v] = adjacency list for vertex v
    private int[] indegree;        // indegree[v] = indegree of vertex v

    public Digraph(int V) {
        this.V = V;
        this.E = 0;
        indegree = new int[V];
        adj = (Bag<Integer>[]) new Bag[V];
        for (int v = 0; v < V; v++) {
            adj[v] = new Bag<Integer>();
        }
    }

    public int V() {
        return V;
    }

    public int E() {
        return E;
    }

    public void addEdge(int v, int w) {
        adj[v].add(w);
        indegree[w]++;
        E++;
    }

    public Iterable<Integer> adj(int v) {
        return adj[v];
    }

    public int outdegree(int v) {
        return adj[v].size();
    }

    public int indegree(int v) {
        return indegree[v];
    }

    public Digraph reverse() {
        Digraph reverse = new Digraph(V);
        for (int v = 0; v < V; v++) {
            for (int w : adj(v)) {
                reverse.addEdge(w, v);
            }
        }
        return reverse;
    }

    public String toString() {
        StringBuilder s = new StringBuilder();
        s.append(V + " vertices, " + E + " edges " + NEWLINE);
        for (int v = 0; v < V; v++) {
            s.append(String.format("%d: ", v));
            for (int w : adj[v]) {
                s.append(String.format("%d ", w));
            }
            s.append(NEWLINE);
        }
        return s.toString();
    }
}
```





# 可达性

---

# 定义

给定一幅有向图和一个起点S，是否存在一条从S到达给定顶点V的有向路径。

## 接口 ![](/assets/graph/directedGraph_interface2.png)

## 代码

```
public class DirectedDFS {
    private boolean[] marked;  // marked[v] = true if v is reachable
                               // from source (or sources)
    private int count;         // number of vertices reachable from s

    public DirectedDFS(Digraph G, int s) {
        marked = new boolean[G.V()];
        validateVertex(s);
        dfs(G, s);
    }

    public DirectedDFS(Digraph G, Iterable<Integer> sources) {
        marked = new boolean[G.V()];
        validateVertices(sources);
        for (int v : sources) {
            if (!marked[v]) dfs(G, v);
        }
    }

    private void dfs(Digraph G, int v) { 
        count++;
        marked[v] = true;
        for (int w : G.adj(v)) {
            if (!marked[w]) dfs(G, w);
        }
    }

    public boolean marked(int v) {
        validateVertex(v);
        return marked[v];
    }

    public int count() {
        return count;
    }
}
```

## 轨迹图

![](/assets/graph/directedGraph_trace3.png)



