# 14. Linear Structures

## 0. 本章在整门课中的位置（先立框架）

本章解决三个核心问题：

1. **如何让同一套数据结构代码支持不同类型**
    
    → Templates
    
2. **如何实现线性结构（Linear Structures）**
    
    → Stack / Queue / Vector
    
3. **C++ 为此带来的关键语言机制与陷阱**
    
    → template 实现位置、deep copy、return by reference
    

期末考察重点：

**不是 STL 的用法，而是“你是否理解这些结构是如何被实现出来的”**

---

## 1. 从 CharStack 到泛型（Templates 的动机）

### 1.1 CharStack 的局限性

```cpp
classCharStack {
private:
char* array;
int capacity;
int count;
};

```

问题本质：

- 类型被写死（char）
- 若要 IntStack / DoubleStack / PointStack
    
    → 逻辑完全一致，只是类型不同
    
- 代码重复，维护成本极高

---

## 2. Overloading vs Templates（一定要区分清楚）

### 2.1 Function Overloading（函数重载）

```cpp
intoperate(int a,int b);
doubleoperate(double a,double b);

```

特点：

- 同名，不同参数类型（signature）
- 编译器根据实参类型选择函数版本

局限：

- 每增加一种类型 → 写一个函数
- 不适合系统性复用

---

## 3. Templates（本章第一核心）

### 3.1 Template 的本质

Template 不是函数、也不是类，而是：

> 一份“代码生成模式（pattern）”
> 

```cpp
template <typename ValueType>
ValueType max(ValueType a, ValueType b);

```

编译阶段行为：

- 编译器在“使用时”生成具体版本
- 例如 `max<int>`、`max<double>`

---

### 3.2 Template 的隐含约束（易考）

```cpp
template <typename T>
T max(T a, T b) {
return (a > b) ? a : b;
}

```

重要结论：

- 模板对类型 **有隐式要求**
- 这里要求 `T` 必须支持 `operator>`

若不满足 → **编译错误（compile-time error）**

---

## 4. Template Stack 的完整设计（重中之重）

---

### 4.1 Stack 的接口结构（必须熟）

```cpp
template <typename ValueType>
classStack {
public:
Stack();
    ~Stack();

intsize()const;
boolisEmpty()const;
voidclear();

voidpush(ValueType value);
ValueType pop();
ValueType peek()const;

Stack(const Stack<ValueType>& src);
    Stack<ValueType>&operator=(const Stack<ValueType>& src);

private:
    ValueType* array;
int capacity;
int count;

voidexpandCapacity();
voiddeepCopy(const Stack<ValueType>& src);
};

```

这是 CharStack 的**泛型版本**，结构完全一致。

---

### 4.2 为什么 template 的实现必须写在 `.h` 文件里（必考）

结论先行：

> Template 类：接口 + 实现必须同时对编译器可见
> 

原因逻辑（一定要能复述）：

1. Template 在“使用时”才实例化
2. 编译器看到 `Stack<int>` 时
3. 必须立刻知道所有函数的实现
4. `.cpp` 文件不会被自动包含
5. 所以实现只能写在 `.h`

这是 **语言层面的限制，不是风格问题**

---

## 5. Deep Copy（期末高频理解点）

### 5.1 为什么必须自己写拷贝逻辑

Stack 内部包含：

```cpp
ValueType* array;// 指向 heap

```

如果使用默认拷贝：

- 只拷贝指针
- 两个 Stack 指向同一块内存
- 析构时 double free

---

### 5.2 deepCopy 的核心逻辑

```cpp
template <typename ValueType>
void Stack<ValueType>::deepCopy(const Stack<ValueType>& src) {
    capacity = src.count + INITIAL_CAPACITY;
    array =new ValueType[capacity];

for (int i =0; i < src.count; i++) {
        array[i] = src.array[i];
    }
    count = src.count;
}

```

理解要点：

- 重新分配 heap
- 逐元素复制
- 新旧对象完全独立

---

## 6. Queue（队列）的两种实现方式

---

## 6.1 Queue 的抽象语义

Queue = FIFO（First In First Out）

- enqueue：从尾部加入
- dequeue：从头部移除

---

## 7. Linked-List Queue（指针版）

### 7.1 数据结构

```cpp
structCell {
    ValueType data;
    Cell* link;
};

Cell* head;// dequeue
Cell* tail;// enqueue
int count;

```

---

### 7.2 核心操作逻辑

enqueue（尾插）：

- 新节点接在 tail 后
- 更新 tail

dequeue（头删）：

- 删除 head
- head 指向下一个节点

---

### 7.3 为什么 List-based Stack 更简单？

- Stack：push / pop 都在同一端
- Queue：一端进，一端出
    
    → 需要同时维护 head 和 tail
    

---

## 8. Array-Based Queue 与 Ring Buffer（考试重点）

---

### 8.1 朴素数组实现的问题

- enqueue：O(1)
- dequeue：O(N)（整体移动）

这是不可接受的。

---

### 8.2 Ring Buffer（环形缓冲区）

核心思想：

- **不移动数据**
- 只移动 index（head / tail）

```cpp
tail = (tail +1) % capacity;
head = (head +1) % capacity;

```

---

### 8.3 为什么必须永远空一个位置（高频理解题）

规则：

```cpp
head == tail  → empty

```

如果允许数组被填满：

- empty 和 full 无法区分

解决方案：

- 最多存 `capacity - 1` 个元素

---

### 8.4 Queue 的 size 计算公式

```cpp
size = (tail + capacity - head) % capacity;

```

因此：

- 不再需要 `count` 成员变量

---

## 9. Vector（动态数组 + 下标访问）

---

### 9.1 Vector 的核心特性

- 动态扩容
- 随机访问
- 支持 `vec[i]` 形式

---

### 9.2 operator[] 必须返回引用（必考）

```cpp
ValueType&operator[](int index);

```

原因：

```cpp
vec[i] =17;

```

- 左侧必须是 lvalue
- 返回引用才能修改原数组元素

---

### 9.3 Return by Reference 的绝对禁忌

绝对不能返回：

- 函数内部的局部变量引用

```cpp
int&f() {
int x;
return x;// 错误
}

```

合法返回引用的典型场景：

- 成员变量
- heap 上对象
- 传入的引用参数
- operator[]、operator=、operator<<

---

## 10. 期末速记总结（高度压缩）

- Template 是代码生成模式
- Template 实现必须在 `.h`
- Stack / Queue / Vector 都基于 dynamic array 或 list
- Queue 的 ring buffer 用 modular arithmetic
- Ring buffer 必须保留一个空位
- `operator[]` 必须 return by reference
- 返回引用不能指向栈帧局部变量