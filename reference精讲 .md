# reference精讲

# 1. 引用是什么 Reference（本质）

## 1.1 一句话定义

**reference = an alias（别名）**

它不是新对象，不是装地址的变量；它只是给一个“已有对象”起了另一个名字。

```cpp
int x =10;
int& r = x;// r 是 x 的别名

```

此时：

- `r` 和 `x` 是同一个东西（同一块内存）
- `&r == &x`
- 修改 `r` 就是修改 `x`

```cpp
r =20;// x 也变成 20

```

## 1.2 必须记住的 3 条铁律（考试级）

1. **Must bind at initialization**（必须初始化绑定）

```cpp
int& r;// 错：未绑定对象

```

1. **Cannot reseat**（不能改绑）

```cpp
int a=1, b=2;
int& r=a;
r=b;// 注意：这是给 a 赋值为 b，不是让 r 指向 b

```

1. **No null reference（语言层面）**

```cpp
int& r = * (int*)nullptr;// 语法上可能写得出来，但行为未定义，别这么做

```

---

# 2. 引用 vs 指针 Pointer（你必须能对比）

| 维度 | Reference `T&` | Pointer `T*` |
| --- | --- | --- |
| 本质 | alias | holds an address |
| 是否必须绑定 | 必须 | 可以为空 |
| 是否可改指向 | 不可 | 可 |
| 访问方式 | 像普通变量 | 需要 `*`/`->` |
| 常见用途 | 参数传递、返回值、运算符重载 | 动态内存、可空、数组遍历、链表 |

一句话总结：

**reference 用来“像变量一样用”，pointer 用来“像地址一样用”。**

---

# 3. 引用的核心用法全家桶（按考试最常出现的分类）

## 3.1 作为函数参数：pass by reference（最常见）

### (A) `T&`：可修改参数

```cpp
voidinc(int& x) { x++; }

```

调用方变量会被改。

适用：swap、修改对象内容、填充结果等。

---

### (B) `const T&`：只读引用（最常用、最安全）

```cpp
voidprint(const string& s) { cout << s; }

```

意义：

- 避免拷贝（效率）
- 保证不修改（安全）
- 可绑定到临时对象（下面会讲）

适用：几乎所有“只读输入参数”。

---

### (C) 为什么不用 `T`（按值）？

按值会 copy：

- 对大对象（string/vector/class）成本高
- 还可能触发 copy constructor（第 12 章核心）

所以常用：

- 小 primitive（int/double）用值传递
- 大对象用 `const T&`

---

## 3.2 作为返回值：return by reference（高频易错）

### (A) 返回引用的意义

避免拷贝，并允许链式操作

典型例子：assignment operator

```cpp
T& T::operator=(const T& src) { ...return *this; }

```

---

### (B) 什么时候可以返回引用？（铁律）

只有当你返回的是**调用后仍然存在的对象**：

- 返回成员（member）
- 返回静态变量（static）
- 返回通过引用传入的东西

例：

```cpp
int&at(vector<int>& v,int i) {return v[i]; }// OK

```

---

### (C) 绝对禁止：返回局部变量引用（必考坑）

```cpp
int&f() {
int x =3;
return x;// 错：x 出函数就没了 -> dangling reference
}

```

记忆法：

**return reference 不允许指向 stack frame 的局部变量。**

---

## 3.3 引用与临时对象（最容易混）

### (A) 非 const 引用不能绑临时值

```cpp
int& r =3;// 错

```

### (B) const 引用可以绑临时值（非常重要）

```cpp
constint& r =3;// 对

```

原因（考试可写）：

- `const T&` 允许绑定 rvalue
- 编译器会延长临时对象 lifetime 到引用作用域结束（lifetime extension）

这也是为什么你经常看到：

```cpp
voidf(const string& s);
f("hello");// OK，因为会生成临时 string 绑定到 const ref

```

---

## 3.4 引用用于“给函数提供可写出口”（output parameter）

```cpp
booldivmod(int a,int b,int& q,int& r) {
if (b ==0)returnfalse;
    q = a / b;
    r = a % b;
returntrue;
}

```

这在 C++ 里很常见：一个函数返回 bool 表示成功，输出靠引用参数。

---

# 4. 引用在类里：`this` 与成员函数 const（你 PPT 里出现的）

## 4.1 `this` 是 pointer，不是 reference

在成员函数内部：

- `this` 类型是 `T*`
- 所以 `this` 是对象本身（T）

这解释了：

```cpp
return *this;// 返回对象本身（再作为引用返回）

```

---

## 4.2 const method（成员函数后面的 const）

```cpp
intsize()const;

```

含义：

- 这个方法保证不修改对象状态（成员变量）
- `this` 在这种方法里相当于 `const T*`

用途：

- 强迫你写“只读方法”
- 让 const 对象也能调用这些方法

---

# 5. 引用的“各种 &”到底怎么区分（你最需要的部分）

C++ 里 `&` 有三种完全不同的含义：

## 5.1 作为类型：reference

```cpp
int& r = x;// r 是引用
const T& src = ...;// const 引用

```

## 5.2 作为运算符：address-of（取地址）

```cpp
int* p = &x;// & 是取地址

```

## 5.3 作为运算符重载：bitwise-and（按位与）

```cpp
a & b

```

考试时判断方法：

- `T&` 出现在类型位置 → 引用
- `&x` 出现在表达式前 → 取地址
- `a & b` 两边都有操作数 → 按位与