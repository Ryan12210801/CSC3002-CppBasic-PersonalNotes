# 5.Collections & Containers

## 0. 本章在整个 CS 学习中的位置

这一章解决的是一个核心问题：

> “当我需要存很多数据时，如何选择合适的数据结构？”
> 

不是在教你写底层结构，而是教你：

- 用 **ADT 思维**选工具
- 用 **抽象接口**写干净代码
- 为后续的 **算法、系统、工程设计**打基础

---

## 1. 抽象数据类型（ADT, Abstract Data Type）

### 1.1 什么是 ADT

ADT =**一组数据 + 一组允许的操作 + 操作语义**

只规定：

- 我能存什么
- 我能做什么

不规定：

- 内部怎么实现

---

### 1.2 为什么一定要用 ADT（重点理解题）

从工程角度回答：

1. **Simplicity**
    - 使用者不用理解数组 / 链表 / 树
    - 降低认知负担
2. **Flexibility**
    - 内部实现可随时更换
    - 客户端代码不受影响
3. **Security**
    - 防止非法访问内部结构
    - 保证数据一致性

---

### 1.3 容器类的本质

- 所有容器都是 **ADT 的具体实现**
- 区别只在：
    - 访问方式
    - 操作限制
    - 使用场景

---

## 2. 泛型 / 模板（Parameterized Classes）

### 2.1 为什么需要泛型

问题：

- 如果没有泛型 → 每种类型写一个容器类

解决：

- 用 template / <T> 表示“元素类型未知”

```cpp
Vector<int>
Vector<string>
Map<string, double>

```

### 2.2 你在这门课的要求

- **会用即可**
- 不要求理解 template 语法细节

---

## 3. Vector（动态数组，核心重点）

### 3.1 Vector 是什么

- 顺序存储
- 支持下标访问
- 自动扩容
- 逻辑上是 **array 的升级版**

适合：

- 顺序数据
- 频繁访问第 i 个元素

---

### 3.2 基本操作全集（必须熟）

```cpp
Vector<T> v;

v.add(x);          // 尾部插入
v.insert(i, x);    // 指定位置插入
v.remove(i);       // 删除
v[i];              // 访问
v.set(i, x);       // 修改
v.size();
v.isEmpty();
v.clear();

```

---

### 3.3 Vector 的典型使用模式

### 模式 1：收集数据

```cpp
Vector<string> lines;
while (getline(is, line)) {
    lines.add(line);
}

```

### 模式 2：二维 / 网格结构

```cpp
Vector< Vector<int> > grid;

```

---

### 3.4 重要易错点

- `Vector< Vector<T> >` 中 **必须有空格**
- 函数参数中：
    - `Vector<T>&` 是修改原容器
    - `Vector<T>` 是拷贝（代价高）

---

### 3.5 Vector 的复杂度直觉（不用死背）

- `v[i]`：快
- 尾插：快（均摊）
- 中间插入 / 删除：慢（要移动）

---

## 4. Stack（栈，LIFO，重点）

### 4.1 栈的本质

- 只允许在一端操作
- 强制访问顺序

```
push → pop

```

---

### 4.2 栈适合解决的问题类型

- 成对结构
- 嵌套结构
- 递归 / 回溯
- 表达式求值

---

### 4.3 括号匹配问题（必考模板）

**逻辑结构比代码更重要**：

1. 扫描字符
2. 左括号 → push
3. 右括号：
    - 栈空 → false
    - pop 并检查匹配
4. 结束后：
    - 栈空 → true

---

### 4.4 常见错误

- 忘记判断栈空
- 忘记最后检查是否为空

---

## 5. Queue（队列，FIFO，重点）

### 5.1 队列的本质

- 模拟“等待”
- 时间顺序敏感

```
enqueue → dequeue

```

---

### 5.2 Queue vs Stack（常考对比）

| 结构 | 顺序 | 应用 |
| --- | --- | --- |
| Stack | LIFO | 递归、回溯 |
| Queue | FIFO | BFS、模拟 |

---

### 5.3 Queue 的典型应用

- 排队系统
- 广度优先搜索
- 任务调度
- 事件模拟

---

```cpp
#include <stack>
#include <queue>

stack<int> s;
s.push(1);
s.pop();        // 不返回
s.top();        // 查看栈顶

queue<int> q;
q.push(1);      // enqueue
q.pop();        // 不返回
q.front();      // 查看队首
```

## 6. Map（映射，重点）

### 6.1 Map 的抽象模型

```
Key → Value

```

- Key 唯一
- 通过 Key 快速查 Value

---

### 6.2 基本操作全集

```cpp
Map<K, V> mp;

mp.put(k, v);
mp[k] = v;

mp.get(k);
mp.containsKey(k);

mp.remove(k);
mp.clear();
mp.size();

```

---

### 6.3 Map 的核心限制（爱考）

- Key 必须 **可比较**
- 决定了 Map 的排序和查找方式

---

### 6.4 Map 的经典用途

- 统计频率
- 查表
- Symbol Table（变量名 → 值）
- 配置表

---

## 7. Set（集合，重点）

### 7.1 Set 的抽象定义

- 元素唯一
- 无索引
- 只关心“是否存在”

---

### 7.2 常用操作

```cpp
Set<T> s;
s.add(x);
s.remove(x);
s.contains(x);

```

---

### 7.3 Set 的数学操作（理解即可）

- 并集
- 交集
- 差集

---

### 7.4 Set 与 Map 的关系（理解题）

- Set ≈ Map 的 keys
- 常用 Map 实现 Set

---

### 7.5 Set 的工程意义

- 去重
- 快速 membership 测试
- 分类系统（字符 / 状态）

---

## 8. Iteration（统一遍历模型，重点）

### 8.1 为什么需要 iteration

- 不所有容器都有 index
- 用 iterator 统一访问方式

---

### 8.2 遍历写法

```cpp
for (auto x : container) {
    ...
}

```

或

```cpp
foreach (auto x in container) {
    ...
}

```

---

### 8.3 遍历顺序（必须背）

- Vector：顺序
- Grid：行优先
- Map：按 key 排序
- Set：按元素排序
- HashMap / HashSet：无序
- Lexicon：字母序

---

## 9. Lexicon（特殊集合）

### 9.1 Lexicon 是什么

- 只存单词
- 本质是高效的字符串 Set

---

### 9.2 Lexicon 的独特优势

- 空间效率高
- 支持前缀查询
- 字母序遍历

---

### 9.3 典型使用

```cpp
Lexicon lex("EnglishWords.txt");
lex.contains("cat");
lex.containsPrefix("pre");

```

---

## 10. 容器选择决策表（终极版）

- 顺序 + 下标 → Vector
- 后进先出 → Stack
- 先进先出 → Queue
- 键值映射 → Map
- 去重 / 判断存在 → Set
- 单词 + 前缀 → Lexicon