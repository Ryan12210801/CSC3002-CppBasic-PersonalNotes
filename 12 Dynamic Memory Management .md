# 12. Dynamic Memory Management

## 12.1 Dynamic Allocation and the Heap

### 一、C++ 中内存从哪里来？

C++ 中变量的内存来自 **三个区域**：

1. **Static data**
    - global variables
    - static variables
    - 生命周期：整个程序
2. **Stack**
    - local variables
    - function parameters
    - 生命周期：函数调用期间
3. **Heap**
    - dynamic allocation
    - 生命周期：**由程序员决定**

核心对比一句话记忆：

> stack：自动申请，自动释放
> 
> 
> heap：手动申请，**必须手动释放**
> 

---

## 二、什么是 Dynamic Allocation？

Dynamic allocation 指的是：

> 在程序运行过程中，向 heap 申请内存
> 

在 C++ 中使用关键字：

```cpp
new
delete

```

---

### 1️⃣ 分配单个对象

```cpp
int* pi =new int;

```

含义分解：

- 在 heap 上申请一块能放 `int` 的内存
- 返回这块内存的 address
- 用指针 `pi` 保存这个 address

---

### 2️⃣ 分配数组（非常重要）

```cpp
int* arr =new int[10000];

```

- 在 heap 上连续分配 10000 个 `int`
- `arr` 指向第一个元素

---

### 3️⃣ 释放内存（必须配对）

```cpp
delete pi;
delete[] arr;

```

**考试级规则（必背）**：

> new    ↔ delete
> 
> 
> new[]  ↔ delete[]
> 

不配对 = Undefined Behavior

---

## 12.2 Freeing Memory（释放内存的细节）

### 一、delete 到底做了什么？

```cpp
delete ptr;

```

只做一件事：

> 释放 ptr 指向的 heap 内存
> 

它 **不会**：

- 删除 ptr 这个变量
- 自动把 ptr 设为 null

---

### 二、Dangling Pointer（悬空指针）

```cpp
delete ptr;

```

此时：

- ptr 还存在
- 但指向的内存已经无效

这种 ptr 叫做 **dangling pointer**

---

### 三、常见防御写法

```cpp
delete ptr;
ptr =nullptr;

```

注意：

- 只能解决 **这一根指针**
- 如果有其他指针指向同一地址，仍然危险

---

### 四、delete[] 怎么知道数组多大？

PPT 的解释要点：

- 编译器在分配数组时
- 会在数组前面偷偷存储元素个数
- delete[] 会用这个信息正确释放

你 **不用记实现细节**，只记规则即可。

---

## 12.3 Heap–Stack Diagrams（非常重要的理解模型）

### 一、为什么要画 heap–stack diagram？

因为：

> 理解 C++ = 理解 stack 和 heap 同时发生了什么
> 

---

### 二、基本规则

- 每次调用函数 → stack 上新建 stack frame
- 每次 new → heap 上新建一块内存
- 函数返回 → stack frame 自动消失
- heap 内存 **不会自动消失**

---

### 三、最重要的一句话

> stack memory 自动管理，heap memory 程序员负责
> 

---

## 12.4 Linked Lists（指针的典型应用）

### 一、为什么要 linked list？

array 的问题：

- 大小固定
- 插入 / 删除代价高

linked list 的核心思想：

> 每个节点存：
> 
> - data
> - pointer to next node

---

### 二、基本结构（PPT 原型）

```cpp
structTower {
    string name;
    Tower* link;
};

```

- `link == NULL` 表示链表结束

---

### 三、用指针“传递消息”

```cpp
void signal(Tower* start) {
if (start !=NULL) {
        cout << start->name << endl;
signal(start->link);
    }
}

```

这就是 **linked structure + recursion**

---

## 12.5 Defining a CharStack Class（重点）

这是 PPT 中 **最重要的综合例子**。

---

### 一、CharStack 的核心设计思想

CharStack 内部有：

```cpp
char* array;// dynamic array on heap
int capacity;// allocated size
int count;// used size

```

这就是一个 **“自己管理 heap 内存的类”**

---

### 二、Constructor & Destructor（必考思想）

```cpp
CharStack::CharStack() {
    array =new char[capacity];
}

CharStack::~CharStack() {
delete[] array;
}

```

核心原则（必背）：

> 谁在 constructor 里 new，谁就在 destructor 里 delete
> 

---

### 三、为什么 destructor 如此重要？

因为：

- stack 上的对象会自动调用 destructor
- destructor 是 **释放 heap 的最后机会**

---

## 12.6 Copying Objects（极其重要，必考）

[保姆极精讲](12%20Dynamic%20Memory%20Management/%E4%BF%9D%E5%A7%86%E6%9E%81%E7%B2%BE%E8%AE%B2%202cd2b212942d80b38d00d8447ed93806.md)

### 一、默认拷贝的问题：Shallow Copy

如果你不写：

- copy constructor
- assignment operator

C++ 会：

> 只拷贝指针的值（地址）
> 

后果：

- 两个对象指向同一块 heap
- delete 两次 → crash

---

### 二、正确做法：Deep Copy

Deep copy 的含义：

> 拷贝 heap 上的数据，而不是地址
> 

---

### 三、必须写的两个函数

```cpp
CharStack(const CharStack& src);
CharStack&operator=(const CharStack& src);

```

并遵循模式：

```cpp
if (this != &src) {
delete[] array;
deepCopy(src);
}

```

---

### 四、一句话总结这一节

> 只要类里有 raw pointer 管理 heap，就必须考虑 deep copy
> 

---

## 12.7 The Uses of const（考试高频）

### const 的三种用法（PPT 指定）

1. **const variable**

```cpp
constdouble PI =3.14;

```

1. **const reference parameter**

```cpp
voidf(const CharStack& s);

```

保证：不修改参数对象

1. **const method**

```cpp
intsize() const;

```

保证：不修改 object state

---

### const-correctness

> 一个设计良好的 C++ 类，应尽可能使用 const
> 
> 
> 这能让编译器替你抓 bug
> 

---

## 12.8 Unit Testing（了解即可）

- 使用 `<cassert>`
- `assert(condition)`
- condition 为 false → 程序终止

PPT 明确说明：

**理解思想即可，不是考试重点**

---

## Modern C++ Smart Pointers（明确说明）

PPT 原话核心意思：

> Things that you only need to know that you don’t know.
> 
> 
> I don’t need you to memorize it for the exam.
> 

所以你只需要知道：

- `unique_ptr`
- `shared_ptr`
- 它们用于自动管理 heap

**不用背，不用写，不考实现细节**

---

## 最终总结

> C++ allows programmers to allocate memory dynamically on the heap using new.
> 
> 
> Heap memory is not automatically reclaimed and must be explicitly freed using `delete`.
> 
> Classes that manage heap memory must define destructors and implement deep copy semantics to avoid memory leaks and dangling pointers.
>