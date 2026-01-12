# 6. Designing Classes

## 1. Data Types in C++

### 1.1 C++ 中的数据类型层次

- **Primitive types**
    
    `int`, `double`, `char`, `bool` 等
    
- **Compound / User-defined types**
    - Enumerated types (`enum`)
    - Structures (`struct`)
    - Classes (`class`)
    - Collections（前一章）

**本章主线**：

从 **struct → class → encapsulation（封装）**

---

## 2. Enumerated Types（enum）

### 2.1 定义

> An enumerated type is a subset of int with named constants.
> 

本质：

- enum 的底层是 `int`
- 编译器默认从 0 开始编号

```cpp
enum Direction { NORTH, EAST, SOUTH, WEST };
// NORTH=0, EAST=1, SOUTH=2, WEST=3

```

---

### 2.2 显式赋值

```cpp
enum Coin {
    PENNY = 1,
    NICKEL = 5,
    DIME = 10
};

```

---

### 2.3 enum 的作用（笔试简答）

- 提高 **readability**
- 避免 magic numbers
- 限制合法取值范围

---

## 3. Structures（struct）

### 3.1 struct 的作用

```cpp
struct Point {
    int x;
    int y;
};

```

- 表示 **compound value**
- 字段用 dot operator 访问：`pt.x`, `pt.y`

---

### 3.2 struct 的局限性（理解题）

- 只描述 **representation**
- 数据和操作分离（C 风格）
- client 可随意修改字段，**无法保证数据合法性**

---

## 4. Classes & Objects

### 4.1 class 的基本思想

```cpp
class Point {
public:
    int x;
    int y;
};

```

- class = **template / blueprint**
- object = class 的 **instance**
- 创建对象的过程叫 **instantiation**

---

### 4.2 class 相比 struct 的核心提升

- 将 **data（状态）** 和 **behavior（方法）** 封装在一起
- 是 Object-Oriented Programming 的基础

---

## 5. Encapsulation（封装）与 private

### 5.1 为什么 instance variables 要 private（必考）

现代 OOP 原则：

**不推荐 public instance variables**

原因（三点标准答案）：

1. **Simplicity**
    
    Client 不需要理解内部细节
    
2. **Flexibility**
    
    内部实现可修改，不影响 client
    
3. **Security**
    
    防止 client 破坏数据不变量（invariants）
    

---

### 5.2 标准 class 结构（会写）

```cpp
class ClassName {
public:
    // public methods
private:
    // private instance variables
};

```

---

## 6. Interface vs Implementation（.h / .cpp）

### 6.1 分工

- `.h`：**interface**
    - class definition
    - method prototypes
- `.cpp`：**implementation**
    - method bodies

---

### 6.2 成员函数实现语法

```cpp
returnType ClassName::methodName(...) {
    ...
}

```

使用 `::`（scope resolution operator）

---

### 6.3 Include Guard 的作用

```cpp
#ifndef _point_h
#define _point_h
...
#endif

```

防止 **multiple inclusion**

---

## 7. Constructors（构造函数）

### 7.1 构造函数规则（必背）

- 名字与 class 相同
- **没有返回类型**
- 可以重载（overloading）
- 无参构造函数叫 **default constructor**
- 若未定义，C++ 自动生成一个空的 default constructor

---

### 7.2 构造函数的真正职责（理解题）

不仅是赋值，而是：

> 在对象创建时建立并保证 class invariants
> 

---

## 8. Initializer List（高频重点）

### 8.1 写法

```cpp
Point(int xc, int yc) : x(xc), y(yc) {}

```

---

### 8.2 为什么使用 initializer list（必考）

- 成员在创建时直接初始化
- 避免 “先默认初始化，再赋值”
- **const members 只能用 initializer list 初始化**
- 效率更高

对比：

```cpp
// 推荐
Point(int xc, int yc) : x(xc), y(yc) {}

// 不推荐
Point(int xc, int yc) {
    x = xc;
    y = yc;
}

```

---

### 8.3 C++11：NSDMI（了解）

可以在 class 中给成员默认值：

```cpp
int x = 10;

```

---

## 9. Getters and Setters

### 9.1 名词

- **Getter / Accessor**：读取 private 成员
- **Setter / Mutator**：修改 private 成员

---

### 9.2 为什么 setter 不常见（理解题）

- private 的目的是限制写权限
- setter 会削弱 encapsulation
- 许多类设计为 **immutable**

结论：

- getter 常见
- setter 应谨慎使用

---

## 10. Operator Overloading（难点重点）

### 10.1 为什么要重载运算符

让 user-defined type 像 built-in type 一样使用：

```cpp
cout << pt;

```

---

### 10.2 重载 `operator<<`（必考细节）

```cpp
ostream& operator<<(ostream& os, Point pt) {
    return os << pt.toString();
}

```

原因（两点都要写）：

1. `ostream` **不能被拷贝** → 必须传 `ostream&`
2. `<<` 支持 chaining → 必须返回 `ostream&`

---

### 10.3 Member vs Free Function

[详细精讲](assests/06.md)

**Member function**：

- 左操作数是 receiver
- 可直接访问 private data

**Free function**：

- 操作数对称，表达更自然
- 若无 public getters，需要 `friend`

关键理解：

> private 是 class-level，不是 object-level
> 

---

## 11. Rational Class（综合设计题）

### 11.1 设计目标

表示有理数 `x / y`，并保持不变量：

- fraction is in **lowest terms**
- denominator always **positive**
- 0 表示为 `0/1`

---

### 11.2 构造函数的作用

- 在创建对象时 enforce invariants
- 对象创建后始终合法

---

### 11.3 为什么 `gcd` 不在 `.h`

- `gcd` 是 **implementation detail**
- 不属于 public interface
- 放 `.cpp` 中符合 encapsulation

---

### 11.4 运算符重载

```cpp
Rational operator+(Rational r);
Rational operator-(Rational r);
Rational operator*(Rational r);
Rational operator/(Rational r);

```

---

## 12. TokenScanner Class

### 12.1 Token 的概念

- Token = logical unit
- 比 character 更高层

---

### 12.2 TokenScanner 的核心接口

- `setInput`
- `hasMoreTokens`
- `nextToken`

使用模式：

```cpp
while (scanner.hasMoreTokens()) {
    string token = scanner.nextToken();
}

```

设计思想：

- 从 **client perspective** 设计 API

---

## 13. Encapsulating Programs as Classes

### 13.1 为什么把程序封装成 class

- 避免 global variables
- 使用 private instance variables 共享状态
- 减少函数参数数量
- 提高安全性和可维护性

---

### 13.2 main 的角色

- 创建对象
- 调用方法
- main 变“薄”

---

## 14. getline：细节区分

### 14.1 两种 getline

- `std::getline(istream&, string&)`：free function
- `istream::getline(...)`：member function

区别原因：

- member function 有隐含 `this`
- free function 需要显式传 stream

---

## 15. 总结

> This chapter focuses on designing classes in C++, emphasizing
> 
> 
> **encapsulation**, **constructors**, **initializer lists**,
> 
> **operator overloading**, and the separation of **interface and implementation**.
>
