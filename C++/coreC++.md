# c++核心编程
## 1 内存分区模型
### 1.1程序运行前
#### 代码区

- 共享的
- 只读的

#### 全局区

##### 在全局区的

    - 全局变量
    - 静态变量 static关键字
    - 常量
    - 字符串常量
    - const 修饰的全局常量

##### 不在全局区中的

    - 局部变量
    - const 修饰的局部变量，局部常量



### 1.2程序运行后
#### 栈区

    放局部变量、函数形参
    
    栈区数据由编译器管理开辟和释放
    
    注意：不要返回局部变量的地址，因为由编译器管理
    
    局部变量存放在栈区，栈区的数据在函数执行完成后释放

#### 堆区

    由程序员分配释放，若程序员不释放，程序结束时由操作系统回收
    
    在C++中主要利用new在堆区开辟内存

### 1.3 new操作符

​	用delete释放

 - new的基本语法

   `int *p = new int(10);` 返回的是该数据类型的指针

   `delete p;`                          释放

 - 在堆区利用new开辟数组

   `int *p = new int[10]`10代表数组有10个元素

   `delete[] p;`                    释放

---



---

## 2 引用

### 2.1 引用的基本使用

​	引用：给变量起别名

​	语法：数据类型 &别名 = 原名`int &b = a;`

### 2.2 引用注意事项

​	引用必须初始化，一旦初始化就不可以更改

### 2.3 引用做函数参数

​	作用：函数传参时，可以利用引用的技术让形参修饰实参

​	优点：可以简化指针修改实参

		- 值传递：不会修饰实参
		- 地址传递：会修饰实参
		- 引用传递：也会修饰实参（形参相当于实参的一个别名）

### 2.4 引用做函数返回值

​	注意：

			- 不要返回局部变量的引用

```c++
int& test()
```

			- 函数的调用可以作为左值

```c++
test() = 1000; //如果函数返回值是一个引用，则也可作为左值使用
```

### 2.5 引用的本质

​	**本质：**引用的本质在c++内部实现是一个指针常量

```c++
int& ret = a; //转换为int* const ret = &a;
```

### 2.6 常量引用

​	**作用：**修饰形参，防止误操作

​	引用必须引一块法合法内存，堆区或栈区

```c++
const int& ret = 10;
//加上const之后，编译器将代码优化为int temp = 10; const int& ret = temp;
//只读，不可修改
```

```c++
int a = 100;
void show(int &val)
{
    cout << val << endl;
}
show(a);//show函数可能会修改a值
//避免修改用
void show(const int &val);
```

---

## 3 函数提高

### 3.1 函数默认参数

​	**语法**： 返回值类型 函数名(形参 = 默认值)

```c++
int func(int a, int b = 20, int c = 30);
{
    return a + b + c;
}
//调用
func(10);//60
func(10, 30);//70
```

​	**注意事项：**

1. 如果某个位置已经有了默认值，那么后面的参数也必须有默认值
2. 如果函数声明有默认值，函数实现就不能有默认值//二义性，声明和实现只能有一个有

### 3.2 函数占位参数

​	c++中函数的形参列表中有点位参数

​	语法：`返回值类型 函数名(数据类型) `//不用写函数名，只写数据类型

```c++
void func(int a, int);
func(10, 10); //现阶段没有实际意义
//点位参数还可以有默认参数
void func(int a, int = 10);
```

### 3.3 函数重载

#### 3.3.1 函数重载概述

**作用** ：函数名可以相同，提高复用性，根据参数不同调用不用函数 

**函数重载条件**：

- 同一个作用域下
- 函数名称相同
- 函数参数类型不同，或者个数不同，或者顺序不同

```c++
void func()
{
    cout << "call func()" <<endl;
}
void func(int a)
{
    cout << "call func(int a)" << endl;
}
void func(double a)
{
    cout << "call func(double a)" << endl;
}
//顺序不同
void func(int a, double b);
void func(double a, int a);
```

**注意事项：**

- 函数的返回值不可以作为函数重载的条件

```c++
void func();
int func();
```

#### 3.3.2 函数重载注意事项

**两个坑**

- 引用作为重载条件

```c++
void func(int &a)
{
    cout << "call func(int &a)" << endl;
}
void func(const int &a)
{
    cout << "call func(const int &a)" << endl;
}
int a = 10;
func(a);//call func(int &a)
func(10);//== const int &a = 10; call func(const int &a)
```

- 函数重载碰到默认参数

```c++
void func(int a, int b = 10);
void func(int a);
//func(10);//两个都能调，有二义性，所以会报错，要避免出现
```

---

## 4 类和对象

c++面向对象三大特性：**封装、继承、多态**

c++认为万事万物皆为对象，对象上有其属性和行为

### 4.1 封装

#### 4.1.1 封装的意义

封装的意义：

- 将属性和行为作为一个整体，表现生活中的事物
- 将属性和行为加以权限控制

**意义一：**

​	

---


