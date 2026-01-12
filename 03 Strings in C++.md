# 3.Strings  in C++

## 0. 总览（Big Picture）

**核心主线只有一句话：**

> C++ 同时支持 C-style strings（底层、危险） 和 C++ string class（抽象、安全），理解两者差异 = 理解 C → C++ 的范式转变。
> 

---

## 1. C vs C++：语言设计背景

### Relationship Between C and C++

- **C++ contains C as a subset**
    - 优点：legacy code 可逐步迁移
    - 缺点：语言整体不完全一致（历史包袱）

### Standard Library 结构（考点）

- **C++ Standard Library**（`std` namespace）
    - Containers: `vector`, `map`, `set`
    - Algorithms: `algorithm`
    - Strings: `<string>`
    - IO: `<iostream>`, `<fstream>`
- **C Standard Library in C++**
    - `<cctype>`, `<cstring>`, `<cmath>`

📌 **考点提醒**：

> <cstring> 是 C 的 string 库在 C++ 中的版本，不是 <string>
> 

---

## 2. Characters（字符基础）

### Encoding

- 使用 **ASCII**
- `char` = 1 byte = 8 bits = 256 values
- Unicode 支持有限 → `wchar_t`（本章不展开）

### Single Character

```cpp
char ch = 'a';     // character literal
char ch = 97;      // ASCII value
char ch = ' ';     // space

```

### Escape Sequences

| Escape | Meaning |
| --- | --- |
| `\n` | newline |
| `\t` | tab |
| `\r` | carriage return |
| `\0` | null terminator |
| `\\` | backslash |
| `\"` | double quote |

📌 **系统差异**

- Unix: `\n`
- Windows: `\r\n`

---

## 3. `<cctype>` Library（工具函数）

用于 **character classification & conversion**

- `isdigit(c)`
- `isalpha(c)`
- `isspace(c)`
- `toupper(c)`
- `tolower(c)`

📌 通常配合 string 遍历使用

---

## 4. C-style Strings（重点 + 易错）

### 本质（一定要会解释）

> C string = char array + null terminator \0
> 

```cpp
char cstr[] = "hello";
// 实际存储：h e l l o \0

```

- `"a"` ≠ `'a'`
    - `"a"` → `{'a','\0'}` → length = 1
    - `'a'` → single char

### 常用 `<cstring>` 函数（必考）

| Function | Meaning |
| --- | --- |
| `strlen(s)` | length（不含 `\0`） |
| `strcpy(dst, src)` | copy |
| `strcat(dst, src)` | concatenate |

### 🚨 致命问题（理解题）

```cpp
char cstr[5];
strcpy(cstr, "hello"); // ❌ overflow

```

**为什么 compiler 不检查？**

- C 的设计哲学：**trust programmer**
- 数组大小在运行期不可知
- 为了效率而牺牲安全

📌 **结论**：

> C-style strings = powerful but dangerous
> 

---

## 5. Strings as Abstract Values（核心思想）

### 抽象视角（非常重要）

- 用户应 **treat string as a whole**
- 内部实现复杂 → **encapsulation**

### C++ 的解决方案

```cpp
#include <string>
std::string s = "hello";

```

- `string` 是一个 **class**
- 操作通过 **methods**
- 隐藏 memory 管理细节

---

## 6. `#include` 易混点（考试常考）

| 写法 | 含义 |
| --- | --- |
| `<cstring>` | ✅ C string library in C++ |
| `<string>` | ✅ C++ string class |
| `<string.h>` | ⚠️ legacy C |
| `"string.h"` | ❌ 容易冲突 |
| `<cstring.h>` | ❌ |

---

## 7. String Operators & Overloading（高频）

### Operator Overloading

- `+` → concatenation
- `==` → comparison
- `<<` → output

### 核心规则（必懂）

```cpp
string s = "hello" + ", " + "world"; // ❌

```

原因：

- `"hello"` 是 **C string literal**
- `+` 不能作用于两个 C strings

### 正确写法

```cpp
string s = string("hello") + ", " + "world";

```

📌 **隐式转换规则**

- C string → C++ string ✔
- C++ string → C string ❌（需 `.c_str()`）

---

## 8. String Methods（常用 API）

### Length

```cpp
str.length();

```

不要写：

```cpp
strlen(str); // ❌
strlen(str.c_str()); // ⚠️ only if necessary 括号中的函数可以把string转化为cstring

```

### Character Access（重点）

```cpp
str[i];        // no bounds checking
str.at(i);     // range-checked

```

📌 考点：

- `at()` safer
- `[]` faster

---

## 9. Iteration Patterns（必须熟）

```cpp
for (int i = 0; i < str.length(); i++) {
    // forward
}

for (int i = str.length() - 1; i >= 0; i--) {
    // backward
}

```

---

## 10. Modifying Strings（效率 vs 安全）

### 三种风格

### 1️⃣ Immutable style（最安全，慢）

```cpp
string result = "";
result += ...

```

### 2️⃣ Copy then modify（折中）

```cpp
string result = str;
result[i] = ...

```

### 3️⃣ In-place modify（最快，危险）

```cpp
void f(string& s) { s[i] = ...; }

```

📌 **设计哲学**

> C++ 允许 mutation
> 
> 
> Python / Java strings are immutable
> 

---

## 11. Algorithmic String Examples（必会）

### Reverse

```cpp
string reverse(string str) {
    string rev = "";
    for (int i = str.length()-1; i >= 0; i--)
        rev += str[i];
    return rev;
}

```

📌 可优化：预分配 / in-place swap

---

### Palindrome（经典）

```cpp
bool isPalindrome(string str) {
    int n = str.length();
    for (int i = 0; i < n/2; i++)
        if (str[i] != str[n-i-1]) return false;
    return true;
}

```

对比：

```cpp
return str == reverse(str); // readable, less efficient

```

---

## 12. String Applications（理解即可）

- Acronym generator
- Pig Latin translator

考察点：

- parsing
- iteration
- condition handling

---

## 13. Stanford `strlib.h`（工具库）

提供更高层 API（教学用）：

- `toUpperCase`
- `equalsIgnoreCase`
- `startsWith`
- `trim`
- `stringToInteger`

📌 **不是 C++ 标准库**

---