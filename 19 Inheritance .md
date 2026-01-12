# 19. Inheritance

## 1. Class Hierarchies（类层次结构）

### 1.1 基本概念

- **Superclass (Base class)**
- **Subclass (Derived class)**
- Subclass 是 Superclass 的一种 **specialization**

**对象语义（必背）**

> If an object is an instance of a subclass,
> 
> 
> it is also an instance of all superclasses above it.
> 

注意：

superclass : subclass ≠ superset : subset

（例如 Rectangle : Square 在设计上并不合理）

---

## 2. Representing Inheritance in C++

```cpp
classSubclass :public Superclass {
    ...
};
```

- 本课程只讨论 **public inheritance**
- Subclass 可以访问：
    - public members
    - protected members
- Subclass **不能访问** private members

---

## 3. Polymorphism：Overloading vs Overriding（必考对比）

### 3.1 Overloading

- 同名函数
- 参数列表不同
- 不依赖继承
- **compile-time binding**
- 属于 static polymorphism

### 3.2 Overriding

- 同名函数
- **相同 signature**
- 发生在继承关系中
- **run-time binding**
- 属于 dynamic polymorphism

判断原则：

- 看参数 → overloading
- 看继承 + signature → overriding

---

## 4. Virtual Methods（本章核心）

## 4.1 问题背景：C++ 默认“不多态”

先记住一句非常重要的话：

> C++ 默认是 static binding，而不是 dynamic binding。
> 

也就是说：

**函数调用默认在编译期就决定了。**

### 例子（不写 virtual）

```cpp
classA {
public:
voidf() { cout <<"A"; }
};

classB :public A {
public:
voidf() { cout <<"B"; }
};

A* p =newB();
p->f();

```

输出：

```
A

```

### 为什么？

因为：

- `p` 的 **声明类型是 A***
- `f()` 不是 virtual
- 编译器在 **compile time** 就决定：调用 `A::f()`

---

## 4.2 virtual 的真正作用

```cpp
classA {
public:
virtual voidf() { cout <<"A"; }
};

```

此时：

```cpp
A* p =newB();
p->f();

```

输出：

```
B

```

### 本质解释（必会）

- `virtual` 告诉编译器：
    
    > “不要在编译期决定调用哪个函数”
    > 
- 实际调用：
    - 在 **runtime**
    - 根据 **对象真实类型（B）**

这就是 **runtime polymorphism**。

---

## 4.3 virtual 的技术含义（考试解释版）

当一个方法被声明为 virtual：

1. 对象内部会维护一张 **virtual table（vtable）**
2. 指针或引用调用该方法时：
    - 通过 vtable 查找
    - 决定实际要调用的函数版本

你不需要画 vtable，但要会说这句话。

---

## 4.4 为什么 virtual 必须写在 superclass？

```cpp
classBase {
public:
virtual voidf();
};

```

关键原因：

- **调用是否动态分派，是由“声明类型”决定的**
- client 往往只“看见” superclass

```cpp
Base* p =new Derived();

```

如果 `Base::f()` 不是 virtual：

- Derived 中即使写了同名函数
- **也不会发生多态**

---

## 4.5 一句话总结（可直接背）

> virtual 的作用是：
> 
> 
> **让函数调用在运行期，根据对象的真实类型进行分派，而不是根据指针的声明类型。**
> 

---

# 5. Abstract Class（抽象类）

## 5.1 为什么需要 abstract class？

先问一个问题：

> Employee 到底“是什么”？
> 
- 现实中：
    - 没有“抽象的员工”
    - 只有 HourlyEmployee / SalariedEmployee

所以：

> Employee 只用于定义“所有员工共有的行为接口”
> 

---

## 5.2 C++ 中的 abstract class

```cpp
classEmployee {
public:
virtual doublegetPay() =0;
};

```

### `= 0` 的含义

- 该函数 **没有实现**
- 编译器不允许创建该类的对象

```cpp
Employee e;// 编译错误
Employee* p;// 正确

```

---

## 5.3 Abstract class 的三条核心性质（必背）

1. **不能被实例化**
2. **可以作为指针或引用类型**
3. **用于定义接口 + 支持多态**

---

## 5.4 为什么 abstract class + pointer 是“黄金搭档”？

```cpp
Employee* ep =newHourlyEmployee();
ep->getPay();

```

解释链条（非常重要）：

1. `Employee` 定义了接口
2. `getPay()` 是 virtual
3. `ep` 指向一个具体子类对象
4. 调用在 runtime 根据真实类型分派

如果 Employee 不是 abstract：

- 就可能被错误实例化
- 设计语义被破坏

---

## 5.5 Expression 例子

```cpp
classExpression {
public:
virtua linteval(...) =0;
};

```

你永远不会写：

```cpp
Expression e;// 没有意义

```

你只会写：

```cpp
Expression* exp;

```

**这正是 abstract class 的“正确使用姿势”。**

---

## 5.6 一句话总结

> Abstract class 的目的不是“不能用”，
> 
> 
> 而是 **只用来规定行为，不用来创建对象。**
> 

---

# 6. Object Slicing（C++ 继承最大陷阱）

这是**期末最容易被忽视、但最容易扣分的点**。

---

## 6.1 slicing 是怎么发生的？

```cpp
classA {
public:
int x;
};

classB :public A {
public:
int y;
};

B b;
A a = b;

```

此时：

- `a.x` 被拷贝
- `b.y` **被直接丢弃**

这就是 **object slicing**。

---

## 6.2 为什么 C++ 会 slicing？

根本原因：

> C++ 是 value semantics 的语言。
> 

当你写：

```cpp
A a = b;

```

发生的是：

- 构造一个新的 A 对象
- 从 b 中拷贝 A 那一部分

编译器**不关心** b 还有没有额外字段。

---

## 6.3 slicing 有多危险？

### 场景一：方法调用错误

```cpp
A a = b;
a.f();// 永远调用 A::f()

```

即使 `f()` 是 virtual，

**对象已经被裁剪成 A 了**。

---

### 场景二：容器中存对象

```cpp
Vector<Employee> v;
v.add(bob);// slicing

```

后果：

- 所有子类信息丢失
- 多态完全失效

---

## 6.4 正确做法：永远用 pointer / reference

```cpp
Vector<Employee*> v;
v.add(&bob);

```

为什么 pointer 不会 slicing？

- pointer 只是地址
- 不发生对象拷贝
- 指向完整对象

---

## 6.5 slicing 与 virtual 的关系（关键）

很多人误以为：

> “我写了 virtual 就没事了”
> 

这是错误的。

- slicing 发生在 **对象拷贝阶段**
- virtual 只影响 **函数调用阶段**

一旦对象被 slicing：

- virtual 也救不了

---

## 7. Avoiding Slicing

两种策略：

1. 禁止拷贝
    - private copy constructor / assignment
    - stream hierarchy 使用该策略
2. 使用指针
    - 但需要显式管理内存

---

## 8. Graphical Shapes（GObject Hierarchy）

### 8.1 设计要点

```cpp
classGObject {
public:
voidsetLocation(...);
voidmove(...);
voidsetColor(...);
virtualvoiddraw(GWindow&) =0;
protected:
double x, y;
    string color;
};

```

- `draw` 是 pure virtual
- GObject 是 abstract class
- 所有图形对象通过 `GObject*` 使用

---

### 8.2 为什么必须用 pointer

```cpp
Vector<GObject*> displayList;

```

- 同一个容器存 GLine / GRect / GOval
- 调用 `draw()` 时根据实际对象类型绘制
- 如果不用 pointer：
    - slicing
    - 多态完全失效

---

## 9. Initializer List（必考细节）

```cpp
GSquare(...) :GRect(...) { }

```

作用：

- 显式调用 superclass constructor
- 确保 superclass 先初始化

注意：

- const / reference 成员 **只能**在 initializer list 初始化
- assignment ≠ initialization

---

## 10. Multiple Inheritance（理解为主）

### 10.1 使用动机

解决多个类共享同一行为的问题

例如：`GRect` 与 `GOval` 都需要 `setFilled`

---

### 10.2 Diamond Problem

当两个类都继承自同一个 superclass，而第三个类再同时继承它们：

- 会出现 superclass 的重复
- 成员访问变得 ambiguous
- 编译失败

---

### 10.3 Virtual Inheritance

```cpp
classB :virtual public A {};
classC :virtual public A {};

```

- 保证最终对象中只有一份 A
- stream hierarchy 采用该设计
- 考试只考概念，不考代码细节

---

## 11. Expression Hierarchy（Part 2，不考但用于理解）

### 11.1 Expression 是 Abstract Class

```cpp
classExpression {
public:
virtual inteval(EvalState&) =0;
virtual stringtoString() =0;
};

```

Subclasses：

- ConstantExp
- IdentifierExp
- CompoundExp

---

### 11.2 对 Part 1 的核心启发

- abstract class 的真实用途
- virtual method 的运行期分派
- pointer 是多态成立的前提

```cpp
Expression* exp;
exp->eval(context);

```

compile time 不知道 exp 的具体类型，

只能依赖 runtime polymorphism。

---

## 12. Parsing & Grammar（不考）

- grammar
- precedence parser
- TokenScanner
- readE / readT 实现

仅用于说明：

**递归结构 + 抽象类 + 多态 = 可扩展系统**

---

## 13. 考试级总结（直接背）

- 只用 public inheritance
- virtual 决定 runtime polymorphism
- abstract class 定义接口
- slicing 是 value assignment 的结果
- polymorphism 必须通过 pointer
- initializer list 用于调用 superclass constructor
- multiple inheritance 有 diamond problem
- virtual inheritance 解决重复基类问题