# 13. Efficiency and Representation

## 1. 为什么研究“老式编辑器”（Why Look at Old Editors）

### 1.1 Command-line Editor vs WYSIWYG

- **WYSIWYG editor**
    - “What You See Is What You Get”
    - 实时显示文档内容
    - 实现复杂，不适合教学分析
- **Command-line editor（如 TECO）**
    - 用户输入指令（I / D / F / B / J / E）
    - 不持续显示全文
    - **非常适合分析 data structure 对性能的影响**

📌教学意义：

> Same abstract operations, different data structures → drastically different performance
> 

---

## 2. EditorBuffer 抽象接口（Abstract Interface）

### 2.1 Editor Commands

| Command | Meaning |
| --- | --- |
| I text | Insert text at cursor |
| J | Jump cursor to beginning |
| E | Move cursor to end |
| F | Move cursor forward |
| B | Move cursor backward |
| D | Delete char after cursor |
| Q | Quit |
| H | Help |

---

### 2.2 EditorBuffer API（核心接口)

```cpp
moveCursorForward()
moveCursorBackward()
moveCursorToStart()
moveCursorToEnd()
insertCharacter(ch)
deleteCharacter()
getText()
getCursor()

```

📌 **关键抽象**

- Cursor position = **insertion point**
- Cursor ∈ [0, length]

---

## 3. 三种 EditorBuffer 实现模型

> 同一个接口，三种 data representation：
> 
1. Array-based
2. List-based
3. Two-stack-based

考试重点：

- **内部结构**
- **cursor 表示方式**
- **时间复杂度对比**
- **trade-off**

---

## 4. Array-Based Implementation（数组模型）

### 4.1 Data Representation

```cpp
char* array;// dynamic array
int capacity;// allocated size
int length;// actual characters
int cursor;// index AFTER cursor

```

- `capacity ≠ length`
- cursor 是 **index**，不是 pointer
- 使用 **dynamic allocation + expansion**

---

### 4.2 核心操作机制

### Insert（插入）

```cpp
for (i = length; i > cursor; i--)
    array[i] = array[i-1];
array[cursor] = ch;
cursor++;
length++;

```

👉 **关键点**：

- 插入点之后所有字符都要右移
- 时间复杂度取决于 cursor 后的元素数量

### Delete（删除）

```cpp
for (i = cursor+1; i < length; i++)
    array[i-1] = array[i];
length--;

```

---

### 4.3 Complexity（考试必考）

| Operation | Time |
| --- | --- |
| moveCursorForward | O(1) |
| moveCursorBackward | O(1) |
| moveCursorToStart | O(1) |
| moveCursorToEnd | O(1) |
| insertCharacter | **O(N)** |
| deleteCharacter | **O(N)** |

📌 **核心缺陷**

> 插入 / 删除 = shift array → 线性时间
> 

---

## 5. List-Based Implementation（链表模型）

### 5.1 为什么需要 Dummy Cell（高频考点）

问题：

- N characters → N+1 possible cursor positions
- Cursor **不能简单指向某个 character**

解决方案：

- 添加 **dummy cell**
- Cursor 指向 **insertion point 前一个 cell**

```cpp
Cell* start;// dummy cell
Cell* cursor;// cell BEFORE cursor

```

---

### 5.2 Cursor 表示的本质

> Cursor = “the cell before insertion point”
> 

📌 好处：

- Insert / Delete = **local pointer manipulation**
- 不需要整体移动数据

---

### 5.3 核心操作分析

### Insert

```cpp
Cell* cp =new Cell;
cp->ch = ch;
cp->link = cursor->link;
cursor->link = cp;
cursor = cp;

```

→ **O(1)**

### Delete

```cpp
Cell* cp = cursor->link;
cursor->link = cp->link;
delete cp;

```

→ **O(1)**

### MoveCursorBackward（重要！）

```cpp
Cell* cp = start;
while (cp->link != cursor)
    cp = cp->link;
cursor = cp;

```

→ **O(N)**（需要找 predecessor）

---

### 5.4 Complexity 总结

| Operation | Time |
| --- | --- |
| moveCursorForward | O(1) |
| moveCursorBackward | **O(N)** |
| moveCursorToStart | O(1) |
| moveCursorToEnd | **O(N)** |
| insertCharacter | O(1) |
| deleteCharacter | O(1) |

📌 **核心 trade-off**

- Insert/Delete 快
- Cursor backward 慢

---

## 6. Two-Stack Implementation（双栈模型，重点）

### 6.1 核心思想

> Characters before cursor in stack before
> 
> 
> Characters **after cursor** in stack `after`
> 

并且：

- **靠近 cursor 的字符在 stack top**

---

### 6.2 Data Representation

```cpp
CharStack before;
CharStack after;

```

📌 Cursor 本身不需要显式变量

→ **Cursor = boundary between two stacks**

---

### 6.3 核心操作机制（非常重要）

### moveCursorForward

```cpp
before.push(after.pop());

```

### moveCursorBackward

```cpp
after.push(before.pop());

```

### insertCharacter

```cpp
before.push(ch);

```

### deleteCharacter

```cpp
after.pop();

```

👉 所有这些操作都是 **stack push/pop**

---

### 6.4 Complexity（表格必须会）

| Operation | Time |
| --- | --- |
| moveCursorForward | O(1) |
| moveCursorBackward | O(1) |
| moveCursorToStart | O(N) |
| moveCursorToEnd | O(N) |
| insertCharacter | O(1) |
| deleteCharacter | O(1) |

📌 **理论最优结构（就操作而言）**

---

---

# 7. Amortized Analysis（必考 · 核心理论）

## 7.1 为什么需要 Amortized Analysis？

在数据结构分析中，我们常见三种分析方式：

### 1️⃣ Worst-case analysis（最坏情况）

- 关注 **单次操作的最大耗时**
- 结论往往**过于悲观**
- 例子：
    - Dynamic array 扩容时，`push` 是 **O(N)**

👉 但问题是：

**这种昂贵操作并不会频繁发生**

---

### 2️⃣ Average-case analysis（平均情况）

- 依赖 **概率分布**
- 需要假设“输入是随机的”
- 实际工程与考试中：
    - 难以定义“average”
    - Random ≠ Average

👉 本书 & 本章 **刻意回避这种分析方式**

---

### 3️⃣ Amortized analysis（摊还分析）✅

> Amortized analysis averages the cost of a sequence of operations,
not a single operation.
> 

关键特征（必背）：

- ❌ 不使用概率
- ✅ 保证在 **worst-case input** 下
- ✅ 平均到每次操作仍是小代价

📌 结论形式通常是：

> “Although a single operation may be expensive,
> 
> 
> the amortized cost per operation is O(1).”
> 

---

## 7.2 本章中的典型问题：CharStack::push()

### 7.2.1 表面矛盾

```cpp
voidCharStack::push(char ch) {
if (count == capacity)expandCapacity();
    array[count++] = ch;
}

```

- 普通 push：O(1)
- expandCapacity()：
    - allocate new array
    - copy all elements
    - delete old array
    - 👉 **O(N)**

❓问题：

> 那 push 到底是 O(1) 还是 O(N)？
> 

---

## 7.3 Doubling Strategy（扩容策略）

### 7.3.1 扩容规则

- 初始 capacity = C
- 每次满了 → `capacity *= 2`

扩容发生在：

```
C,2C,4C,8C,...

```

---

### 7.3.2 扩容发生得“很少”

- 第 1 次扩容：拷贝 C 个元素
- 第 2 次扩容：拷贝 2C
- 第 3 次扩容：拷贝 4C
- …

如果一共插入 N 个元素：

> 扩容次数 = O(log N)
> 

---

## 7.4 数学层面的 Amortized 推导（PPT 核心）

### 7.4.1 成本模型

定义：

- α = cost of insertion **without expansion**
- β = per-element cost during expansion
- N = total number of insertions

---

### 7.4.2 总成本（Total Cost）

扩容发生时的复制成本：

```
β(C+2C+4C+...<N)

```

这是一个 **geometric series**：

```
C+2C+4C+...<2N

```

👉 所以复制总成本 < 2βN

加上每次插入本身的成本：

```
Totalcost ≤ αN+2βN

```

---

### 7.4.3 Amortized Cost（最终结论）

```
Averagecostperinsertion
=(αN+2βN)/N
= α+2β
=O(1)

```

📌 **关键结论（必须会写）**：

> Even though some individual operations take O(N) time,
> 
> 
> the amortized cost of each insertion is O(1).
> 

---

## 7.5 回到本章：EditorBuffer（Two-Stack Model）

### 7.5.1 为什么 stack-based buffer 看起来“太理想”？

在复杂度表中：

| Operation | Stack-based |
| --- | --- |
| insertCharacter | O(1) |
| deleteCharacter | O(1) |

老师会追问：

> 这些 O(1) 是 worst-case 还是 amortized？
> 

---

### 7.5.2 正确答案（期末标准）

- `insertCharacter` → `before.push()`
- `deleteCharacter` → `after.pop()`
- push/pop 依赖 CharStack

📌 **严格说法**：

> insertCharacter is O(1) amortized, not worst-case.
> 

这是**完全符合 PPT 的严谨结论**。

---

## 7.6 Amortized Analysis vs Average-case（高频区分题）

| Amortized | Average-case |
| --- | --- |
| No probability | Requires probability |
| Worst-case guarantee | Depends on input distribution |
| Sequence-based | Single-operation based |
| 常用于 dynamic array / stack | 常用于 hashing |

📌 考试中如果问：

> “Why amortized analysis is preferred here?”
> 

标准答法：

> Because it guarantees average performance over a sequence of operations without relying on probabilistic assumptions.
> 

---

## 7.7 一句话终极总结（可直接背）

> Amortized analysis shows that although individual operations may be expensive,
> 
> 
> the average cost per operation over a sequence is constant in the worst case.
> 

## 8. 综合对比

| Model | Insert/Delete | Cursor Move | Space | Complexity Balance |
| --- | --- | --- | --- | --- |
| Array | Slow | Fast | Compact | 不适合编辑 |
| List | Fast | Mixed | Pointer overhead | 不平衡 |
| Two-stack | Fast | Mostly fast | Extra stack | **最优折中** |

---

## 9. 终极思考题

**Q: 能否让所有操作都是 O(1)？**

A:

- 可以，用 **doubly linked list**
- 但代价是：
    - 更多空间
    - 更多指针
    - 实现复杂度上升

👉 **Time–Space Tradeoff**

---

## 10. 一句话总总结

> Different data representations can lead to dramatically different performance characteristics, even when the abstract operations are identical.
>