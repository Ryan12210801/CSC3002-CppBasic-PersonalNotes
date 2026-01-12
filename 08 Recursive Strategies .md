# 8. Recursive Strategies

## 一、Towers of Hanoi（必考・递归思想标杆）

[https://www.researchgate.net/publication/226586215/figure/fig3/AS%3A366568216252418%401464408081094/The-Tower-of-Hanoi-puzzle-consists-of-three-pegs-and-several-disks-of-varying-size-Given.png?utm_source=chatgpt.com](https://images.openai.com/thumbnails/url/8XGCr3icu5meUVJSUGylr5-al1xUWVCSmqJbkpRnoJdeXJJYkpmsl5yfq5-Zm5ieWmxfaAuUsXL0S7F0Tw7OLAzyCnJ1NPTIzS8Jcza2SPfwDcw38HMzzTDLC0r00q3ydCr19MyydDR1VCu2NTQAAPY2I-E?utm_source=chatgpt.com)

### 1. 问题建模（Exam 必问）

递归函数必须明确 4 个参数（这是**得分点**）：

- `n` : number of disks
- `from` : source spire
- `to` : destination spire
- `aux` : auxiliary spire

---

### 2. Recursive Decomposition（最关键）

把 **“搬 n 个盘子”** 转化为 3 件事：

1. Move top `n−1` disks from **A → C** (using B)
2. Move bottom disk from **A → B**
3. Move `n−1` disks from **C → B** (using A)

👉 注意：**中间那一步（move 1 disk）不是递归**

---

### 3. Base Case

```
n == 1

```

- 直接移动一个盘
- 不再拆分

---

### 4. 为什么一定正确？（理解题）

- 每一步都把规模从 `n` 降为 `n−1`
- 子问题与原问题 **same form**
- 最终一定到达 `n = 1`

---

### 5. Complexity

- Number of moves: `2^n − 1`
- Time Complexity: **O(2^n)**

> Hanoi 是“指数复杂度不可避免”的经典例子
> 

---

## 二、Generating Subsets（极其重要）

### 1. 问题本质

对每一个元素，只有两种选择：

- **include**
- **exclude**

👉 这叫 **Inclusion–Exclusion Pattern**

---

### 2. Subset Tree（理解核心）

以 `"abc"` 为例，递归其实在“走一棵二叉树”：

```
            ""
          /     \
       exclude   include a
        "bc"        "a"
       /   \       /   \
     ...   ...   ...   ...

```

- 每一层处理一个字符
- 每一层分裂成 **2 条分支**
- 总节点数：`2^n`

---

### 3. Recursive Strategy（语言描述，考试可直接写）

- Base Caseset is empty → return empty string
- Recursive Case：
    - generate subsets without first element
    - copy them and add first element in front

---

### 4. Complexity（必会）

- Number of subsets: `2^n`
- Time: **O(2^n)**
- Space: **O(2^n)**

---

## 三、Subset-Sum Problem（容易混）

[https://i.sstatic.net/1L2eY.jpg?utm_source=chatgpt.com](https://images.openai.com/thumbnails/url/oBkIYnicu5meUVJSUGylr5-al1xUWVCSmqJbkpRnoJdeXJJYkpmsl5yfq5-Zm5ieWmxfaAuUsXL0S7F0Tw6uyHPycfVOyXbPyPZJigryyXWu8ir2TfYt8vWO9Iu3yE-sMvEtDaqMMkkKVCu2NTQAABzsJYA?utm_source=chatgpt.com)

### 1. 比普通 Subset 难在哪里？

多了一个 **target value**

> 不只是“生成”，而是“判断是否存在”
> 

---

### 2. 关键递归思想（必背）

选定一个 element 后，只有两种可能：

1. **exclude element**
    
    → 能否用 rest 组成 `target`
    
2. **include element**
    
    → 能否用 rest 组成 `target − element`
    

这一步是逻辑核心。

---

### 3. Base Case（非常容易写错）

```
if set is empty:
    return (target == 0)

```

解释：

- 没元素可选了
- 若 target 刚好被凑到 0 → success
- 否则 → failure

---

### 4. 为什么是 OR？

```cpp
exclude || include

```

因为：

> 只要存在一个合法子集即可
> 

---

### 5. Complexity（理解题高频）

- 每个元素：2 种选择
- Time Complexity：**O(2^n)**

---

## 四、Generating Permutations（高频 + 易乱）

[https://i.sstatic.net/SDdLO.jpg?utm_source=chatgpt.com](https://images.openai.com/thumbnails/url/AdsIUnicu5mZUVJSUGylr5-al1xUWVCSmqJbkpRnoJdeXJJYkpmsl5yfq5-Zm5ieWmxfaAuUsXL0S7F0Tw5y9g4pdSx1tUgOcLYwy44MyU1yDq5KjK_I8Sp3C0wxyqxw9i708Tcy8Q8O8c80jyxXKwYAVQsl5Q?utm_source=chatgpt.com)

[https://i.sstatic.net/yrTFw.gif?utm_source=chatgpt.com](https://images.openai.com/thumbnails/url/NkiMU3icu5meUVJSUGylr5-al1xUWVCSmqJbkpRnoJdeXJJYkpmsl5yfq5-Zm5ieWmxfaAuUsXL0S7F0Tw42NQz0c02ucDIL8MgJCA8wLos0DkiMNInPKi_2D8oNcfasMPUyzUzTjQh3VCu2NTQAAPRzJDk?utm_source=chatgpt.com)

### 1. 本质区别（和 Subset 对比）

| Problem | 每一层做什么 |  |
| --- | --- | --- |
| Subset | choose / not choose |  |
| Permutation | choose **谁当第一个** |  |

---

### 2. Recursive Decomposition（必会说）

- 从 `n` 个字符中：
    - 选一个作为 first character
    - 对剩余 `n−1` 个字符递归生成 permutations
    - 把 first character 加到前面

---

### 3. 递归树的形态

```
"ABC"
 ├─ A + perm("BC")
 ├─ B + perm("AC")
 └─ C + perm("AB")

```

- 第一层：`n` 个分支
- 第二层：`n−1`
- 总结果数：`n!`

---

### 4. Base Case

```
str == ""

```

- 返回 `{""}`
- 这是“递归拼接”的起点

---

### 5. Complexity（必背）

- Number of permutations: `n!`
- Time: **O(n!)**
- Space: **O(n!)**

---

## 五、Graphical Recursion（理解即可）

### 1. 和前面递归的不同点

- 不返回数值
- 不“解问题”，而是“生成结构”

---

### 2. Base Case 的变形

在 graphical recursion 中：

> Base Case = Stopping Criteria
> 

例如：

- region area < MIN_AREA
- edge length < MIN_EDGE

---

### 3. Mondrian / Fractals 的共性

- self-similar structure
- same pattern at different scales
- recursion naturally fits

---

## 六、全章核心套路总结（期末用）

### 三种最重要的递归模式

1. **Linear Recursion**
    - Hanoi（1 次递归调用）
2. **Binary Branching Recursion**
    - Subset / Subset-Sum（include / exclude）
3. **Multi-branch Recursion**
    - Permutations（n branches）

---

### 一句话终极总结（可背）

> Most recursive strategies rely on reducing problem size, branching on choices, and trusting that recursive calls correctly solve smaller subproblems
>