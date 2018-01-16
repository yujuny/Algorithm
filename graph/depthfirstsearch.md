# 深度优先搜索

---

## 定义

深度优先搜索是一种经典的递归方法，要搜索一幅图，只需要用一个递归方法来遍历所有顶点。在访问其中一个顶点时：

* 将它标记为已访问。
* 递归地访问它的所有没有被标记过的邻居顶点。

## 接口

![](/assets/graph/depthFirstSearch_interface.png)

## 代码

```
public class DepthFirstSearch {
    private boolean[] marked;    // marked[v] = is there an s-v path?
    private int count;           // number of vertices connected to s

    public DepthFirstSearch(Graph G, int s) {
        marked = new boolean[G.V()];
        validateVertex(s);
        dfs(G, s);
    }

    private void dfs(Graph G, int v) {
        count++;
        marked[v] = true;
        for (int w : G.adj(v)) {
            if (!marked[w]) {
                dfs(G, w);
            }
        }
    }

    public boolean marked(int v) {
        validateVertex(v);
        return marked[v];
    }

    public int count() {
        return count;
    }

    private void validateVertex(int v) {
        int V = marked.length;
        if (v < 0 || v >= V)
            throw new IllegalArgumentException("vertex " + v + " is not between 0 and " + (V-1));
    }
}
```



