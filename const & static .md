# const & static

# 1. 先把两个词分清：const vs static 在管什么？

## const 管的是“能不能改”

`const` 是 **immutability / read-only** 约束：

> 这个名字（变量/对象/引用/方法）承诺“不修改”。
> 

它解决的是：**修改权限**（compile-time constraint）。

---

## static 管的是“它属于谁、活多久、在程序里有几份”

`static` 主要影响：

1. **storage duration（生命周期）**：是不是整个程序都活着
2. **linkage / scope（可见性/作用域）**：在哪些地方能用这个名字
3. **是否共享一份**：类的所有对象是否共用同一份成员

它解决的是：**生存周期 + 归属关系 + 共享性**。

一句话总括：

- `const`：改不改得了
- `static`：活多久、属于谁、有几份

---

# 2. const 在你 PPT 里强调的三种用法（12.7）

## 2.1 const variable（常量变量）

```cpp
constdouble PI =3.14159;

```

含义：PI 之后不能再赋值。

注意：这叫“const 对象/变量”，不等同于“编译期常量”。

有些 const 会占存储，有些能被编译器直接替换（优化）。

---

## 2.2 const reference parameter（只读引用参数）

```cpp
voiddeepCopy(const CharStack& src);

```

作用：

- 不拷贝（efficiency）
- 禁止修改 src（safety）
- 允许传临时对象（重要特性）

这是你写库/ADT 时最常用的参数写法。

---

## 2.3 const method（只读成员函数）

```cpp
intsize()const;

```

含义：这个方法承诺不改变对象状态（不修改成员变量，除非是 `mutable` 这种特殊情况）。

你可以把它理解为：

在 `foo() const` 里面，`this` 更像是 `const T*`，所以不能改成员。

这会带来一个非常重要的能力：

- `const CharStack cs;`
- `cs.size();` 只有 `const` 方法能被调用

这就是 PPT 说的 **const-correctness**：

“该 const 的地方都 const”，让编译器替你抓 bug。

---

# 3. static 在 C++ 里的常见含义（按考试常考拆）

## 3.1 static local variable（函数内部 static）

```cpp
voidf() {
static bool initialized =false;
    ...
}

```

特点：

- 只初始化一次
- 生命周期：整个程序
- 作用域：只在函数里可见

PPT 的 initRandomSeed 就是这个例子。

---

## 3.2 static data member（类的静态成员）

```cpp
classA {
public:
static int i;
};
int A::i =2;// 必须在类外定义（分配存储）

```

特点：

- 这份变量属于 **class**，不是某个对象
- 所有对象共享同一份 `A::i`
- 改一个对象看到的值，另一个也变

这就是 PPT 里展示 `&a.i == &b.i` 的原因：地址相同。

---

## 3.3 static const member（类静态常量）

```cpp
classA {
public:
static const int c =1;
};

```

这类在课程语境里你要抓住两个点：

1. 它是“类级别共享”的常量
2. 很多情况下可以作为编译期常量使用（例如数组长度、模板参数），实现上可能不需要独立存储（取决于是否 ODR-use）

PPT 里强调了“Now they will have the same address and the same value.”（针对静态成员共享的直觉）。

---

# 4. const 和 static 的关键区别（最精炼对照）

| 维度 | const | static |
| --- | --- | --- |
| 解决的问题 | 能不能改 | 活多久 / 属于谁 / 有几份 |
| 是否影响生命周期 | 不一定 | 会（通常变成 program lifetime 或 class-shared） |
| 是否意味着共享 | 不一定 | 常常意味着共享（static member） |
| 常见位置 | 变量/参数/方法/成员 | 局部变量/全局变量/类成员 |

最常见误解：

“const 就是 static” 或 “static 就是 const” ——都错。

它们是正交的，可以组合。

---

# 5. const + static 组合起来会怎样？（这最考）

## 5.1 `static const`：共享且不可改

例：类常量

```cpp
classA {
public:
staticconstint c =1;
};

```

语义：

- 所有 A 对象共享同一个常量
- 不允许修改

用途：

- 类级别常量（例如初始容量 `INITIAL_CAPACITY`）

---

## 5.2 `static` 但非 const：共享且可改

```cpp
staticint i;

```

语义：所有对象共享同一份变量，谁改都改全局那份。

---

## 5.3 `const` 但非 static：每个对象各自一份，且不可改

```cpp
classA {
public:
constint c1 =1;// (C++11 起) 每个对象一个 c1
};

```

语义：

- 每个对象的 `c1` 都独立存在
- 值一样，但不是同一个内存
- 所以 `&a.c1 != &b.c1`

这正是 PPT 里对比地址的重点：

“same value but different address”。