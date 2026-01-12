# 9.Backtracking Algorithms

## 0. 本章主线（先把逻辑抓住）

本章核心在于：

**在一个 branching structure（分支结构）中，通过 recursion 系统性地探索路径，遇到死路就 backtrack（回退）**。

三条主线：

1. **Recursive Backtracking** 的通用思想
2. **Maze / N-Queens**：搜索一条解（existence / one solution）
3. **Games（Nim, Minimax）**：在 game tree 上做最优决策

---

## 1. What is Backtracking（定义)

**Backtracking algorithm**：

> An algorithm that explores a sequence of decision points, and backtracks to try alternative choices when a path leads to a dead end.
> 

直观理解：

- 走一条路
- 不行 → 回到上一个 decision point
- 换一条路继续试

关键点：

- 表面像 iterative trial-and-error
- **本质上更适合用 recursion 来写**

---

## 2. Recursive Insight of Backtracking（本质句)

> A backtracking problem has a solution iff at least one of the smaller backtracking problems that result from making each possible initial choice has a solution.
> 

拆解：

- 当前问题 = 多个 choice 的 OR
- 只要 **有一个子问题返回 true**
- 当前问题就有解

👉 这是后面 `solveMaze`、`solveQueens`、`findGoodMove` 的统一逻辑基础。

---

## 3. Maze Solving（重点示例）

### 3.1 为什么 Right-Hand Rule 不行？

- Right-hand rule 是 heuristic
- **在有 loop 的 maze 中会 infinite loop**
- 无法保证 correctness

→ 所以要用 **systematic search（系统搜索）**

---

### 3.2 Recursive View of Maze（核心思想）

一个 maze 可解 **iff**：

> 从当前 square 出发，
> 
> 
> 存在至少一个相邻 square，
> 
> 使得“把当前 square 移除后的子 maze”是可解的。
> 

这里的 **recursive decomposition** 是：

- 固定当前位置
- 尝试向每个方向走一步
- 每走一步，问题规模变小（square 变少）

---

### 3.3 Base Cases（非常重要）

在 `solveMaze` 中有两个 simple cases：

1. `isOutside(start)`
    
    → 已经走到 maze 外面，**success**
    
2. `isMarked(start)`
    
    → 走回老路，**dead end**
    

---

### 3.4 核心代码结构（理解级）

```cpp
bool solveMaze(Maze& maze, Point start) {
    if (maze.isOutside(start)) return true;
    if (maze.isMarked(start)) return false;

    maze.markSquare(start);

    for (Direction dir = NORTH; dir <= WEST; dir++) {
        if (!maze.wallExists(start, dir)) {
            if (solveMaze(maze, adjacentPoint(start, dir))) {
                return true;
            }
        }
    }

    maze.unmarkSquare(start);
    return false;
}

```

---

### 3.5 为什么要 `unmarkSquare`？

- **mark**：防止在当前路径中形成 loop
- **unmark（backtracking）**：
    - 表示“这条路走不通”
    - 回到上层 decision point
    - 尝试其他方向

📌 考点：

- 不 unmark → 只能找 *any* path（效率高）
- unmark → 可保留正确路径（但搜索更全面）

---

## 4. Searching in a Branching Structure（抽象总结）

Maze 只是一个例子，背后是通用模型：

- 每一层 recursion = 一个 **choice point**
- execution stack 自动保存：
    - 走到哪一步
    - 哪些 choice 已尝试
    - 哪些还没试

👉 **recursion 的最大优势：自动 bookkeeping（自动记账）**

---

## 5. N-Queens Problem（重点）

### 5.1 问题回顾

- 在 `n × n` board 上放 `n` 个 queens
- 任意两个 queens：
    - 不同行
    - 不同列
    - 不同 diagonal

---

### 5.2 为什么不能硬写 n 个 for-loop？

- 不 scalable
- 只对固定 n 有效
- 无法表达“逐步放置”的逻辑

---

### 5.3 Recursive Decomposition（关键）

把问题拆成：

> 已经放了 nPlaced 个 queens，
> 
> 
> 尝试在第 `nPlaced` 列放下一个 queen。
> 

**Subproblem size**：`nPlaced`

→ 每次递归 `nPlaced + 1`

---

### 5.4 Base Case

```
nPlaced == n

```

- 所有 queens 已成功放置
- return true

---

### 5.5 核心代码逻辑（理解）

```cpp
bool solveQueens(Grid<char>& board, int nPlaced) {
    if (nPlaced == n) return true;

    for (int row = 0; row < n; row++) {
        if (queenIsLegal(board, row, nPlaced)) {
            board[row][nPlaced] = 'Q';
            if (solveQueens(board, nPlaced + 1)) return true;
            board[row][nPlaced] = ' ';
        }
    }
    return false;
}

```

📌 标准 backtracking 结构：

- try
- recurse
- undo
- try next

---

## 6. Backtracking and Games：Nim（理解重点）

### 6.1 Nim 的 recursive insight

两条 mutually recursive 定义：

- **Good move**：leave opponent in a bad position
- **Bad position**：offers no good moves

这是一个 **mutual recursion**：

```cpp
isBadPosition(n) ↔findGoodMove(n)

```

---

### 6.2 Base Case

```
nCoins == 1 → bad position

```

因为：

- 你必须拿最后一枚 coin
- 你输

---

### 6.3 findGoodMove 的本质

```cpp
for each possible move:
if opponent is in bad position:
this move is good

```

如果所有 move 都不能让对手变 bad：

- 当前 position 本身就是 bad

---

## 7. Game Trees & Minimax（重点概念题）

### 7.1 Game Tree

- 每个 node = 一个 game position
- 边 = 一个 move
- 层级交替：
    - MAX player（你）
    - MIN player（对手）

---

### 7.2 为什么不能只选最大值？

因为：

- 你 **只控制一半的 moves**
- 对手会选择 **对你最不利的 response**

---

### 7.3 Minimax Algorithm

> Choose the move that minimizes the maximum gain of your opponent.
> 

也就是：

- 你假设对手总是最聪明、最坏
- 在这种前提下，选对自己最好的路

---

### 7.4 Practical Issue（理解）

- 完整 game tree 太大（Chess, Go）
- 所以要：
    - cut off search depth
    - evaluate position（heuristic rating）

---

## 8. 本章总结

1. Backtracking = recursion + try / undo / try next
2. 本质是 **searching in a branching structure**
3. Recursion simplifies bookkeeping via call stack
4. Maze / N-Queens：search for *a* solution
5. Games / Minimax：search for *optimal* move under adversary

---

## 一句话终极总结

> Backtracking algorithms use recursion to explore a branching space of choices, automatically remembering past decisions via the execution stack and retreating when a path leads to failure
>