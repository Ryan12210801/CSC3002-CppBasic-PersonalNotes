# 2.Functions&Libraries

# 第一部分 Function

## 1. Programming Paradigm（理解层，少考）

- **Programming paradigm**：programming 的“风格/思想”
- C++ 是 **multi-paradigm language**
    - Procedural（本章重点）
    - Object-Oriented（后面章节）
    - Functional（了解）

本章关注：**Procedural programming**

---

## 2. Procedural Programming & Functions（核心思想）

### Procedural Programming

- Based on **procedure / function calls**
- Program = sequence of statements + function calls
- 强调 **how to do**（Imperative）

### Function（必背定义）

> A function is a named block of code that performs a specific task.
> 

类比数学函数：

```
f(x) = x² + 1

```

C++ 实现：

```cpp
double f(double x) {
    return x * x + 1;
}

```

---

## 3. Parameters vs Arguments（必考概念题）

- **Parameter**：函数定义中的变量（占位符）
- **Argument**：函数调用时传入的实际值

```cpp
int add(int x) {   // x is parameter
    return x + 1;
}

add(5);            // 5 is argument

```

 常考问法

- parameter ≠ argument（但一一对应）

---

## 4. Advantages of Using Functions（理解 + 简答）

Functions help:

1. **Code reuse**（写一次，多次用）
2. **Abstraction**（隐藏细节）
3. **Decomposition**（大问题拆小问题）
4. **Maintainability**（易修改，不影响调用者）

关键词：**top-down design / stepwise refinement**

---

## 5. Function Prototype（必考）

### 什么是 Function Prototype？

- 函数的 **声明**
- 只有 header + `;`

```cpp
int gcd(int x, int y);   // prototype

```

### 为什么需要？

- C++ 遵循 **declare-before-use**
- Compiler 需要提前知道：
    - 参数类型
    - 返回类型

重要对比

- 若函数定义在 `main()` 前 → prototype 可省
- 实际工程 & 考试 → **默认要 prototype**

---

## 6. Defining Functions in C++（必会结构）

### 一般形式

```cpp
return_type function_name(parameter_list) {
    statements;
    return expression;
}

```

### 特殊类型

- **Predicate function**：returns `bool`

```cpp
bool isEven(int n) {
    return n % 2 == 0;
}

```

- **Procedure**：returns nothing → `void`

```cpp
void printHello() {
    cout << "Hello";
}

```

易考点

- `return type` ≠ function signature（见下）

---

## 7. Function Signature & Overloading（高频考点）

---

## 7.1 什么是 Function Signature？

**Signature = function name + (parameter types & number)**

也就是：编译器用来决定“你到底要调用哪一个函数版本”的信息。

✅ 包含：

- 参数个数（number of parameters）
- 参数类型（types）
- 以及顺序（order）

❌ 不包含：

- parameter names（形参名字不算）
- return type（返回类型不算）

**例子：参数名字不影响 signature**

```cpp
int add(int x);
int add(int y);   // ❌ 这不是新函数，signature 一样

```

## 7.2 为什么 return type 不属于 signature？（常考解释）

因为 **function call 只提供 arguments**，比如：

```cpp
add(3);

```

这里编译器只看到 `3`（int），它没法仅靠调用语句猜“你想要 int 返回还是 double 返回”。

如果 return type 也算 signature，会出现：

```cpp
int add(int);
double add(int);

```

调用 `add(3)` 就无法决定选哪个（除非你在调用处额外声明“我想要 double”——C++ 不这么设计）。

📌 考试一句话答法：

> Overload resolution is based on arguments; return type is not specified at call site, so it cannot distinguish overloads.
> 

## 7.3 Overloading 怎么匹配？（编译器挑选规则的直觉版）

编译器会选“最合适”的那个：

- **exact match**（完全匹配）最好
- **promotion**（比如 char→int）次之
- **conversion**（比如 int→double）更次
- 如果两个一样好 → **ambiguous**（二义性，编译错误）

### 例子：为什么会 ambiguous？

```cpp
int f(long);
int f(double);

f(1);  // 1 是 int：既能转 long，也能转 double
        // 两条路都“差不多”，编译器可能判 ambiguous

```

📌 考试常见：让你判断“调用哪个版本 / 是否二义性”。

---

## 8. Default Arguments（常考规则题）

### 基本形式

```cpp
void setMargin(int margin = 72);

```

### 规则（必背）

1. Default arguments **only in prototype**
2. Must appear **at the end**
3. Optional parameter 后不能有 required parameter

❌ 错误示例：

```cpp
void f(int x = 0, int y);   // ❌

```

### 用 overloading 替代 default arguments

```cpp
void setInitialLocation(double x, double y);
void setInitialLocation() {
    setInitialLocation(0, 0);
}

```

📌 考点：**防止只传一部分参数**

---

## 9. Functions & Algorithms（理解题）

- Function 是表达 **algorithm** 的基本单位
- 不同 algorithm → **不同 efficiency**

### GCD Example（经典对比）

### Brute-force（低效）

```cpp
while (x % guess != 0 || y % guess != 0)

```

- 最坏情况：O(min(x, y))

### Euclid’s Algorithm（高效，常考）

```cpp
while (r != 0) {
    x = y;
    y = r;
    r = x % y;
}

```

📌 核心思想

> gcd(x, y) = gcd(y, x % y)
> 

---

## 10. Mechanics of Function Call（重点理解题）

调用函数时发生的事（**非常重要**）：

1. Evaluate arguments（在 caller 中）
2. Create a new **stack frame**
3. Copy argument values → parameters
4. Execute function body
5. Encounter `return`
6. Destroy stack frame
7. Return to caller

📌 关键词

- stack frame
- copy
- local variables
- 

例子：

```cpp
int add(int a, int b) {
    int c = a + b;
    return c;
}

int x = 3, y = 4;
int z = add(x, y);

```

### Step 1 — Evaluate arguments in caller

在 caller 里算 `x` 和 `y` 的值：得到 `3`、`4`

（注意：是值，不是地址）

### Step 2 — Create a new stack frame（栈帧）

为 `add` 函数分配一块新的函数调用空间：存放

- parameters：`a`, `b`
- locals：`c`
- return address（返回到 caller 的哪里）

### Step 3 — Copy argument values → parameters

把 3 copy 给 `a`，把 4 copy 给 `b`

👉 现在 `a` 和 `x` **不是同一个变量**，只是值相同

### Step 4 — Execute function body

算 `c = a + b`

### Step 5 — Encounter return

return `c`（得到一个返回值）

### Step 6 — Destroy stack frame

`add` 的所有 local / parameters 都被销毁（出栈）

### Step 7 — Resume caller

把返回值赋给 `z`

---

## 11. Call by Value（必考陷阱）

```cpp
void swap(int x, int y) {
    int tmp = x;
    x = y;
    y = tmp;
}

```

```cpp
int a = 1, b = 2;
swap(a, b);

```

📌 结果：

- `a == 1, b == 2`（没变）

原因：

- 参数是 **copies**
- 修改的是局部变量

---

## 12. Reference Variables（核心重点）

### 什么是 Reference？

> A reference is an alias (another name) of an existing variable.
> 

```cpp
int n = 5;
int &r = n;

```

- `r` 和 `n` 指向同一个 object
- Reference 必须初始化
- 不能 rebind

---

### Call by Reference（超级高频）

```cpp
void swap(int &x, int &y) {
    int tmp = x;
    x = y;
    y = tmp;
}

```

```cpp
swap(a, b);   // a, b will be swapped

```

 作用（必背）

1. **Pass information both ways**
2. **Efficiency**（避免 copy 大对象）

---

## 13. Reference vs Pointer（了解）

- Reference：
    - Safer
    - No null
    - No rebind
- Pointer（C inherited）：
    - 更灵活
    - 更危险

 本章只记一句话：

> Reference = safer alias of an existing object
> 

---

## 14. “Return more than one value?”（经典考点）

 C++ function 只能 return 一个值

 用 **reference parameters**

```cpp
void solve(double &r1, double &r2);

```

---

# 第二部分（library）

## 1. What is a Library（必考定义）

### Library

> A library is a collection of implementations of behavior with a well-defined interface.
> 

解释：

- library = 已写好的 **functions / classes**
- 被多个程序 **reuse**
- 程序员实际写的代码只是 **tiny fraction**

 易考关键词

- reuse
- interface
- implementation

---

## 2. Client / Interface / Implementation（三件套，必考）

### Client

- Code that **uses** the library
- Depends only on the **interface**
- 不应依赖 implementation

👉 client cares about **what**, not **how**

---

### Interface

- Defines **what the library provides**
- Function names, parameter types, return types
- Acts as:
    - **barrier**（隔离实现）
    - **communication channel**（沟通手段）

📌 在 C++ 中：

- interface 通常在 `.h` 文件

---

### Implementation

- Contains **actual code**
- Defines **how the functionality is achieved**
- Can change without affecting clients (as long as interface unchanged)

📌 在 C++ 中：

- implementation 通常在 `.cpp` 文件

---

### ⭐ 一句话考试版

> The client interacts with the library only through its interface, while the implementation remains hidden.
> 

---

## 3. From Client to Implementer（视角切换，概念题）

- **Client**：调用库的人
- **Implementer**：写库的人

课程逻辑

- 前期：你是 client
- 后期：你要学会当 implementer

---

## 4. Library Interface in C++（header file，必考）

## 4.1 Header file（`.h`）到底是什么？

**一句话定义（考试版）：**

> A header file defines the interface of a library, specifying what is available to clients.
> 

换句话说：

`.h` 文件 = **客户端唯一需要看到的东西**

---

## 4.2 `.h` 文件里“应该有什么 / 不应该有什么”（超高频）

### ✅ `.h` 里应该有（interface）

- Function prototypes
- Exported types (`enum`, `class`)
- Exported constants
- Documentation comments（Usage）

### ❌ `.h` 里不应该有（implementation）

- 函数实现代码
- 算法细节
- private helper functions（实现细节）

📌 考试判断题常见：

> 把函数实现写进 .h 是否合理？
> 
> 
> ❌ 不合理（破坏 abstraction & 增加耦合）
> 

---

## 4.3 为什么要用 Header File？（Why）

### 原因 1：Declare-before-use

- C++ 编译器需要在使用前知道函数签名
- `.h` 提供声明

### 原因 2：Separation of concerns

- Client：只关心 **what**
- Implementer：负责 **how**

### 原因 3：Stability

- Implementation 可以改
- Client 代码不用动（只要 interface 不变）

📌 一句话答题模板：

> Header files allow clients to compile against an interface without knowing implementation details.
> 

---

## 4.4 Include Guard（必考细节）

### 问题背景

如果一个 `.h` 被多次 `#include` → **redefinition error**

### 解决方案：Include Guard

```cpp
#ifndef _error_h
#define _error_h

void error(std::string msg);

#endif

```

### 工作原理（理解题）

- 第一次 include → 宏未定义 → 编译内容
- 后续 include → 宏已定义 → 跳过

📌 易考点

- Include guard ≠ C++ 语法，是 **preprocessor feature**

---

## 4.5 `.h` vs `.cpp` 的严格分工（必背）

| 文件 | 作用 |
| --- | --- |
| `.h` | Interface（what） |
| `.cpp` | Implementation（how） |

📌 老师很爱的一句话：

> Clients include header files, not implementation files.
> 

---

## 4.6 Exporting Types（接口不只导出函数）

### 为什么要导出 type？

- Type 本身就是 abstraction 的一部分
- Client 需要用这个 type 才能调用函数

### 示例：导出 enum

```cpp
enum Direction { NORTH, EAST, SOUTH, WEST };

```

### 配套导出操作

```cpp
Direction leftFrom(Direction dir);

```

📌 考点理解

> A good interface exports both data types and the operations on them.
> 

---

## 4.7 Operator Overloading 作为 Interface（理解题）

```cpp
std::ostream& operator<<(std::ostream& os, Direction dir);

```

- Operator 本质上也是函数
- 让 client 用更自然的语法

```cpp
cout << dir;

```

📌 设计思想

- Improve **usability** without exposing implementation

---

## 5. Implementing an Interface（`.cpp`）

- `.cpp` includes:
    - standard libraries
    - its own header

```cpp
#include "error.h"

```

📌 原则

- `.h`：what
- `.cpp`：how

---

## 6. Exporting Types（enum + operator overloading）

### Exporting an enum

```cpp
enum Direction { NORTH, EAST, SOUTH, WEST };

```

- enum 本身是 interface 的一部分
- client 可以直接使用该 type

---

### Exporting related operations

- Functions operating on the type
- Operator overloading (e.g. `<<`, `++`)

📌 易考理解点

- 类型 + 操作一起构成 **abstraction**

---

## 7. Interface vs Implementation（设计思想，必考）

### Why separate them?

| Advantage | Explanation |
| --- | --- |
| Simplicity | Client sees fewer details |
| Flexibility | Implementation can change |
| Security | Prevents misuse |
| Maintainability | Less breakage |

📌 关键句

> As long as the interface remains unchanged, the implementation may evolve freely.
> 

---

## 8. Principles of Interface Design（重点简答题）

### 1️⃣ Unified

- One clear abstraction
- No unrelated functions

### 2️⃣ Simple

- Hide complexity
- Easy for client to use

### 3️⃣ Sufficient

- Provide all necessary operations
- Missing key function → client abandons library

### 4️⃣ General

- Useful for many clients
- Not over-specialized

### 5️⃣ Stable（**高频**）

- Interface should not change
- Changing interface forces client code changes

📌 考试常问：

> Which principle is violated if changing a function breaks many client programs?
> 
> 
> 👉 **Stability**
> 

---

## 9. C++ Standard Library（了解 + 举例）

### Features

- Declared in namespace **std**
- Containers: `vector`, `map`, `set`
- Algorithms: `<algorithm>`
- I/O: `<iostream>`, `<fstream>`
- Math & C libs: `<cmath>`, `<cstdlib>`

📌 考试一般：

- 知道 **存在**
- 知道 **namespace std**

---

## 10. Library vs Class Hierarchy（理解题）

- Libraries: organized for **user convenience**
- Classes: organized for **data integrity & efficiency**

📌 结论句（好用）

> Libraries are designed for usability; class hierarchies are designed for implementation efficiency.
> 

---

## 11. Designing a Random Number Library（理解 + 应用）

### Why not true randomness?

- Computer behavior is deterministic
- So we simulate randomness → **pseudorandom**

---

### rand() problems

- Returns `[0, RAND_MAX]`
- Too low-level for clients

---

### random.h Interface（考点）

```cpp
int randomInteger(int low, int high);
double randomReal(double low, double high);
bool randomChance(double p);

```

📌 易考理解

- interface hides complexity
- client 不接触 `rand()` / `srand()`

---

### Seed & determinism

- Same seed → same sequence
- Good for debugging

---

## 12. Static vs Dynamic Libraries（低频概念）

- **Static library**: linked at compile time
- **Dynamic library**: linked at runtime