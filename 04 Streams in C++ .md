# 4.Streams in C++

## 1. Core Idea（全章主线）

**Stream** 是一种 **abstraction**，用于表示

> an input source or output destination of characters of indefinite length.
> 

核心思想：

C++ 用 *stream* 把 **keyboard / console / file / string** 统一成同一套输入输出机制。

---

## 2. Standard Streams（标准流）

来自 `<iostream>`：

- `cin` : standard input stream（keyboard）
- `cout` : standard output stream（console）
- `cerr` : standard error stream

Stream ≠ data

Stream 是“通道”，不是数据本身。

---

## 3. Output Stream：`cout` 与 `<<`

### 3.1 Insertion Operator `<<`

```cpp
cout << "The total is " << total << endl;

```

语义：

- 左边：output stream
- 右边：任意类型表达式
- 返回值：stream 本身

因此可以 **chaining**：

```cpp
cout << a << b << c;

```

---

### 3.2 Output Manipulators（重点）

来自 `<iomanip>`，影响**之后**的输出格式。

常考的：

- `endl`：换行并刷新缓冲区
- `setw(n)`：设置字段宽度
- `left` / `right`：对齐方式
- `fixed`：定点表示
- `scientific`：科学计数法
- `setprecision(n)`：精度
- `setfill(ch)`：填充字符
- `boolalpha`：true / false 输出

### 精度规则（非常重要）

- **default format**
    
    `setprecision(n)` → significant digits
    
- **fixed / scientific**
    
    `setprecision(n)` → digits after decimal point
    

这是考试里最容易混淆的点之一。

---

## 4. C-style vs C++ style Output

```cpp
printf("This is a double: %.2f\n", d);  // C-style
cout << fixed << setprecision(2) << d << endl;  //C++ Style

```

区别理解即可：

- `printf`：C-style，format string
- `cout`：C++ style，type-safe，object-oriented

课程立场：

> encouraged to use cout, but should recognize printf
> 

---

## 5. Input Stream：`cin` 与 `>>`

### 5.1 Extraction Operator `>>`

```cpp
cin >> x;
```

特点：

- 自动跳过 whitespace（space / tab / newline）
- 以 token 为单位读取

---

### 5.2 Input Manipulators（易考）

[5.2 Input Manipulators（`skipws / noskipws / ws`）详细讲解](4%20Streams%20in%20C++/5%202%20Input%20Manipulators%EF%BC%88skipws%20noskipws%20ws%EF%BC%89%E8%AF%A6%E7%BB%86%E8%AE%B2%E8%A7%A3%202c92b212942d809090e0e514c6fe89b3.md)

- `skipws`：跳过空白（默认）
- `noskipws`：不跳过空白
- `ws`：吃掉前导空白

```cpp
cin >> noskipws >> ch；
```

是否能读到 `' '`（space）是常见考点。

---

## 6. Files（文件流，重点）

[6. Files（文件流）——系统讲解版复习讲义](4%20Streams%20in%20C++/6%20Files%EF%BC%88%E6%96%87%E4%BB%B6%E6%B5%81%EF%BC%89%E2%80%94%E2%80%94%E7%B3%BB%E7%BB%9F%E8%AE%B2%E8%A7%A3%E7%89%88%E5%A4%8D%E4%B9%A0%E8%AE%B2%E4%B9%89%202c92b212942d80c4b5d2f20a21b58a52.md)

### 6.1 File 的本质

- File：permanent storage（external）
- String / variable：temporary（RAM）

---

### 6.2 使用文本文件的标准流程（必背）

1. Declare file stream
2. Open the file
3. Read / write data
4. Close the file

```cpp
ifstream infile;
infile.open(filename.c_str());
...
infile.close();

```

为什么用 `c_str()`？

- `open()` 接受的是 C-style string
- 不是 `std::string`

---

## 7. Reading Files：Character by Character

### 方法一：`get()` 返回 `int`

```cpp
int ch;
while ((ch = infile.get()) != EOF) {
    ...
}

```

### 方法二：`get(ch)`（更常用）

```cpp
char ch;
while (infile.get(ch)) {
    ...
}

```

原理：

- stream 可转换为 `bool`
- 到达 EOF / fail → false

---

## 8. Reading Files：Line by Line（高频）

### `getline`（free function）

```cpp
string line;
while (getline(infile, line)) {
    ...
}

```

特点：

- 读一整行
- 丢弃换行符
- 失败时 stream 状态为 fail

典型应用：

统计最长一行的长度。

---

## 9. `getline` 的两个版本（理解题）

### C++ string 版本（推荐）

```cpp
istream& getline(istream& is, string& str);

```

### C-style 版本（不推荐）

```cpp
istream& getline(char* s, streamsize n);

```

一个是 **free function**，一个是 **member function**。

---

## 10. Strings vs Files（对比）

| Strings | Files |
| --- | --- |
| RAM | External storage |
| Temporary | Permanent |
| Random access | Usually sequential |
| Private to program | Shared |

---

## 11. String Streams（<sstream>）

### 定义

String stream 是 **绑定到 string 的 stream**，

让 string 像 file 一样使用 `<<` / `>>`。

可以和file类比，不过这个流的输入/输出指向stream

---

### 常用类型

- `istringstream`
- `ostringstream`
- `stringstream`

---

### 应用一：生成格式化字符串

```cpp
stringstream ss;
ss << "Value = " << x;
string s = ss.str();

```

---

### 应用二：类型转换 / 重排数据

```cpp
stringstream ss;
ss << x << ' ' << y;
ss >> y >> x;

```

---

## 12. 易错点汇总（考试重点）

- `cin >> name` 只读一个 token
- `getline(cin, name)` 读整行
- 忘记 `close()` file
- `setprecision` 在 default / fixed 下含义不同
- `open()` 需要 C-style string
- 文件访问通常是 sequential

---

## 13. 本章精简总结

- A stream is an abstraction representing a flow of characters.
- `<<` inserts data into an output stream; `>>` extracts data from an input stream.
- Files must be opened before use and closed after use.
- `getline` reads an entire line and discards the newline.
- String streams allow treating strings as input/output streams.
