# 7.Introduction to Recursion

## 1. Recursion 的定义

**Recursion**：

Solving a problem by breaking it into **smaller sub-problems of the same form**.

关键点：

- sub-problem 与 original problem **形式相同（same form）**
- function **calls itself** (directly or indirectly)
- 是一种特殊的 **Divide and Conquer**

区分点（常考）：

- 如果子问题不是 same form → 只是 top-down design，不是 recursion

---

## 2. Recursive Paradigm（通用结构）

几乎所有递归函数都符合以下模式：

```cpp
if (simplecase) {
// Base Case
    solve directly
}else {
// Recursive Case
break into smaller sub-problems
    solve recursively
    combine results
}

```

写递归时必须明确：

- **Base Case 是什么**
- **Recursive Decomposition 是什么**

---

## 3. Base Case & Recursive Case

- **Base Case**
    
    不再递归、可直接得到答案的最简单情况
    
- **Recursive Case**
    
    把问题拆成规模更小、形式相同的子问题
    

若没有 base case → **nonterminating recursion（stack overflow）**

---

## 4. Factorial（最基础示例）

数学定义：

```
n! = n × (n−1)!
0! = 1

```

递归实现：

```cpp
intfact(int n) {
if (n ==0)return1;
return n *fact(n -1);
}

```

要点：

- 递归形式更贴合数学定义
- 但需要 O(n) stack space
- iteration 可做到 O(1) space

---

## 5. Recursive Leap of Faith

**Recursive Leap of Faith**：

在写递归时，相信**所有更小规模的递归调用都会正确返回结果**，而不去追踪细节。

重要性：

- 若逐层 trace，递归会变得难以编写
- 正确方式：只关注“当前这一层”

一句话总结（可直接写简答）：

> Writing recursive programs requires believing that recursive calls work correctly for simpler inputs.
> 

---

## 6. Recursion vs Mathematical Induction

相同点：

- 都有 Base Case
- 都需要 Leap of Faith

不同点：

| Recursion | Induction |
| --- | --- |
| 写程序 | 证明公式 |
| 函数调用自身 | 假设命题对 n 成立 |
| 运行时过程 | 逻辑推理过程 |

核心理解：

> Recursion in programming corresponds closely to induction in mathematics.
> 

---

## 7. Fibonacci Function（重点 ）

定义：

```
fib(0) = 0
fib(1) = 1
fib(n) = fib(n−1) + fib(n−2)

```

朴素递归：

```cpp
intfib(int n) {
if (n <2)return n;
returnfib(n -1) +fib(n -2);
}

```

为什么效率极低：

- 大量 **overlapping subproblems**
- 时间复杂度接近 O(2^n)

---

### 改进 Fibonacci（PPT给出的）

**Tail Recursion：**

```cpp
intfib(int n) {
returnadditiveSequence(n,0,1);
}

intadditiveSequence(int n,int t0,int t1) {
if (n ==0)return t0;
if (n ==1)return t1;
returnadditiveSequence(n -1, t1, t0 + t1);
}

```

- Time: O(n)
- Space: O(n)

**Iteration（最优）：**

```cpp
intfib(int n) {
if (n <2)return n;
int f0 =0, f1 =1, f2 =1;
for (int i =3; i <= n; i++) {
        f0 = f1;
        f1 = f2;
        f2 = f0 + f1;
    }
return f2;
}

```

- Time: O(n)
- Space: O(1)

---

## 8. Euclid’s Algorithm（GCD，经典递归）

数学思想：

```
gcd(x, y) =gcd(y, x% y)

```

递归实现：

```cpp
intgcd(int x,int y) {
if (x % y ==0)return y;
returngcd(y, x % y);
}

```

考点：

- 识别 recursive decomposition
- 正确写 base case

---

## 9. Palindrome（字符串递归）

递归定义：

- length ≤ 1 → true
- 首尾字符相等，且中间是 palindrome

基本递归：

```cpp
boolisPalindrome(string str) {
int len = str.length();
if (len <=1)returntrue;
return str[0] == str[len -1] &&
isPalindrome(str.substr(1, len -2));
}

```

问题：

- `substr` 代价高

改进（用 indices）：

```cpp
boolisSubstringPalindrome(string &str, int p1,int p2) {
if (p1 >= p2)return true;
return str[p1] == str[p2] &&
isSubstringPalindrome(str, p1 +1, p2 -1);
}

```

---

## 10. Binary Search（算法题重点）

前提：

- Array **must be sorted**

思想：

- 每一步排除一半元素

递归实现：

```cpp
intbinarySearch(string key, Vector<string> &vec, int p1,int p2) {
if (p1 > p2)return-1;

int mid = (p1 + p2) /2;
if (key == vec[mid])return mid;
if (key < vec[mid])
returnbinarySearch(key, vec, p1, mid -1);
else
returnbinarySearch(key, vec, mid +1, p2);
}

```

复杂度：

- Time: O(log n)
- Space: O(log n)

---

## 11. Mutual Recursion（概念即可）

**Mutual Recursion**：

Multiple functions call each other recursively.

示例：

```cpp
boolisOdd(unsignedint n) {
if (n ==0)returnfalse;
returnisEven(n -1);
}

boolisEven(unsignedint n) {
if (n ==0)returntrue;
returnisOdd(n -1);
}

```

特点：

- 可读性差
- 通常 inefficient
- 主要考概念，不考性能优化

---

## 12. Thinking Recursively（期末检查表）

写递归前自检：

- 是否有正确的 base case？
- 问题规模是否在递减？
- 是否一定能到达 base case？
- 子问题是否 same form？
- 是否可能出现 infinite recursion？