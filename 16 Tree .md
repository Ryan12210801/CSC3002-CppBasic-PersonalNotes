# 16. Tree

---

## 0. 为什么需要 Trees（从 Map 的角度理解）

在前一章（Maps）中，我们已经见过几种实现 `get / put` 的策略：

| Strategy | get | put | 问题 |
| --- | --- | --- | --- |
| Vector + linear search | O(N) | O(N) | 太慢 |
| Sorted vector + binary search | O(log N) | O(N) | 插入慢 |
| Hashing | O(1) average | O(1) average | **无序，不能 ordered iteration** |

**关键问题：**

> 有没有一种结构，既支持
> 
> - `get / put` 都是 O(log N)
> - 又能按 key 的顺序进行 iteration？

答案：**Binary Search Tree (BST)**

---

## 1. Tree 的基本概念（Terminology）

- **root**：树的根节点
- **parent / child**：父子关系
- **siblings**：同一 parent 的节点
- **ancestor / descendant**：祖先 / 后代

这些概念在 BST、Heap、Set 中都会反复出现。

---

## 2. Trees as a Recursive Data Structure（非常重要）

PPT 中给出的程序员视角定义：

> A tree is a pointer to a node.
> 
> 
> A node is a structure that contains some number of trees.
> 

这一定义的关键点：

- **递归定义（recursive definition）**
- Tree 本身可能是 `NULL`
- Node 内部再指向子树

典型结构：

```cpp
structTreeNode {
    string key;
    Vector<TreeNode*> children;
};

```

---

## 3. Binary Search Trees（BST，本章核心）

---

### 3.1 BST 的定义（一定要会说）

Binary Search Tree 的不变式（invariant）：

> 对于任意节点：
> 
> - 左子树中所有 keys < 当前 key
> - 右子树中所有 keys > 当前 key

每个节点最多两个子树：`left` 和 `right`。

---

### 3.2 BSTNode 的基本结构

```cpp
structBSTNode {
    string key;
    BSTNode* left;
    BSTNode* right;
};

```

---

## 4. 在 BST 中查找（find / get）

### 4.1 查找算法的逻辑

```cpp
Node* findNode(Node* t, string key) {
if (t ==NULL)returnNULL;
if (key == t->key)return t;
if (key < t->key)
returnfindNode(t->left, key);
else
returnfindNode(t->right, key);
}

```

**为什么是 O(log N)（理想情况）？**

- 每一步都把搜索空间减半
- 和 binary search 思想一致

---

## 5. 插入与删除（理解层面即可）

### 5.1 插入（put）

- 类似查找
- 找到 `NULL` 位置
- 新建节点挂上去

---

### 5.2 删除（remove，理解为三种情况）

1. **Leaf node**：直接删除
2. **只有一个 child**：用 child 替换
3. **两个 children**：
    - 用 **predecessor**（左子树最大）
    - 或 **successor**（右子树最小）替换

期末通常考**思想，不要求完整代码**。

---

## 6. Balance（BST 的致命问题）

### 6.1 什么是“坏”的 BST

如果 keys 按顺序插入：

```
A →B →C →D →E

```

BST 会退化成：

- 类似 linked list
- `get / put` 变成 O(N)

---

### 6.2 Balanced Tree 的概念

PPT 中的定义（理解即可）：

> A BST is balanced if
> 
> 
> the heights of left and right subtrees differ by at most one
> 
> and both subtrees are themselves balanced.
> 

**结论：**

- Balanced BST → O(log N)
- Unbalanced BST → O(N)

---

### 6.3 Tree-Balancing Algorithms（不要求细节）

提到即可：

- AVL Tree
- Red-Black Tree
- 2-3 Tree
- Splay Tree

你只需要知道：

> 可以通过算法，在插入时保持 BST 平衡
> 

---

## 7. Map Using BST（非常重要）

Stanford `Map`（以及 `std::map`）的核心特性：

- 内部使用 **BST**
- `get / put`：O(log N)
- **iteration is ordered**

对比：

| Structure | Ordered iteration |
| --- | --- |
| Map (BST) | Yes |
| HashMap | No |

---

## 8. Tree Traversal（必考概念）

---

### 8.1 Depth-First Traversals

### Preorder

```
node →left →right

```

用途：

- 拷贝树
- 打印结构

---

### Inorder（BST 特有重要性）

```
left → node →right

```

**关键结论（必背）：**

> Inorder traversal of a BST produces keys in sorted order
> 

这是 BST Map 优于 HashMap 的根本原因之一。

---

### Postorder

```
left →right → node

```

用途：

- delete/free entire tree（析构）

---

### 8.2 Level-order Traversal（BFS）

- 按层访问
- 不属于 DFS
- 常用于 Heap

---

## 9. Priority Queue & Partially Ordered Trees

---

### 9.1 Priority Queue 的需求

- 出队顺序取决于 priority
- Dijkstra / Kruskal 等算法核心结构

朴素实现（array / list）：

- enqueue / dequeue：O(N)

---

### 9.2 Partially Ordered Tree（Heap）

性质：

1. Tree 是 **complete**
2. Parent 的 priority ≥ children
3. **不是 BST**

---

### 9.3 Heap 的数组表示（非常重要）

索引关系（必须记）：

```
parent(i)      = (i - 1) / 2
leftChild(i)   = 2*i + 1
rightChild(i)  = 2*i + 2

```

---

## 10. Heapsort（理解层面）

核心步骤：

1. Heapify（build heap）
    - bottom-up：O(N)
2. 重复：
    - swap root 和 last
    - siftDown
3. 得到有序数组

性质：

- Worst-case：O(N log N)
- In-place
- Not stable