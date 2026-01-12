# 15.Map

## 0. 本章在课程中的定位

**Map 是“非线性结构”的起点**，但本质仍是：

> 如何把 key 映射到 value，并尽可能快地完成查找（lookup）
> 

本章围绕一个核心问题展开：

> 如何实现高效的 get(key) 和 put(key, value)
> 

---

## 1. Map 的抽象概念（先理解“是什么”）

### 1.1 什么是 Map

Map（又称 dictionary）维护一组：

```
(key → value)

```

- **key**：唯一标识（identifier）
- **value**：与 key 关联的数据

示例：

```cpp
Map<string, string> dictionary;
Map<string,double> symbolTable;

```

---

### 1.2 Map 的基本操作（必须熟）

```cpp
map.size()
map.isEmpty()

map.put(key, value)// 或 map[key] = value
map.get(key)// 或 map[key]

map.containsKey(key)
map.remove(key)
map.clear()

```

期末重点：

**理解 get / put 的实现代价（time complexity）**

---

## 2. 为何先引入 StringMap（教学上的简化）

为了聚焦**实现策略**而非模板细节，PPT 中：

- 固定 key 和 value 类型为 `string`
- 得到 `StringMap`

目的：

> 先理解 Map 的实现思想
> 
> 
> 再推广到 `Map<KeyType, ValueType>`
> 

这一步逻辑与你之前学的 **CharStack → Stack<ValueType>** 完全一致。

---

## 3. Vector-Based StringMap（第一种实现）

---

### 3.1 基本思想：Linear Search

内部结构：

```cpp
Vector<KeyValuePair> bindings;

```

其中：

```cpp
structKeyValuePair {
    string key;
    string value;
};

```

---

### 3.2 核心方法逻辑

### get(key)

- 在 vector 中顺序查找 key
- 找到 → 返回 value
- 找不到 → 返回空字符串

```cpp
O(N)

```

### put(key, value)

- 先查找 key
- 若存在 → 覆盖 value
- 若不存在 → 在 vector 末尾新增一对

```cpp
O(N)

```

---

### 3.3 复杂度总结

| 操作 | 时间复杂度 |
| --- | --- |
| get | O(N) |
| put | O(N) |

结论：

- 实现简单
- 性能差
- 不适合大量数据

---

## 4. 改进方案一：Binary Search（仍基于 Vector）

---

### 4.1 核心思路

- 保持 vector **按 key 排序**
- 使用 binary search 查找 key

---

### 4.2 性能分析（必考）

- `get`：
    
    ```cpp
    O(log N)
    
    ```
    
- `put`：
    - 插入新元素需要移动元素
    
    ```cpp
    O(N)
    
    ```
    

结论：

> 排序提高了查找效率，但插入仍然昂贵
> 

---

## 5. 特殊情形：Lookup Table（理解即可）

---

### 5.1 场景限制

示例：

**美国州名查询**

- key：两个大写字母（A–Z）
- 总共 26 × 26 = 676 种可能

---

### 5.2 实现方式

```cpp
Grid<string> table(26,26);

```

通过：

```cpp
row = key[0] -'A'
col = key[1] -'A'

```

---

### 5.3 为什么是 O(1)

- 直接计算索引
- 不依赖 key 数量
- 无搜索过程

代价：

- **空间浪费极大**
- 只适用于 key 空间极小、已知的情况

---

## 6. Hashing（本章最核心、最难理解的部分）

---

## 6.1 Hashing 的核心思想

> 用 key 本身，计算出“应该去哪里找它”
> 

类比字典：

- thumb index（A、B、C…）
- 先缩小范围，再精确查找

---

## 6.2 Hash Function 与 Hash Code

### 定义

- **hash function**：
    
    `key → non-negative integer`
    
- 输出称为 **hash code**

要求（重点）：

1. 计算要快（O(1)）
2. 尽量均匀分布 key（减少 collision）

---

### 两个极端例子（理解用）

- 所有 key → 0
    
    → 退化成普通 Map
    
- 完美 hash（每个 key 唯一映射）
    
    → 理论上极难构造
    

---

## 7. Bucket Hashing（最重要实现策略）

---

### 7.1 基本结构

```cpp
Cell** buckets;// array of bucket heads
int nBuckets;

```

每个 bucket 是一个 **linked list**（separate chaining）。

---

### 7.2 Bucket Index 的计算

```cpp
index =hashCode(key) % nBuckets;

```

意义：

- 把巨大的 hash code 压缩到 bucket 范围内
- 决定 key 属于哪个 bucket

---

### 7.3 Collision（冲突）如何处理

**Collision 是允许的**

- 不同 key → 同一个 bucket
- 在 bucket 的 linked list 中顺序查找

重要结论（PPT 强调）：

> Collision 不影响 correctness，只影响 performance
> 

---

## 8. HashMap 中 get / put 的逻辑

---

### get(key)

1. 计算 bucket index
2. 在该 bucket 的链表中查找 key
3. 找到 → 返回 value
4. 找不到 → 返回空字符串

平均复杂度：

```cpp
O(1)

```

最坏情况（所有 key 在同一 bucket）：

```cpp
O(N)

```

---

### put(key, value)

1. 计算 bucket
2. 查找 key
3. 若存在 → 更新 value
4. 若不存在 → 在链表头部插入新 cell

---

## 9. Load Factor 与 Rehashing（高频理解点）

---

### 9.1 Load Factor

```
load factor = (#keys) / (#buckets)

```

意义：

- 衡量 bucket 的“拥挤程度”

---

### 9.2 为什么需要 Rehashing

当 load factor 过大：

- collision 增多
- 链表变长
- get / put 退化

解决方案：

- **增大 nBuckets**
- 把所有 key 重新分配（rehash）

---

### 9.3 复杂度结论（一定要会说）

- HashMap 的时间复杂度：
    - **平均：O(1)**
    - **最坏：O(N)**

---

## 10. 从 StringMap 到通用 HashMap

要推广为：

```cpp
HashMap<KeyType, ValueType>

```

对 KeyType 的要求（非常重要）：

1. 可拷贝（assignable）
2. 支持 `==`
3. 必须提供 `hashCode(KeyType)` 函数

这是为什么 **STL unordered_map** 需要：

- `hash<Key>`
- `==` 运算符

---

## 11. STL 对应关系（认知层面）

- `Map`（有序） → `std::map`（通常基于 tree）
- `HashMap`（无序） → `std::unordered_map`（hash table）

---

## 12. 期末速记总结（高度压缩版）

- Map 维护 key → value
- Vector-based Map：O(N)
- Binary-search Map：get O(log N), put O(N)
- Lookup table：O(1)，但空间浪费
- Hashing：用 key 决定查找位置
- Collision 不影响正确性，只影响性能
- Bucket + chaining 是常见实现
- Load factor 小 → 平均 O(1)
- HashMap 平均 O(1)，最坏 O(N)