# 18. Graph

## 0. 本章在课程中的位置

Graph 是本课程中**最综合的数据结构**：

- 融合了：Set / Map / Tree / Priority Queue
- 算法密集（DFS / BFS / Shortest Path / MST）
- 抽象层次高（ADT + Interface design）

> 重点不是“记图”，而是：
如何表示图 + 如何系统性地遍历和计算图上的性质
> 

---

## 1. Graph 的定义（Definition & Terminology）

### 1.1 Graph 的形式化定义

> A graph consists of:
> 
- a set of **nodes**
- a set of **arcs**

说明：

- node：实体（city / page / state）
- arc：连接关系（road / link / transition）

---

### 1.2 Directed vs Undirected Graph

- **Directed graph**：arc 有方向（A → B）
- **Undirected graph**：arc 无方向（A — B）

---

### 1.3 基本术语

| Term | Meaning |
| --- | --- |
| neighbor | 与某 node 直接相连的节点 |
| degree | undirected graph 中的连接数 |
| in-degree | directed graph 中进入的 arc 数 |
| out-degree | directed graph 中出去的 arc 数 |
| path | 一系列 arcs 连接的节点序列 |
| cycle | 起点 = 终点的 path |
| simple path/cycle | 无重复节点 |
| strongly connected | 任意两点都有 path（有向） |
| weakly connected | 忽略方向后连通 |

---

## 2. Graph Representations

**PPT 的第一个重难点**：

> Graph 的本质差异不在节点，而在如何表示连接（connections）
> 

---

### 2.1 Adjacency List（最常用）

**思想**：

> 每个 node 存一份 “它连向谁”
> 

```
Atlanta → (Chicago, Dallas, New York)

```

**复杂度特征**：

- space：O(N + E)
- 查 neighbors：O(D)（D = degree）

**适合**：

- sparse graph（D << N）
- 实际工程中最常见

📌 **PPT 强调点**：

在 dense graph 中，D → N，效率下降。

---

### 2.2 Adjacency Matrix（查连接最快）

**思想**：

> 用一个 N×N 的表记录是否有 arc
> 
- matrix[i][j] = true / cost

**复杂度特征**：

- space：O(N²)
- 查连接：O(1)

**适合**：

- dense graph
- 小规模 N

📌 **关键 trade-off**：

> 用空间换时间
> 

---

### 2.3 Set of Arcs（概念最贴近数学）

**思想**：

```cpp
Set<string> nodes;
Set<string> arcs;

```

优点：

- 概念清晰
- 贴近数学定义

缺点：

- 找 neighbors 必须遍历所有 arcs
- 需要支持 iteration 的 Set

---

## 3. Low-level Graph Abstraction（重要设计思想）

PPT 给出一个**低层图接口**：`graphtypes.h`

```cpp
structNode {
    string name;
    Set<Arc*> arcs;
};

structArc {
    Node* start;
    Node* finish;
double cost;
};

structSimpleGraph {
    Set<Node*> nodes;
    Set<Arc*> arcs;
    Map<string, Node*> nodeMap;
};

```

### 3.1 这里的核心思想

- Graph = set of nodes + set of arcs
- Node / Arc **不是纯数据**
- 它们同时服务于：
    - client data
    - graph structure

📌 **这为后面的 Graph class 铺路**

---

## 4. Graph Traversals（DFS / BFS，本章算法基础）

---

### 4.1 Traversal 的目标

> Visit every node exactly once,
> 
> 
> in an order determined by connections.
> 

难点不是“怎么走”，而是：

> 如何避免重复访问（cycle）
> 

---

### 4.2 visited 的必要性（必考理解点）

Graph 不像 tree：

- 有 cycle
- 没有天然层级

所以必须：

- 用 `Set<Node*> visited`
- 或 node 内部加 visited flag

---

## 5. Depth-First Search（DFS）

### 5.1 思想

> 一条路走到黑，再回溯
> 
- 利用 recursion（implicit stack）
- 或显式 stack

---

### 5.2 递归版 DFS（PPT 代码核心）

```cpp
voidvisitUsingDFS(Node* node, Set<Node*>& visited) {
if (visited.contains(node))return;
visit(node);
    visited.add(node);
for (Arc* arc : node->arcs) {
visitUsingDFS(arc->finish, visited);
    }
}

```

📌 **关键理解点**：

- visited check 在最前面
- for-loop 决定 traversal 顺序
- 实际 DFS 顺序 **依赖 arcs 的存储顺序**

---

### 5.3 DFS 的典型用途

- 判断连通性
- 枚举路径
- cycle detection（扩展）

---

## 6. Breadth-First Search（BFS）

### 6.1 思想

> 一层一层向外扩散
> 
- 使用 **queue**
- 先访问距离近的节点

---

### 6.2 BFS 的本质优势（重要）

> BFS finds shortest path in terms of number of edges
> 

（无权图）

---

### 6.3 BFS vs DFS 对比

| DFS | BFS |
| --- | --- |
| stack / recursion | queue |
| 深度优先 | 层次优先 |
| 不保证最短路径 | 保证最少 hop |
| 结构探索 | 距离分析 |

---

## 7. Graph Interface Design（抽象层重头戏）

---

### 7.1 Graph 与普通 collection 的本质区别

普通容器：

- 元素彼此独立

Graph：

- Node 和 Arc **相互依赖**
- Node 必须知道 arcs
- Arc 必须知道 endpoints

👉 **Graph 的元素是“结构性元素”**

---

## 8. Three Strategies for Graph Abstraction（必考设计题）

---

### 8.1 Low-level structures

- client 直接用 `Node / Arc`
- 最大自由度
- 最少支持

---

### 8.2 Hybrid strategy（PPT 推荐）

```cpp
template <typename NodeType,typename ArcType>
classGraph { ... }

```

- Graph 提供算法
- Node / Arc 类型由 client 定义
- C++ 中非常实用

📌 **这是 Stanford 风格、也是你这门课的核心设计哲学**

---

### 8.3 Class-based approach

- Node / Arc 是基类
- client 用 inheritance 扩展

PPT 指出：

> 在 C++ 中 不理想
> 
> 
> （内存 + inheritance 复杂）
> 

---

## 9. Dijkstra’s Algorithm

---

### 9.1 问题背景

> 最短路径问题
> 
> 
> 暴力搜索：O(2^N)，不可行
> 

---

### 9.2 核心思想（一定要理解）

Dijkstra ≈ BFS + Priority Queue

- queue 按 **total cost** 排序
- dequeue 的节点 → distance fixed
- 不允许 negative edges

---

### 9.3 本质 invariant

> Once a node is dequeued,
> 
> 
> its shortest distance is final.
> 

---

## 10. Kruskal’s Algorithm（MST）

---

### 10.1 Minimum Spanning Tree（定义）

> A set of arcs that:
> 
- connects all nodes
- has minimum total cost
- contains no cycles

---

### 10.2 Kruskal 的步骤

1. Sort arcs by increasing cost
2. 从小到大尝试加入
3. 如果 endpoints 已连通 → skip
4. 否则加入，并 merge sets

---

### 10.3 Forest + Set 的作用（关键）

- 每个 component 是一个 set
- 判断 cycle = 是否在同一 set
- 本质：**Union-Find 思想**

---

## 11. PageRank（理解层面即可）

---

### 11.1 核心思想

> A page is important
> 
> 
> if important pages link to it
> 

---

### 11.2 Random Surfer Model

- 用户随机点链接
- PageRank = 被访问的概率

---

### 11.3 数学本质（知道即可）

- Markov process
- 稳态概率分布
- iterative computation

---

## 12. 期末速记总结

- Graph = nodes + arcs
- Representation 决定效率
- Adjacency list 最常用
- DFS：recursion / stack
- BFS：queue / shortest hop
- visited 是 traversal 核心
- Dijkstra：shortest path（non-negative）
- Kruskal：MST + sets
- Graph abstraction = 结构 + 算法的平衡