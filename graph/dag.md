# 有向无环图

---

## 定义

有向无环图是一幅不含有环的有向图。

## 调度问题

给定一组任务并安排他们的执行顺序，限制条件是这些任务的执行方法的起始时间。最重要的一种限制条件叫做优先级限制，它指明了哪些任务必须在哪些任务之前完成。

![](/assets/graph/dag_define1.png)

![](/assets/graph/dag_define2.png)

**拓扑排序。**给定一幅有向图，将所有的顶点排序，使得所有的有向边均从排在前面的元素指向排在后面的元素。

![](/assets/graph/dag_define3.png)

## 有向图中的环

如果一个有优先级限制的问题中存在有向环，那么这个问题肯定是无解的。

**有向环检测**。检测给定的有向图中是否含有有向环，如果有，则按照路径的方向从某个顶点并返回自己来找到环上的所有顶点。

#### 接口

![](/assets/graph/dag_interface1.png)

#### 代码

```
public class DirectedCycle {
    private boolean[] marked;        // marked[v] = has vertex v been marked?
    private int[] edgeTo;            // edgeTo[v] = previous vertex on path to v
    private boolean[] onStack;       // onStack[v] = is vertex on the stack?
    private Stack<Integer> cycle;    // directed cycle (or null if no such cycle)

    public DirectedCycle(Digraph G) {
        marked  = new boolean[G.V()];
        onStack = new boolean[G.V()];
        edgeTo  = new int[G.V()];
        for (int v = 0; v < G.V(); v++)
            if (!marked[v] && cycle == null) dfs(G, v);
    }

    private void dfs(Digraph G, int v) {
        onStack[v] = true;
        marked[v] = true;
        for (int w : G.adj(v)) {

            // short circuit if directed cycle found
            if (cycle != null) return;

            // found new vertex, so recur
            else if (!marked[w]) {
                edgeTo[w] = v;
                dfs(G, w);
            }

            // trace back directed cycle
            else if (onStack[w]) {
                cycle = new Stack<Integer>();
                for (int x = v; x != w; x = edgeTo[x]) {
                    cycle.push(x);
                }
                cycle.push(w);
                cycle.push(v);
            }
        }
        onStack[v] = false;
    }

    public boolean hasCycle() {
        return cycle != null;
    }

    public Iterable<Integer> cycle() {
        return cycle;
    }
}
```

#### 轨迹图

![](/assets/graph/dag_trace1.png)

![](/assets/graph/dag_trace2.png)

## 拓扑排序

当且仅当一幅有向图是无环图时它才能进行拓扑排序。

#### 接口

![](/assets/graph/dag_interface2.png)

#### 代码



