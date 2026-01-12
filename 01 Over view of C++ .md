# 1. Over view of C++

## 1. What is C++（背景：了解即可，少考）

- **C++** is a **compiled**, **statically typed**, **general-purpose** programming language
- Developed by **Bjarne Stroustrup** at **Bell Labs (since 1979)**
- Extension of **C** (Dennis Ritchie, 1972)
- Design goal:
    
    **Low-level efficiency + high-level abstraction**
    

📌 易考对比（只记结论）

- C/C++ → compiled
- Python → interpreted
- Java → compiled to **bytecode**, executed by **JVM (with JIT)**

---

## 2. Compiler vs Interpreter

### Compiler

- Translates **entire source code** into **machine code**
- Generates a **stand-alone executable**
- Source code not needed at runtime
- Faster execution

### Interpreter

- Executes code **line by line**
- Source code needed **every run**
- Slower

📌 关键词（看到就要反应）

- object code
- linker
- executable
- runtime performance

---

## 3. Structure of a C++ Program（必考框架）

```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "hello, world" << endl;
    return 0;
}

```

### 必须知道的点

- Every C++ program **must contain `main()`**
- Program starts from `main`
- `return 0;` → program ends normally

---

## 4. Preprocessor & `#include`（理解层面）

- Lines starting with `#` are **preprocessor directives**
- Executed **before compilation**
- No semicolon `;`

```cpp
#include <iostream>   // standard library
#include "myLib.h"    // user-defined library

```

📌 记住即可，不深考

---

## 5. Namespace (`std`)（高频）

- Standard library identifiers are in namespace **`std`**
- Two common ways:

```cpp
std::cout << "Hi" << std::endl;

```

```cpp
using namespace std;
cout << "Hi" << endl;

```

📌 考点

- Purpose: avoid **name collision**
- Built-in types (`int`, `double`) do **not** need namespace

---

## 6. Declare-before-use Rule（必考）

> Every entity must be declared before it is used
> 

### Function Prototype

```cpp
int raiseToPower(int n, int k);   // declaration

```

- Needed if function definition is **after `main()`**
- Tells compiler: function name, parameter types, return type

📌 常考问法

- 为什么这个程序能编译？→ 因为有 function prototype

---

## 7. Variables（重点 + 易考）

### What is a variable?

> A variable is a named address that stores a value of a specific type
> 

```cpp
int total = 42;

```

### Attributes of a variable

- **Name**
- **Type** (fixed)
- **Value** (changeable)
- **Address** (fixed)

---

### Variable Scope vs Lifetime（重点）

- **Scope**: where the variable is visible (代码层面)
- **Lifetime / Extent**: when the variable exists (运行时)

### Common types

- **Local variable**
    - Declared inside a function or block
    - Created when execution enters, destroyed when leaves
- **Global variable**
    - Declared outside all functions
    - Exists for entire program
    - ❌ not recommended (hard to control, side effects)

📌 必考判断

- Variables in `main()` are **local**, not global

---

## 8. Data Types（你提到“讲得不够细”的重点）

### Primitive Data Types（本章核心）

| Type | Meaning | Typical use |
| --- | --- | --- |
| `int` | Integer | counting, indexing |
| `double` | Floating-point (real numbers) | math, scientific values |
| `bool` | Boolean (`true` / `false`) | conditions |
| `char` | Single character | characters |

📌 核心区别（易考）

- `int` → no decimal part
- `double` → decimal + higher precision
- `bool` → only `true` or `false`
- `char` → stored as integer internally (ASCII)

---

## 9. Constants（必考）

### Literal constants

- Integer: `42`
- Floating-point: `3.14`, `5.6E-8`
- Boolean: `true`, `false`
- Character: `'a'`
- String: `"hello"`

---

### Named constants（重点）

```cpp
const double PI = 3.14159;

```

- Must be initialized
- Cannot be modified later
- Has **type** and **scope**

⚠️ 对比（常考）

- `const` ✅ safe, scoped
- `#define` ❌ no scope, text replacement

---

## 10. Expressions & Operators（高频）

### Expression

- Combination of **operands + operators**
- Can be constants, variables, function calls

```cpp
double sum = n1 + n2 + n3;

```

---

### Integer Division Trap（超级易考）

规则：

> int / int → int
> 

```cpp
14 / 5     // 2

```

正确写法：

```cpp
14.0 / 5
double(14) / 5

```

⚠️ 经典陷阱

```cpp
double(14 / 5)   // 2.0 ❌

```

---

## 11. Type Casting（你问的 static_cast 重点在这里）

### 什么是 type cast？

> Explicitly converting a value from one type to another
> 

---

### C-style cast（不推荐）

```cpp
(int)a

```

---

### C++-style cast（推荐）

```cpp
int(a)
static_cast<int>(a)

```

### `static_cast<>` 是什么？

- **C++ 提供的安全、明确的类型转换方式**
- 编译期检查
- 不允许乱转（比如指针乱转）

```cpp
double x = 5.7;
int y = static_cast<int>(x);  // y = 5

```

📌 为什么推荐？

- 更安全
- 语义清晰
- 考试里写它 = 加分写法

---

## 12. Assignment & L-value / R-value（理解题）

```cpp
total = total + value;

```

- Left side must be **L-value** (has address)
- Right side can be **R-value** (temporary result)

📌 数学上不成立，但在 C++ 是合法赋值

---

## 13. Boolean Expressions & Short-circuit（必考）

### Relational operators

`<  <=  >  >=  ==  !=`

### Logical operators

`&&  ||  !`

⚠️ 易错

```cpp
0 <= x <= 9     // ❌
0 <= x && x <= 9  // ✅

```

---

### Short-circuit evaluation

```cpp
n != 0 && x % n == 0

```

- Left false → right not evaluated
- Prevents runtime error (divide by zero)

---

## 14. Control Statements（会写结构）

### if / else

```cpp
if (condition) { }
else { }

```

### Ternary operator

```cpp
max = (x > y) ? x : y;

```

### switch（记住 break）

基本语法

```cpp
switch (expression) {
    case constant1:
        statements;
        break;

    case constant2:
        statements;
        break;

    default:
        statements;
}

```

---

### ① `expression` 的要求

- 必须是 **integral type**
    - `int`
    - `char`
    - `enum`
- ❌ 不能是 `double`、`string`

易考判断题

> switch (double x) → ❌ illegal
> 

---

### ② `case` 后面必须是 **compile-time constant**

合法：

```cpp
case 1:
case 10:
case 'a':

```

不合法：

```cpp
case x:      // x 是变量 ❌
case 3.14:   // double ❌

```

---

### ③ `break` 的作用（超级重点）

- `break`：**跳出整个 switch**
- 如果没有 `break` → **fall-through（贯穿执行）**
- Without `break` → fall-through
- `default` handles other cases

---

## 15. Loops（结构必考）

### while

```cpp
while (condition) { }

```

### for

```cpp
for (init; test; step) { }

```

等价关系（必会）：

```cpp
init;
while (test) {
    ...
    step;
}

```

📌 Repeat-N-Times idiom

```cpp
for (int i = 0; i < N; i++)

```

---

## 16. Debug & Testing（了解即可）

- `assert(condition)` from `<cassert>`
- Condition false → program terminates
- `assert` is a **macro**, not a function