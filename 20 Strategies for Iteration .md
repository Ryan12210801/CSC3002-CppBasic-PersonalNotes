# 20. Strategies for Iteration

## 20.0 C++ Standard Library Overview（背景）

C++ 标准库（`std` namespace）提供：

- **Containers**：`vector`, `map`, `set`, `queue`, `stack`
- **Algorithms**：`<algorithm>`
- **Iterators**：`<iterator>`
- **Functional**：`<functional>`
- **Streams**：`iostream`, `fstream`, `sstream`

📌 本章关注的是：

**Containers + Iterators + Algorithms + Functions**

---

## 20.1 Iterating over a Collection（为什么需要 Iterator）

### 问题背景

- `vector`, `array`：可以 index-based loop
- `set`, `map`, `tree-based structure`：**没有自然 index**

核心问题：

> 如果一个数据结构没有顺序索引，如何统一遍历？
> 

---

### 解决方案：Iterator

**Iterator** 是一种抽象工具：

- 每次“交付”一个元素
- 屏蔽底层存储结构

---

## 20.2 Using Iterators in C++

### 1. Range-based for（C++11，最推荐）

```cpp
for (string word : english) {
    ...
}

```

本质：

- 编译器自动展开为 iterator 版本
- **语义清晰、最安全**

---

### 2. Iterator-based for（经典形式）

```cpp
for (Lexicon::iterator it = english.begin(); it != english.end();it++)   {
 
     
    string word = *it;
}

```

必须理解的点：

- `begin()`：指向第一个元素
- `end()`：指向“最后一个元素之后”
- `*it`：dereference
- `it++`：移动到下一个元素

---

### 3. `it` vs `it->`

```cpp
string word = *it;
int len = it->length();

```

解释：

- `it->x` 等价于 `(*it).x`
- iterator 的行为 **模仿 pointer**

---

## 20.3 The Iterator Hierarchy

STL iterator 并非一种，而是一个**能力层级**：

- InputIterator
- OutputIterator
- ForwardIterator
- BidirectionalIterator
- RandomAccessIterator

例子：

- `Vector::iterator` → RandomAccessIterator
    
    支持：`it + n`, `it < other`
    
- `Lexicon::iterator` → InputIterator
    
    只能顺序遍历
    

📌 **期末重点理解**

> 不要假设所有 iterator 都支持 +, <
> 

---

## 20.4 Implementing Iterators（理解重点，不考手写）

### Vector iterator 为什么“容易实现”

- 底层是 **dynamic array**
- iterator 只需维护：
    - 指向 vector 的指针
    - 当前 index

```cpp
const Vector* vp;
int index;

```

---

### Tree-based iterator 为什么“难”

- Tree 是 recursive structure
- Iterator 是 iterative interface
- 需要把 recursion → iteration（非常复杂）

结论（PPT 原意）：

> Iterator implementation should be left to experts.
> 

---

## 20.5 Using Functions as Data Values（重要思想）

### 1. Function Pointer（Callback Function）

核心思想：

> Function 有地址 → 可以作为参数传递
> 

```cpp
voidplot(GWindow& gw, double (*fn)(double));

```

- `fn` 是函数指针
- client 决定传 `sin`, `cos`, 或自定义函数

📌 这与模板中的 “virtual type” 类似：

- 行为由 client 决定
- 接口由 library 定义

---

## 20.6 Mapping Functions（mapAll）

### 1. 什么是 Mapping Function

Mapping function：

- 对集合中**每个元素**调用一次 client 提供的函数

```cpp
template <typename ValueType>
voidmapAll(void (*fn)(ValueType));

```

---

### 2. mapAll vs iterator

| Iterator | mapAll |
| --- | --- |
| 灵活 | 简单 |
| client 控制遍历 | collection 控制遍历 |
| 易扩展 | 难传额外参数 |

---

### 3. mapAll 的核心问题（重难点）

> Client 往往需要向 callback 传额外信息
> 

例如：

“打印长度为 k 的单词”

callback 只接收 `string`，但需要 `k`

---

## 20.7 Passing Data to Mapping Functions（重难点）

### 方案一：增加参数（不优雅）

```cpp
mapAll(fn, k);

```

- 会导致大量 overload
- 接口膨胀

---

### 方案二：Function Object（Functor）【重点】

```cpp
classListKLetterWords {
public:
ListKLetterWords(int k);
voidoperator()(string word);
};

```

使用：

```cpp
lex.mapAll(ListKLetterWords(k));

```

解释：

- Object 内部保存 state（`k`）
- `operator()` 让对象“像函数一样被调用”

---

## 20.8 STL Algorithm Library

STL algorithms 的共同特点：

- **不关心 container**
- **只依赖 iterator**

---

### 常考算法（必须熟）

- `sort(begin, end)`
- `find(begin, end, value)`
- `count`, `count_if`
- `min_element`, `max_element`
- `for_each`
- `replace`, `reverse`

例：

```cpp
sort(v.begin(), v.end());
sort(v.begin(), v.end(),greater<int>());

```

---

### 为什么 STL 用 iterator 而不是 container？

> Decoupling
> 
> 
> algorithm ≠ data structure
> 

---

## 20.9 Functional Programming in C++（理解层面）

### 核心特征

- Functions as values
- Nested function calls
- Minimize side effects
- Avoid explicit loops

---

### 对比示例

Procedural：

```cpp
for (...)if (...)

```

Functional：

```cpp
count_if(v.begin(), v.end(), predicate);

```

---

## 20.10 Programming Paradigms（总结性内容）

C++ 是 **multi-paradigm language**：

- Procedural
- Object-Oriented
- Functional

本章体现的是：

> 从“如何遍历” → “如何抽象遍历” → “如何组合行为”
> 

---

## 期末终极总结

1. **Iterator 是遍历集合的统一抽象**
2. **Range-based for 是 iterator 的语法糖**
3. **STL algorithms 通过 iterator 解耦 container**
4. **Function pointer / functor 让行为参数化**
5. **mapAll 简单但不灵活，iterator 更通用**
6. **Functional style 强调“what”，弱化“how”**