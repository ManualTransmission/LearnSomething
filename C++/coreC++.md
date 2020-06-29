# c++核心编程
## 1 内存分区模型
### 1.1程序运行前
#### 代码区

共享的
只读的

#### 全局区

##### 在全局区的
全局变量
    静态变量 static关键字
常量
    字符串常量
    const 修饰的全局常量

##### 不在全局区中的

​    局部变量
​    const 修饰的局部变量，局部常量



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

​	在设计类的时候，属性和行为写在一起，表现事物

**意义二：**

​    可以反属性和行为放在不同的权限下，加以控制

- public 公共，成员在类内可以访问，类外也可以访问
- protected 保护， 成员在类内可以访问，类外不可以
- private 私有，  成员在类内可以访问，类外不可以

**区别**： 在继承的时候，子类可以访问父类的protected，不可以访问private

#### 4.1.2 struct和class区别

唯一区别以及默认访问权限不同

struct默认权限是公共public，class默认权限是私有private

#### 4.1.3 成员属性设置为私有

- 优点一：可以自己控制读写的权限
- 优点二：对于写可以检测数据有效性

头文件中，第一行`#pragma once`防止重复

头文件中只保留声明，不需要实现；而在源文件中，要包含对应头文件，同时只保留成员函数，但是要加上作用域，不需要类名以及成员属性

---

### 4.2 对象的初始化和清理

#### 4.2.1 构造函数和析构函数

如果我们不写，编译器会提供相应的空实现

- 构造函数： 主要作用在于创建对象时为对象的成员属性初始化赋值
- 析构函数： 主要作用于对象销毁前系统自动调用，执行清理操作

**构造函数语法：**`类名(){}`

1. 构造函数，没有返回值也不写void
2. 函数名称与类名相同
3. 构造函数可以有参数，因此可以发生重载
4. 程序在调用对象时会自动调用构造，无须手动调用，而且只会调用一次

**析构函数语法：**`~类名(){}`

1. 构造函数，没有返回值也不写void
2. 函数名称与类名相同，在名称前加上符号~
3. 构造函数不可以有参数，因此不可以发生重载
4. 程序在对象销毁前会自动调用构造，无须手动调用，而且只会调用一次

```c++
class Person
{
public:
    //构造
    Person()
    {
        cout << "Person 构造函数调用" << endl;
    }
    //析构
    ~Person()
    {
        cout << "Person 析构函数调用" << endl;
    }
}

void test()
{
    Person p;
}
```

#### 4.2.2 构造函数的分类及调用

两种分类方式：

​	按参数：有参和无参(默认)

​	按类型： 普通和拷贝

```c++
class Person;
Person(const Person &p);//加const,同时以引用的方式，拷贝构造
//将传入的对象所有属性，拷贝到当前对象
```

调用：

1. 括号法

   `Person p;`//默认构造函数

   `Person p1(10);`//有参构造函数

   `Person p2(p1);`//拷贝构造函数

   注意事项：调用默认构造函数时，不要加()

   `Person p();`//因为编译器会认为是一个函数的声明，不会认为在创建对象

2. 显示法

   `Person p = Person(10);`

   `Person p1 = Person(p);`

   `Person(10)`是一个匿名对象，当前行执行结束，系统会立即回收

   不要利用拷贝构造函数来初始化匿名对象，编译器会认为`Person(p3) === Person p3`

3. 隐式转换法

   `Person p = 10;`//相当于`Person p = Person p(10)`

   `Person p = p1;`//相当于拷贝，`Person p = Person(p1);`

#### 4.2.3 拷贝构造函数的调用时机

- 使用一个已经创建完毕的对象来初始化一个新对象

- 值传递的方式给函数参数传值 

  ```c++
  void do(Person p){}
  void test()
  {
      Person p;
      do(p);//此时值传递会复制一个副本，所以会调用拷贝
  }
  ```

- 值方式来返回局部对象

  ```c++
  Person do()
  {
      Person p1;
      return p1;
  }
  void test()
  {
      Person p = do();
  }
  ```

#### 4.2.4 构造函数调用规则

c++编译器至少给一个类添加3个函数：

1. 默认构造（空实现）
2. 默认析构（空实现）
3. 默认拷贝构造（值拷贝）

构造函数调用规则：

- 如果用户定义有参构造，则不提供默认无参构造，但会提供默认拷贝
- 如果用户定义拷贝构造，则不提供其他构造函数

#### 4.2.5 深拷贝和浅拷贝

浅拷贝：简单的赋值拷贝操作，主要是编译器默认的拷贝构造提供

深拷贝：在堆区重新申请空间，进行拷贝操作

```c++
//拷贝构造函数 
class Person
{
public:
    //拷贝构造
    Person(const Person &p)
    {
        m_Age = p.m_Age;
        m_Height = new int(*p.m_Height);
    }
//析构函数
    ~Person()
    {
        if(m_Height != null)
        {
            delete m_Height;
            m_Height = null;
        }
    }
public:
    int m_Age;
    int * m_Height;s
}
//总结：如果属性有在堆区开辟的，一定要自己提供拷贝构造，否则容易浅拷贝
```

#### 4.2.6初始化列表

**作用：**c++提供了初始化列表语法，用来初始化属性

**语法**：`构造函数（）: 属性1(值1),属性2(值2)...`

```c+
Person() :m_A(10),m_B(20),m_C(30){}
Person(int a, int b, int c) :m_A(a), m_B(b), m_C(c){}
```

#### 4.2.7 类对象作为类成员

称为对象成员

```c++
class A{}
class B{
    A a;
}

```

构造时先构造类中的成员类，再调用自身类构造，析构时相反

#### 4.2.8 静态成员

静态成员就是在成员变量和成员函数前加上关键字static

- 静态成员变量

  - 所有对象共享同一份数据
  - 在编译阶段分配内存
  - 类内声明，类外初始化

  ```c++
  class Person{
  public:
      static int m_A;//类内声明
  private:
      static int m_B;//也有访问权限，类外访问不到私有的
  }
  int Person::m_A = 100;//类外初始化，双冒号
  //有两种访问方式：
  //通过对象进行访问 p.m_a
  //通过类名进行访问 Person::m_a
  ```

- 静态成员函数

  - 所有对象共享同一个函数

  - 静态成员函数只能访问静态成员变量

    ​	也有两种访问方式，通过对象访问，也可以通过类名访问，也有访问权限

---

### 4.3 c++对象模型和this指针

#### 4.3.1成员变量和成员函数分开存储

只有非静态成员变量才属于类的对象上

空对象占用内存空间为：1

c++编译器会给每个空对象也分配一个字节空间，为了区分空对象占内存位置

每个空对象也应该有一个独一无二的内存地址

#### 4.3.2this指针概念

this指针指向被调用的成员函数所属的对象

this指针是隐含在第一个非静态成员函数内的一种指针

this指针不需要定义，直接使用即可

1. 解决名称冲突，当形参和实参名称相同时使用

   `this->age = age;`

2. 返回对象本身用*this

#### 4.3.3 空指针访问成员函数

c++允许空指针访问

如果使用到this指针，进行判断，提高代码健壮性`if(this == null){return;}`

#### 4.3.4 const修饰成员函数 

**常函数**

- 成员函数加const后称为常函数
- 常函数内不可以修改成员属性
- 在成员属性声明时加关键字mutable后，在常函数中依然可以修改

```c++
class Person
{
public:
    //this指针本质是指针常量，指针指向不可以修改
    //在成员函数后面加const,修饰的是this指针，值也不可以修改
    void showPerson() const
    {
        //this->m_A =100;//error const修饰不可修改
        //this = null; //error this 指针不可以修改指针的指向
        this->m_B = 100; //可以修改
    }
    int m_A;
    mutable int m_B;//加Mutable关键字，可以修改
}
```

**常对象**

`const Person p;`//在对象前加const，变为常对象

常对象只能调用常函数 ，不可以调用普通成员函数 ，因为普通成员函数可以修改成员属性

---

### 4.4 友元

友元关键字 **friend**，可以访问私有成员

#### 4.4.1 全局函数做友元

```c++
class Building
{
    friend void goodGay(Building *building);//goodGay可以访问building中的私有成员
public:
    Building()
    {
        m_SittingRoom = "客厅";
        m_BedRoom = "卧室";
    }
    string m_SittingRoom;
    
private:
    string m_BedRoom;
}
void goodGay(Building *building)//全局函数 
{
    cout << "好基友的全局函数 正在访问：" << building->m_SittingRoom << endl;
    cout << "好基友的全局函数 正在访问：" << building->m_BedRoom << endl;
}
```

#### 4.4.2 类做友元

`friend class goodGay;`

#### 4.4.3 成员函数做友元

`friend void goodGay::visit();`

---

### 4.5 运算符重载

对已有的运算符重新进行定义，赋予其另一种功能，以适应不同的数据类型

对于内置数据类型，编译器知道如何进行运算

#### 4.5.1 加号运算符

1. 通过成员函数重载+号

```c++
class Person
{
public:
    Person operator+(Person &p)
    {
        Person temp;
        temp.m_a = this->m_a + p.m_a;
        temp.m_b = this->m_b + p.m_b;
        retrun temp;
    }
    int m_a;
    int m_b;
};

void test()
{
    Person p1;
    p1.m_a = 10;
    p2.m_a = 10;
    Person p2;
    p2.m_a = 20;
    p2.m_b = 20;
    Person p3 = p1 + p2;
    cout << "p3.m_a: " << p3.m_a << endl;
    cout << "p3.m_b: " << p3.m_b << endl;
}
```

2. 全局函数重载+号

```c++
Person operator+(Person &p1, Person &p2)
{
    Person temp;
    temp.m_a = p1.m_a + p2.m_a;
    temp.m_b = p1.m_b + p2.m_b;
    retrun temp;
}
```

```c++
//成员函数重载本质：
Person p3 = p1.operator+(p2);
//全局函数重载本质：
Person p3 = operator+(p1, p2);
//运算符重载也可以发生函数重载
```

- 总结1：对于内置数据类型的表达式的运算符不可以发生重载

- 总结2：不要滥用运算符重载

#### 4.5.2 左移运算符重载

作用：可以输出自定义的数据类型

```c++
class Person
{
public:
//通常不会利用成员函数重载<<运算符，因为cout会在右侧
    int m_a;
    int m_b;
};
//只能利用全局函数重载左移运算符
ostream &operator<<(ostream &cout, Person &p)//本质 operator(cout, p)，简化 cout << p
{
    cout << "m_a" << m_a << "m_b" << m_b;
    return cout;
}
void test()
{
    Person p;
    p.m_a = 10;
    p.m_b = 10;
    cout << p << endl;//可以直接输出对象
}
```

总结:重载左移配合友元可以实现输出自定义数据类型

#### 4.5.3 递增运算符重载

作用：实现自己的整形数据 

```c++
//重载前置++
MyInteger& operator++()//返回引用是为了一直对一个数据进行递增
{
    m_Num++;
    return *this;
}
//后置++
//int代表占位参数，可以用于区分前置和后置，让编译器知道是后置递增
MyInteger operator++(int)//后置返回值
{
    MyInteger temp = *this;
    m_Num++;
    return temp;
}
```

#### 4.5.4 赋值运算符重载

c++编译器至少给一个类添加4个函数 

1. 默认构造
2. 默认析构
3. 默认拷贝构造
4. 赋值运算符operator=对属性进行值拷贝

注意深浅拷贝的问题

```c++
Person& operator=(Person &p)//解决连等的问题
{
    //先判断是否有属性在堆区
    if(m_Age != null)
    {
        delete m_Age;
        m_Age = null;
    }
    m_Age = new int(*p.m_Age);//深拷贝
    //返回对象自身
    return *this;
}
```

#### 4.5.5 关系运算符重载

**作用：**重载关系运算符，可以让两个自定义类型对象进行对比操作

```c++
bool operator==(Person &p)
{
    if(this->m_Name == p.m_Name && this->m_Age == p.m_Age)
    {
        return true;
    }    
    return false;
}
```

#### 4.5.6 函数调用运算符重载

- 函数调用运算符()也可以重载
- 也称为仿函数
- 仿函数没有固定写法，非常灵活

```c++
class MyPrint
{
public:
    //重载函数调用运算符
    void operator()(string test)
    {
        cout << test << endl;
    }
};
void test01()
{
    MyPrint myPrint;
    myPrint("Hello world");//由于使用非常类似函数调用，因此称为仿函数 
}
//仿函数非常灵活，没有固定写法
class MyAdd
{
public:
    int operator()(int num1, int num2)
        return num1 + num2;
};
void test02()
{
    MyAdd myAdd;
    int ret = myAdd(10, 10);
    cout << "ret =  " << ret << endl;
    //匿名函数对象
    cout << MyAdd()(100, 100) << endl;
}
```

---

### 4.6 继承 

**继承是面向对象的三大特性之一**

#### 4.6.1 继承的基本语法

**语法：**`class 子类 ：继承方式 父类`

子类也称为派生类，父类也称为基类

```c++
class BasePage
{};
class Java:public BasePage
{
public:
    void content(){}
};//减少重复代码
```

#### 4.6.2 继承方式

继承方式一共有有三种：

- 公共继承 :public

  父类public还是public，protected还是protected

- 保护继承 :protected

  父类中public,protected，都是protected

- 私有继承 :private

  父类中public,protected,都是private

父类中private子类都不可以访问

#### 4.6.3 继承中的对象模型

**问题:** 从父类继承过来的成员，哪些属于子类对象中？

父类中所有非静态成员属性都会被子类继承下去

父类中私有成员属性，是被编译器隐藏了，因此访问不到，但是确实继承了

```cmd
//vs开发人员命令提示符
> cl /d1 reportSingleClassLayout类名 文件名
//打印对象模型
```

#### 4.6.4 继承中构造和析构顺序

问题：先有父类还是先有子类

先父类构造，再子类构造

先子类析构，再父类析构，与构造顺序相反

#### 4.6.5 继承同名成员处理方式

- 访问子类同名成员 直接访问即可

- 访问父类同名成员 加作用域

- 如果子类中出现了和父类中同名的函数，子类中的同名成员会隐藏掉父类中所有的同名成员函数 ，需要加作用域才能访问到

`a.Base::func();`

#### 4.6.6 继承中同名的静态成员处理方式

与非静态处理方式一致

注意：通过类名访问

```c++
Son::m_a;
son::Base::m_a;//第一个::表示通过类名访问，第二个::表示访问父类作用域
```

#### 4.6.7 多继承语法

语法：`class 子类 ：继承方式 父类1，继承方式 父类2...`

多继承可能会引发父类中出现同名成员，需要通过加作用域方式访问

实际开发中不建议使用多继承

#### 4.6.8 菱形继承

概念：

两个派生类继承同一个基类

又有某个类同时继承两个派生类

这种继承被称为菱形继承，又称钻石继承

利用虚继承，解决菱形继承的问题，在继承之前加上关键字virtual变为虚继承，最大的类称为虚基类

```c++
class b;
class c;
class a : virtual public b, :virtual public c;
//vbptr: virtual base pointer,指向vbtable
```

### 4.7 多态

分为两类

- 静态：函数重载和运算符重载
- 动态：派生类和虚函数实现运行时多态

区别：

- 静态函数地址早绑定，-编译阶段确定
- 动态函数地址晚绑定，-运行阶段确定

---

//

