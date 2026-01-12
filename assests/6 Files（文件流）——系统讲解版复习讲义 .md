# 6. Files（文件流）——系统讲解版复习讲义

---

## 6.1 为什么要有 File Streams？（先讲动机）

在前面你已经用过：

- `cin`：从 **keyboard**
- `cout`：到 **console**

但现实程序需要：

- 从文件读数据（配置、数据集、文本）
- 把结果写入文件（日志、输出）

**核心问题**：

> 文件不在 RAM 里，而在 external storage（磁盘）
> 

所以 C++ 的设计是：

> 仍然使用 stream 抽象，只是把 stream 绑定到 file
> 

---

## 6.2 File Stream 的本质（非常重要）

### 一句话定义（考试标准）

> A file stream is a stream object associated with a file,
> 
> 
> such that input/output operations on the stream are reflected in the file.
> 

关键点拆解：

- file stream 是 **object**
- file stream ≠ file
- file stream 是程序内部的“代理”
- 真正的数据在磁盘文件中

---

## 6.3 三种常见 File Stream 类

来自 `<fstream>`：

| 类名 | 用途 |
| --- | --- |
| `ifstream` | input file stream（读文件） |
| `ofstream` | output file stream（写文件） |
| `fstream` | input + output |

---

## 6.4 使用文件的标准四步流程（必背）

这是**考试和作业都默认你会的流程**：

### Step 1：声明 stream 对象

```cpp
ifstream infile;

```

此时：

- 只是创建了一个 stream 对象
- **还没有**和任何文件关联

---

### Step 2：打开文件（open）

```cpp
infile.open(filename.c_str());

```

关键点：

- `open()` 的参数是 **C-style string**
- 所以 `std::string` 要用 `.c_str()`

📌 为什么不是 `string`？

- 历史原因（C 兼容性）
- C++ 标准库最初设计时延续了 C API

---

### Step 3：读 / 写文件

```cpp
infile >> x;
getline(infile, line);
infile.get(ch);

```

⚠️ 文件数据**通常是 sequential（顺序访问）**

---

### Step 4：关闭文件（close）

```cpp
infile.close();

```

为什么必须 close？

- 释放系统资源
- 确保数据真正写回磁盘
- 允许其他程序访问该文件

---

## 6.5 为什么文件处理“看起来很复杂”？（理解题）

课件里问过这个问题，标准解释是：

1. 文件在 external storage
2. 多个程序可能同时访问同一文件
3. 必须通过 open 建立独占或共享访问
4. 用完必须 close，避免冲突和数据损坏

一句话总结：

> 文件是共享资源，必须显式管理生命周期
> 

---

## 6.6 判断文件是否成功打开（很实用）

```cpp
ifstream infile;
infile.open("data.txt");

if (!infile.fail()) {
    // open succeeded
}

```

或更常见：

```cpp
if (infile.is_open()) {
    ...
}

```

---

## 6.7 从文件中读数据：两种核心方式

[核心逻辑精讲](6%20Files%EF%BC%88%E6%96%87%E4%BB%B6%E6%B5%81%EF%BC%89%E2%80%94%E2%80%94%E7%B3%BB%E7%BB%9F%E8%AE%B2%E8%A7%A3%E7%89%88%E5%A4%8D%E4%B9%A0%E8%AE%B2%E4%B9%89/%E6%A0%B8%E5%BF%83%E9%80%BB%E8%BE%91%E7%B2%BE%E8%AE%B2%202c92b212942d809baf8de3a47d98f4fa.md)

### 方式一：Character by Character（重点）

### 版本 A：`get()` 返回 int

```cpp
int ch;
while ((ch = infile.get()) != EOF) {
    ...
}

```

解释：

- `get()` 返回 `int`
- 文件结束时返回 `EOF`
- 必须用 `int`，不能用 `char`

---

### 版本 B：`get(ch)`（更推荐）

```cpp
char ch;
while (infile.get(ch)) {
    ...
}

```

这里发生了什么？

- `get(ch)` 读一个字符
- 返回 stream 本身
- stream 在 `while` 条件中会被转成 `bool`
- 到 EOF / fail → `false`

📌 **这是 C++ stream 的经典写法**

---

### 方式二：Line by Line（最常用）

### 使用 `getline`

```cpp
string line;
while (getline(infile, line)) {
    ...
}

```

重要特性：

- 读一整行
- **不包含换行符**
- 读失败（EOF）→ 返回 false

---

## 6.8 一个完整、标准的读文件模板

```cpp
#include <fstream>
#include <string>
using namespace std;

int main() {
    ifstream infile;
    infile.open("data.txt");

    string line;
    while (getline(infile, line)) {
        // process line
    }

    infile.close();
    return 0;
}

```

---

## 6.9 一个“危险但很经典”的例子（理解）

```cpp
file.open(filename.c_str());
file.get(c);
c = toupper(c);
file.put(c);
file.close();

```

问题：

> 这个程序真的把文件第一个字符变成大写了吗？
> 

答案：

**不一定 / 很危险**

原因：

- 文件指针在读完第一个字符后已经向前移动
- 写操作的位置取决于 stream 的当前状态
- 没有明确控制 read/write pointer

📌 说明一点：

> 读文件 ≠ 随便修改文件内容
> 

---

## 6.10 Files vs Strings（考试必考对比）

| Strings | Files |
| --- | --- |
| live in RAM | live in external storage |
| temporary | permanent |
| random access | usually sequential |
| private | shared |