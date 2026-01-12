# 5.2 Input Manipulators（skipws / noskipws / ws）详细讲解

## 1. 背景：`operator>>` 默认行为是什么？

`cin >> x` 属于 **formatted input**。默认状态下，`cin` 有一个 flag 叫 **skipws**（skip whitespace），意味着：

> 在读取任何普通类型（int/double/char/string）之前，会先跳过所有前导 whitespace。
> 

whitespace 包含：`' '`（space）、`\n`（newline）、`\t`（tab）等。

### 重要结论（默认）

- `cin >> int`：会跳过空白（正常）
- `cin >> string`：会跳过空白，并且读到下一个空白就停（读一个 token）
- `cin >> char`：**也会跳过空白**（很多人不知道这个！）

也就是说默认情况下：

```cpp
char ch;
cin >> ch;  // 会跳过所有空白，读到第一个非空白字符

```

---

## 2. `skipws`：跳过空白（默认）

它本质上是把 stream 的状态设成 “skip whitespace”。

```cpp
cin >> skipws >> ch;

```

等价于你什么都不写（因为默认就是 skipws）。

行为：

- 如果输入开头是很多空格或回车，它会一直丢掉，直到遇到非空白字符才把它读进 ch。

---

## 3. `noskipws`：不跳过空白（考试高频）

`noskipws` 会关闭 skipws flag：

```cpp
cin >> noskipws >> ch;

```

行为变成：

> 读取下一个字符，不管它是不是空白。
> 
> 
> 空格、换行、tab 都会被当作“有效字符”读进来。
> 

所以：

- 如果下一位是 `' '` → `ch` 就是 `' '`
- 如果下一位是 `'\n'` → `ch` 就是换行

这是“能不能读到空格”的核心。

---

## 4. `ws`：吃掉前导空白（专门用来“清空空白”）

`ws` 是一个 manipulator，它会：

> consume（提取并丢弃）所有前导 whitespace，直到遇到非空白字符为止。
> 

常用场景：

- 前面用 `cin >> number;` 后，缓冲区可能残留 `'\n'`
- 你下一步想读“真正的字符/字符串”，就先 `ws` 把空白吃掉

例如：

```cpp
char ch;
cin >> ws >> noskipws >> ch;

```

解释：

- `ws`：先把所有空白清掉
- `noskipws`：接下来读的这个字符允许是空白（但由于刚清空过，通常读到的是非空白）

---

## 5. 三者组合的典型“考试题”套路

你课件里那段类似：

```cpp
char a, b, c;
cin >> noskipws >> a >> ws >> b >> skipws >> c;

```

我们逐个解释：

### (1) `noskipws >> a`

- a 读到“下一个字符”
- 可能是空格、换行、普通字母都行

### (2) `ws >> b`

- `ws` 会把前导空白吃掉
- 然后 `>> b`（此时是否 skipws 要看当前 flag，但通常 b 会读到非空白）

### (3) `skipws >> c`

- 显式打开 skipws
- c 一定会读到非空白字符

---

## 6. 用一个具体输入演示（你能一眼看懂）

假设你输入（用 `·` 表示空格）：

```
··A··B

```

### 情况 1：默认（skipws）

```cpp
char ch;
cin >> ch;

```

- 会跳过前导空格
- ch = 'A'

### 情况 2：noskipws

```cpp
char ch;
cin >> noskipws >> ch;

```

- 不跳过空格
- ch = ' '（第一个空格）

### 情况 3：ws + noskipws

```cpp
char ch;
cin >> ws >> noskipws >> ch;

```

- ws 吃掉所有前导空格
- 然后读一个字符（不跳过）
- ch = 'A'

---

## 7. 最常见的坑：为什么我读到的是 `'\n'`？

因为你前面可能做了：

```cpp
int x;
cin >> x;

```

你输入 `123⏎`（回车），`123` 被读走了，但 `'\n'` 还留在缓冲区。

如果你接着：

```cpp
char ch;
cin >> noskipws >> ch;

```

那么 ch 很可能直接读到 `'\n'`。

解决方法：

- 用 `ws`：

```cpp
cin >> ws >> ch;  // 跳过所有空白后再读

```

---

## 8. 考试用速记结论（直接背）

- `cin >> ch` 默认 **skipws**：读到第一个非空白字符
- `noskipws`：空格/换行也会被读进来
- `ws`：专门用来“清空前导空白”

判断题只要看：

- 读 `char` 前有没有 `noskipws`
- 有没有 `ws` 清空空白

### `ws` 本身 **就会读取输入**

而 `skipws` **不会读取任何东西**，它只是规定“之后怎么读”。

|  | ws | skipws |
| --- | --- | --- |
| 是否消耗字符 | 是 | 否 |
| 是否改变流状态 | 否 | 是 |
| 影响范围 | 只作用一次 | 之后所有 `>>` |
| 本质 | operation | state |
