# 10. Algorithmic Analysis

## 0. 本章在考什么（先抓主线）

这一章核心不是“会不会写代码”，而是：

1. **如何分析算法效率（Computational Complexity）**
2. **为什么有些算法慢、有些快**
3. **为什么 recursion 能“拯救” sorting（Merge Sort）**
4. **如何理解 Big-O、Worst-case、Average-case**
5. **Quick Sort 为什么快，但又不“完美”**

---

## 1. The Sorting Problem（背景）

**Sorting problem**：

> Arrange elements of an array / vector into order.
> 

重要性：

- 几乎所有实际系统都会用到 sorting
- 不同 sorting algorithm **效率差异巨大**
- 大规模数据下差异是“数量级”的

---

## 2. Selection Sort（基础但重要）

### 2.1 核心思想（English 描述）

At each step:

1. Find the smallest element in the unsorted part
2. Swap it into its correct position

---

### 2.2 代码结构的本质

```cpp
for lh =0 to n-1:
    rh = index of smallest element in [lh, n-1]
swap(vec[lh], vec[rh])

```

- 外层 loop：决定当前位置 `lh`
- 内层 loop：扫描剩余所有元素

---

### 2.3 为什么 Selection Sort 慢？（重点）

第 1 轮：比较 `n` 次

第 2 轮：比较 `n-1` 次

…

第 n 轮：比较 `1` 次

总比较次数：

1+2+3+⋯+n=n(n+1)21 + 2 + 3 + \dots + n = \frac{n(n+1)}{2}

1+2+3+⋯+n=2n(n+1)

👉 **Quadratic growth（平方级增长）**

---

### 2.4 Complexity

- Time Complexity：**O(N²)**（worst / average / best 都一样）
- Space Complexity：**O(1)**（in-place）
- 特点：简单、稳定性差、效率低

---

## 3. Measuring Time vs. Counting Operations（方法论）

### 3.1 为什么不直接测时间？

- `time()` 精度有限
- 系统 background tasks 会干扰
- 数据分布会影响结果

👉 实际测时只能做 **empirical observation**

---

### 3.2 理论分析更可靠

**Algorithmic analysis** 的标准方法：

- 找出执行次数最多的代码段
- 统计其执行次数随 N 的增长关系

---

## 4. Big-O Notation（核心理论)

### 4.1 定义（简化版）

**Big-O** 描述的是：

> 当 N 很大时，算法运行时间的增长趋势
> 

例如：

- Linear search → O(N)
- Selection sort → O(N²)

---

### 4.2 Big-O 的两条简化规则（必背）

写 Big-O 时：

1. **忽略常数因子**
2. **只保留最高阶项**

例如：

- O(3N² + 5N + 7) → **O(N²)**

---

## 5. Worst-case vs Average-case（常考概念题）

### 5.1 定义

- **Worst-case complexity**
    
    → 算法在任何输入下的上界（guarantee）
    
- **Average-case complexity**
    
    → 对所有输入的统计期望（难算）
    

---

### 5.2 为什么 Worst-case 更重要？

- 给出 **绝对保证**
- 很多实际情况中：
    - Worst-case 发生得并不少
- Average-case 往往 ≈ Worst-case（如 linear search）

👉 **考试默认：分析 worst-case**

---

## 6. Recursion to the Rescue：Merge Sort（重难点）

### 6.1 为什么需要新算法？

Selection sort 的问题：

- O(N²) 在大数据下不可接受
- N × 2 → 时间 × 4

👉 需要 **subproblem-based strategy**

---

### 6.2 Merge Sort 的核心思想

1. Divide the vector into two halves
2. Sort each half recursively
3. Merge the two sorted halves

这是典型的 **Divide and Conquer**

---

### 6.3 Merge Sort 的递归结构

```cpp
sort(vec):
if size <=1:return
    split vec into v1, v2
sort(v1)
sort(v2)
merge(v1, v2)

```

---

### 6.4 为什么是 O(N log N)？（重点理解）

- 每一层 recursion：
    - merge 总成本是 **O(N)**
- 总共有多少层？
    - 每次对半分
    - 层数 = **log₂ N**

所以：

T(N)=N×log⁡NT(N) = N \times \log N

T(N)=N×logN

👉 **这是 comparison-based sorting 的理想复杂度**

---

### 6.5 Merge Sort 的特点

- Time Complexity：**O(N log N)**（稳定）
- Space Complexity：**O(N)**（not in-place）
- 特点：稳定、可预测、适合大数据

---

## 7. Standard Complexity Classes（概念）

常见复杂度增长顺序（从好到坏）：

O(1)<O(log⁡N)<O(N)<O(Nlog⁡N)<O(N2)<O(2N)O(1) < O(\log N) < O(N) < O(N \log N) < O(N^2) < O(2^N)

O(1)<O(logN)<O(N)<O(NlogN)<O(N2)<O(2N)

理论 CS 中：

- **Polynomial time** → tractable
- **Exponential time** → intractable

---

## 8. Quick Sort（重点 )

### 8.1 核心思想

Quick Sort 分两步：

1. **Partition**
    - 选一个 pivot
    - 左边 < pivot
    - 右边 ≥ pivot
2. **Recursively sort** 左右两部分

---

### 8.2 Partition 是灵魂

Partition 的目标不是排序，而是：

> 把 pivot 放到最终正确的位置
> 

---

### 8.3 Recursive Structure

```cpp
quicksort(vec, start, end):
if start >= end:return
    boundary =partition(...)
quicksort(left)
quicksort(right)

```

---

### 8.4 Complexity（非常重要）

- **Average-case**：O(N log N)
- **Worst-case**：O(N²)
    - 例如：数组已经有序，但 pivot 选得很差

⚠️ 这是 Quick Sort 最大的理论缺点

---

### 8.5 为什么 Quick Sort 仍然很快？

- In-place（O(1) extra space）
- Cache-friendly
- 常数因子小
- 实际表现常优于 Merge Sort

---

## 9. Other Important Concepts（概念题）

### 9.1 O vs Ω vs Θ

- O：upper bound
- Ω：lower bound
- Θ：tight bound（上下界一致）

---

### 9.2 Comparison Sorting 的下界

- Any comparison-based sort：
    - Worst-case ≥ **Ω(N log N)**

👉 Merge Sort / Quick Sort 已是理论最优级别

---

### 9.3 常见对比维度（记关键词）

- Time vs Space
- In-place vs Not-in-place
- Stable vs Unstable
- Recursive vs Iterative

---

## 10. 本章终极总结（期末可背）

> Algorithmic analysis studies how running time grows with input size.
> 
> 
> Quadratic algorithms like selection sort do not scale, while divide-and-conquer algorithms such as merge sort achieve O(N log N).
> 
> Quicksort is fast in practice but has a quadratic worst case.
>