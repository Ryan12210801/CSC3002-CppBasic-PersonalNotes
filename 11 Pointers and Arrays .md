# 11. Pointers and Arrays

## 1. The Structure of Memory（内存的本质）

### 1.1 Bit / Byte / Word（最底层）

- bit：binary digit（0 或 1）
- byte：8 bits（最小 addressable unit）
- word：CPU 一次能处理的数据大小
    - x86-64：64 bits = 8 bytes

核心结论（必背）：

**Memory is byte-addressable.**

每一个 byte 都有一个独立的 memory address。

---

### 1.2 Number Bases（表示 ≠ 数本身）

- Binary（base 2）
- Octal（base 8）
- Hexadecimal（base 16）

PPT 原句（考试爱考）：

**Numbers do not have bases; representations do.**

例子：

00101010₂ = 42₁₀ = 2A₁₆

数字是同一个，变化的只是表示方式。

---

### 1.3 Address Length and Memory Size

- N-bit address space ⇒ theoretical max 2ⁿ bytes
- 16-bit → 64 KB
- 32-bit → 4 GB
- 64-bit → 16 EB

重要理解：

- “32-bit machine / 64-bit machine” 通常指 address length
- pointer size ≈ address length

---

### 1.4 Memory Regions

- Static data： global variables / constants
- Stack：local variables / stack frames
- Heap：dynamic allocation（new / delete）

经典结构：stack 与 heap 相向增长。

---

## 2. Pointers（C++ 的灵魂）

### 2.1 什么是 Pointer

定义：

**Pointer = a variable whose value is a memory address**

```cpp
int x =42;
int* p = &x;

```

内存层面理解：

- x：存 value（42）
- p：存 address (x 的地址）

---

### 2.2 lvalue（极其重要）

定义（PPT）：

An expression that refers to a memory location capable of storing data.

性质：

- every lvalue has an address
- address never changes after declaration
- value may change

判断标准（考试）：

能否出现在 assignment 的左侧。

---

### 2.3 Declaring Pointer Variables

```cpp
double* px;
Point* pptr;

```

说明：

- 属于变量，而不是 type
- 以下写法完全等价：
    
    `type* v` / `type *v` / `type * v`
    

---

### 2.4 Pointer Operators

- `&`：address-of operator
- ：dereference operator

```cpp
double x =2.5;
double* px = &x;
double y = *px;

```

此时 `*px` 与 `x` 代表同一块内存。

---

### 2.5 Pointers Are Dangerous

pointer 在底层本质上是一个 integer-like value。

```cpp
int* pi1 = (int*)pd;

```

风险来源：

- 强制类型转换
- 错误解释内存内容
- Undefined Behavior

---

### 2.6 Pointer and Call by Reference

Reference 版本：

```cpp
voidswap(int& x,int& y);

```

Pointer 版本：

```cpp
voidswap(int* px,int* py);

```

本质：

reference 是语法层面的抽象，底层仍基于 address。

---

### 2.7 Pointers to Objects and `>`

```cpp
Point pt(3,4);
Point* pp = &pt;

pt.getX();
(*pp).getX();
pp->getX();

```

原因：

- `.` precedence 高于
- `pp->x` 等价于 `(*pp).x`

---

### 2.8 `this` Keyword

- `this` 是一个 pointer
- 指向当前 object

用于区分 parameter 与 member 同名情况：

```cpp
this->x = x;

```

---

## 3. Arrays

### 3.1 Nature of Arrays

array 的本质：

- contiguous memory block
- no bounds checking
- fixed length
- does not store its own size

相比 Vector：更接近硬件，但更危险。

---

### 3.2 Array Size

```cpp
int arr[10];

```

- allocated size：10
- effective size：由程序逻辑决定

获取元素个数：

```cpp
sizeof(arr) /sizeof(arr[0])

```

---

### 3.3 Arrays and Pointers

核心等价关系（必背）：

```
arr[i] == *(arr + i)

```

说明：

- array name 常被当作 pointer to first element
- 但 array 是 non-modifiable lvalue

```cpp
int* p = arr;// OK
arr = p; // illegal

```

---

### 3.4 Arrays Passed to Functions

```cpp
voidsort(int arr[],int n);

```

本质：

- only the address is passed
- call by pointer
- callee 和 caller 共享同一块内存

---

## 4. Pointer Arithmetic（高危重点）

### 4.1 Pointer Addition

```cpp
double arr[5];
double* dp = arr;

```

规则：

- `dp + 1` 跳 sizeof(double) bytes
- `dp + i` 指向 arr[i]

等价关系：

```
arr[i]
*(arr + i)
*(dp + i)
dp[i]

```

---

### 4.2 Illegal Operations

- pointer + pointer：不允许
- pointer - pointer：允许（distance）

---

### 4.3 `p++` 表达式（必考）

```cpp
int a = *p++;

```

等价于：

```cpp
a = *p;
p = p +1;

```

原因：

- unary operators 从右向左结合
- `++` precedence 高于

---

### 4.4 Relation to STL Iterators

- `p++` 常见于 STL iterator。

PPT 原意：

- 必须能读懂
- 不建议自己写（易越界，易出错）

---

## C Strings（指针的历史灾难）

- C string = pointer to char
- ends with `'\0'`

```cpp
char* msg ="hello";

```

典型函数：

### strlen

```cpp
intstrlen(char* s) {
int n =0;
while (*s !='\0') {
        n++;
        s++;
    }
return n;
}

```

逻辑本质：

- 从首地址开始
- 一个 char 一个 char 往后走
- 直到 `'\0'`

### strcpy

```cpp
voidstrcpy(char* dst,char* src) {
while (*dst++ = *src++);
}

```

**逐步拆解：**

1. `src` 取字符
2. 赋值给 `dst`
3. 判断是否为 `'\0'`
4. 两个指针都往后挪

## 为什么 C string 非常危险？

- 没有 length 信息
- 没有 bounds checking
- 极易 buffer overflow

这正是 **Morris Worm** 能成功的原因。

---

## 最终总结（可直接用于笔试）

**Pointers make memory addresses first-class values in C++.**

**Arrays are contiguous memory blocks accessed via pointer arithmetic.**

**This power enables efficiency and flexibility, but introduces safety risks.**