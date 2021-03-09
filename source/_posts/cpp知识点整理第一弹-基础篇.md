---
title: C++知识点整理第一弹-基础篇
date: 2021-03-09 15:54:47
tags: C++
categories: C++
cover: https://jiajuu.top/2021/03/09/cpp知识点整理第一弹-基础篇/cpp知识点整理第一弹-基础篇.jpg
---

# 在main()执行前后的操作

- `main()`函数执行之前（主要是初始化系统相关资源）

  - 设置栈指针
  - 初始化静态变量`static`以及全局变量`global`，也就是我们C++内存分区中的全局静态区区中的数据段
  - 对未初始化的部分全局变量赋初值，即数值型赋值为`0`，布尔型赋值为`false`，指针赋值为`NULL`，也就是全局区中的BSS段
  - 全局对象的初始化，调用其构造函数
  - 将`main()`函数的参数传入`main()`函数中

- `main()`函数执行之后

  - 全局对象的析构函数

  - 用`atexit()`函数注册的函数

    ```
    void func(){
    	cout<<"我在main()函数后才执行嗷嗷嗷！！"
    }
    
    int main(){
    	atexit(func);//注册程序正常终止时被调用的函数
    	cout<<"main exit";
    	return 0;
    }
    ```



# 内存对齐

- 类/结构体中的成员是按照声明顺序存储的，即第一个成员的地址和整个类/结构体的地址相同
- 类/结构体的字节大小是其最大的成员的整数倍，也就是说若有double成员，按8字节对齐



# 指针和引用的区别

- 指针是一个变量，存储着一个地址；引用是变量的一个别名，实质上是同一个东西。
- 指针可以多级（例如：`int** p`）；引用只有一级。
- 指针初始化时可以为空；引用在初始化时不能为空。
- 指针初始化后可以改变指向；引用在初始化后便不能再改变。
- 指针传入`sizeof()`函数得到的是指针的大小（64系统8字节，32位系统4字节）；引用传入`sizeof()`函数得到所指变量的大小。
- 指针作为参数传入函数时，也是把实参拷贝到形参（两者不是同一个变量），只不过两者指向的地址相同；引用作为参数传入函数时，是作为一个别名的存在（两者是同一个变量）。
- 指针的自增（++）是地址自增；引用的自增是变量的自增
- 指针作为函数返回，返回的是地址（指针常用来指向堆中的变量，函数结束时不会释放堆中的数据）；引用作为函数的返回，若返回局部变量的引用会报错（局部变量在函数结束时被释放了）



# 堆和栈的区别

- 申请方式：
  - 栈是系统自动分配的
  - 堆是程序员自行申请和释放的
- 申请大小限制：
  - 栈的大小都是操作系统设置好的，可以通过`ulimit -a`查看以及`ulimit -s`修改
  - 堆是不连续的内存区域，大小可以灵活调整
- 扩展方向：
  - 栈的扩展方向是从栈顶向栈底扩展
  - 堆是向高地址扩展，是不连续的内存区域
- 申请效率：
  - 栈由系统分配，速度快，没有碎片
  - 堆由程序员分配，速度满而且由碎片



# 区别指针类型

```
int *p[10];
int (*p)[10];
int *p(int);
int (*p)(int);
```

- `int *p[10]`表示指针数组，我们可以看作是`int* p[10]`，表示10个整型指针数组
- `int (*p)[10]`表示数组指针，表示一个指针变量，指向一个元素个数为10的整型数组
- `int *p(int)`表示函数声明，我们可以看作是`int* p(int)`，表示函数名为p，参数为int类型，返回值为int*类型
- `int (*p)(int)`表示函数指针，表示指针指向一个函数，这个函数参数为int类型，返回值为int类型



# C++的内存内存模型

- 栈区
- 堆区
- 函数区
- 全局静态区
- 常量区



# 虚函数表存放的内存分区，虚表指针vptr的初始化时间

- 虚函数表存放的内存分区：
  - 虚函数表是一个全局共享的元素，也就是说每个类全局只有一个虚函数表，在编译时便构造完毕
  - 虚函数表类似一个数组，类对象存储的vptr指针指向虚函数表指针
  - 虚函数表不是函数也不是程序代码，所以不可能存储在代码段
  - 虚函数表存储着虚函数的地址，即虚函数表的元素指向类成员函数的地址，而类中虚函数的个数在编译时期就已经确定下来了，所以其不是动态分配内存空间存储虚函数表，也就是说不在堆区中
  - 虚函数表类似于类中的静态成员变量（静态成员变量也是全局共享的，大小也是确定的），所以最有可能存放在全局数据区

> 实际上，虚函数表vtable在Linux/Unix存放在可执行文件的只读数据段（也就是C++内存模型中的常量区）中；而微软的编译器将虚函数表存放在常量段。虚函数则存放在代码段（也就是C++内存模型中的函数区中）

- 虚表指针的初始化时间：

  由于虚表指针和虚函数密切相关，对于有虚函数或继承于拥有虚函数基类的类的实例化时，在构造函数执行时便对虚表指针进行了初始化，并存放在对象内存布局的最前面



# new/delete与malloc/free的异同

- 相同点：

  - 用于内存的动态申请和释放

- 不同点：

  - new/delete是C++的运算符；malloc/free是C/C++语言标准库函数

  - new能自动计算需要分配的空间大小，无需程序员指定；malloc需要程序员指定大小

  - new是类型安全的；malloc则不是

    ```
    int* p = new float[2];//分配失败时，编译会报错
    int* p = (int*)malloc(2*sizeof(float));//分配失败时，编译不会报错
    ```

  - new运算符会调用`operator new()`标准库函数分配足够空间并调用相关对象的构造函数，<br>delete运算符会调用`operator delete()`标准库函数释放该对象所用内存；<br>malloc/free却没有相关调用

  - new/delete不需要库文件支持；malloc/free需要库文件的支持

  - new/delete除了分配释放内存外，还会调用构造和析构函数；malloc/free仅仅进行分配释放内存，不做其他操作

    > new的底层封装了malloc，直接free不会报错，但仅仅释放了内存，对象并不会析构。

  - new/delete支持`operator new()`/`operator delete()`函数的运算符重载以及函数覆写；malloc/free仅支持覆盖

  - new/delete返回的是具体类型的指针；malloc/free返回类型是void类型指针（必须进行类型转换）



# new和delete的实现过程

- new的实现过程：
  1. 调用`operator new()`标准库函数，分配足够大的内存
  2. 运行构造函数
  3. 返回新分配内存并构造后的对象指针
- delete的实现过程
  1. 对指针指向的对象运行析构函数
  2. 调用`operator delete()`标准库函数释放该对象所用内存



# delete和delete[]的区别

- delete只会调用一次析构函数
- delete[]会调用数组中每个元素的析构函数。





# 宏定义和函数的区别

- 宏函数在编译时完成替换，被替换的文本参与编译。相当于对代码进行直接文本替换，运行时不存在函数调用，执行更快；函数的调用需要在运行时跳转到具体的函数代码段，涉及到压栈出栈
- 宏定义属于在代码中直接替换文本，没有返回值；函数调用具有返回值
- 宏定义参数没有类型，不会进行类型检查；函数参数具有类型，需要检查类型
- 宏定义不要在最后加分号



# 宏定义和typedef区别

- 宏定义主要用于定义常量以及书写复杂的内容；typedef主要用于定义类型的别名
- 宏定义替换发生在编译阶段之前，属于对代码进行文本替换；typedef属于编译的一部分
- 宏定义不检查类型；typedef会检查数据类型
- 宏定义不是语句，最后不添加分号；typedef时语句，结束需要添加分号

> 注意对指针的操作：
>
> - typedef char* p_char，例如：
>   - p_char c1,c2
>   - 效果等同于char* c1,char* c2,表示定义了两个字符型指针变量
>
> - #define p_char char*，例如：
>   - p_char  c1,c2
>   - 效果等同于char* c1,c2,，表示定义了一个字符型指针变量和一个字符型变量



# 变量声明和定义的区别

- 声明仅仅是把变量声明的位置以及类型提供给编译器，并不会分配内存空间
- 定义会在定义的地方为其分配存储空间
- 相同变量可以在多处声明（外部变量extern）；但相同变量只能在一处定义



# 必须使用初始化成员列表的情况

- 初始化一个const成员
- 初始化一个reference成员
- 初始化基类的私有变量（调用基类的构造函数，而该函数有一组参数）
- 初始化数据成员对象的私有变量（调用数据成员对象的构造函数，而该函数有一组参数）



## strlen()和sizeof()区别

- `sizeof()`是运算符，而非函数，结果在编译时得到而非在运行中获得；`strlen()`是字符处理的库函数
- `sizeof()`的参数可以是任何的数据类型或着数据；`strlen()`的参数只能是字符指针且结尾是`\0`的字符串

> 注意：因为`sizeof()`的值在编译时已确定，所以无法用来得到动态分配（运行时分配）存储空间的大小
>
> ```
> int main(){
> 	const char* str = "name";
> 	sizeof(str);//取的是指针的长度，大小为4
> 	strlen(str);//取的是字符串的长度，不包含结尾的'\0'，大小为4
> 	return 0;
> }
> ```



# 常量指针和指针常量

- 常量指针是一个指针，指向一个只读变量。`int const *p`或者`const int* p`
- 指针常量是一个不能中途改变指向的指针，该指针是一个常量。`int* const p`



# a和&a的区别

```
int main(){
	int a[10];
	int (*p)[10] = &a;
	return 0;
}
```

- `a`是数组名，也是数组首元素的地址，`a+1`表示在地址值加上int类型的大小（即a = 0x00000001,a+1 = 0x00000005，`*(a+1)` == `a[1]`）
- `&a`是数组的指针，其类型为`int (*)[10]`，`p+1`时，编译器会认为是数组首地址加上整个数组的偏移（10个int型变量），值为数组a尾元素的后一个元素地址
- 若`(int*)p`，此时输出`*p`，值为a[0]的值，因为被转为`int *`类型，解引用时按照int类型大小来读取



# 数组名和指针的区别

- 二者均可通过增减偏移量来访问数组中的元素
- 数组名不是真正意义上的指针，可以理解为指针常量，数组名无法自增、自减操作
- 当数组名当作形参传递给函数后，就失去了原有的特性，退化成一般指针，可以自增、自减的操作，而`sizeof()`运算符不能再得到原数组的大小了



# 野指针和悬空指针

> 两者都是指向不安全不可控的内存区域。

- 野指针，指的是没有被初始化过的指针

```
int main(){
	int* p;
	//为了防止出错，初始化时把指针赋值为nullptr，当我们误用时，编译器会直接报错
	//int* p = nullptr;
	cout<< *p << endl;
	return 0;
}
```

- 悬空指针，指的是开始指向内存已被释放的指针

```
int main(){
	int* p1 = nullptr;
	int* p2 = new int(1);
	p1 = p2;
	delete p2;
	//此时，p1和p2都是悬空指针，指向的内存已被释放，误用该两个指针，会产生不可控的现象。
	//为防止出现上述情况，在释放完内存空间后，手动将指针置为nullptr，当产生误用时，编译器会直接报错。
	//避免野指针比较简单，在C++11后，引入智能指针，能够比较简单容易的避免悬空指针的产生。
	cout<< *p1 << endl;
	return 0;
}
```

> 总结：
>
> 野指针：
>
> - 产生原因：指针变量未及时初始化
> - 解决方法：定义指针变量及时初始化或者置空
>
> <br>悬空指针：
>
> - 产生原因：指针free获得delete后没有及时置空
> - 解决方法：释放操作后立刻置空



# 迭代器失效的情况

## 序列式容器

> 针对的是vector，deque

- `insert()`和`erase()`操作都会使迭代器失效

## 关联式容器

> 针对的是map, set,multimap,multiset

- `insert()`不会使任何迭代器失效
- `erase()`会使指向删除位置的迭代器失效。但是不会使其他迭代器失效

## 链表式容器

> 针对的是list

- `insert()`不会使任何迭代器失效

- `erase()`会使指向删除位置的迭代器失效。但是不会使其他迭代器失效



# C++和C的区别

- C++中的new和delete是对内存分配的运算符，取代了C中的malloc和free
- 标准C++中的字符串类取代了标准C函数库头文件中的字符串处理函数（C中没有字符串类型）
- C++中用来左控制台输入输出的iostream类库取代了标准C中的stdio函数库
- C++中的try/catch/throw异常处理机制取代了标准C中的setjmp()和longjmp()函数
- C++允许有相同的函数名，不过它们的参数个数以及类型不能完全相同以便区分；C语言中不允许。换句话说，C++支持函数重载，C语言不支持函数重载
- C++语言中，允许变量定义语句在程序中的任何地方，只要使用它之前都可以；而C语言必须在函数开头部分
- C++中允许重复定义变量，而C语言做不到这一点
- C++出了值和指针外还新增了引用；C语言中没有这个概念
- C++相对于C新增了一些关键字（例如bool、using、dynamic_cast、namespace）



# C++和Java的区别

- 语言特性

  - Java语言给开发人员提供了更为简洁的语法；完全面向对象，由于JVM可以安装到任何操作系统上，所以它的可移植性非常强

  - Java语言中没有指针的概念，引入了真正的数组。不同于C++中利用指针实现的“伪数组”。同时Java把容易造成麻烦的指针从语言中去掉，这有利于预防C++程序中常见的因为数组操作越界等指针操作而对系统数据进行非法读写带来的不安全问题

    > 指针对于C++是一个优势也是一个劣势：
    >
    > 因为指针的存在可以直接对内存进行操作。
    >
    > 但是在带来便利的同时，也给程序带来的危害性以及复杂性。

  - C++也可以在其他系统上运行，但是需要不同的编码，可移植性性和Java程序还是有差距的（例如：一个数字在windows下是大端存储，在unix中是小端存储。而Java程序生成字节码，在JVM里面运行得到结果）

  - Java用接口（interface）技术取代C++程序中的多继承性。接口和多继承有同样的功能，但是省却了多继承在实现和维护上复杂性。

- 垃圾回收

  - C++用析构函数回收垃圾，所以写C++程序时一定要注意内存的申请和释放
  - Java语言不使用指针，内存的分配和回收都是自动进行的，程序员无需考虑内存碎片的问题

- 应用场景

  - Java在桌面程序上不如C++使用，因为C++可以直接编译成exe文件
  - Java在Web应用上具有C++无可比拟的优势，具有丰富多样的框架
  - 对于底层程序的编程以及控制方面的编程，C++很灵活，因为有句柄的存在



# C++中struct和class的区别

- 相同点
  - 两者都拥有成员函数和公有、私有部分
  - 任何可以在class完成的工作，同样可以使用struct完成
- 不同点
  - struct默认的访问权限是公有，class默认是私有
  - struct默认是public继承，class默认是private继承
  - class关键字可以作为模板类型，struct不行



# C++和C的struct区别

- C语言中的struct是用户自定义数据类型；C++中struct是抽象数据类型
- C语言中的struct没有访问权限的设定，struct只能是多个变量的集合体，可以封装数据却不可以隐藏数据，而且成员不能是函数
- C++中的struct增加了访问权限，可以和类一样有成员函数，成员默认访问权限是public（为了与C兼容）
- C语言中，结构体的声明必须在结构体前加上struct，才能做结构体类型名；而C++可以直接使用结构体名作为结构体类型名



# define和const的区别

- define在编译之前的预处理阶段起作用，而const在编译、运行的时候起作用
- define对代码做文本替代，不做类型检查和计算，也不求解，容易产生错误；而const常量有数据类型，编译器会对其进行类型安全检查
- define只是对宏名称进行替换，在内存中会产生多份相同的备份；const在程序运行时只有一份备份
- define定义的数据不会分配内存空间，而const定义的变量只是值不能改变，但会分配内存空间



# const和static的作用

- static

  - 不考虑类的情况
    - 隐藏。所有不加static的全局变量和函数都具有全局可见性，能够在其他文件中使用，static修饰以后只能在该文件的所有编译模块中使用
    - 默认初始化为0，包括未初始化的全局静态变量和局部静态变量，都存在全局未初始化区
    - 静态变量在函数内定义，始终存在，且只能初始化一次，具有记忆性，其作用范围与局部变量相同，但是函数退出后仍然存在，只是不可以使用而已
  - 考虑类的情况
    - static成员变量
      - 只与类关联，不与类的对象关联
      - 定义时要分配空间，不能在类中声明初始化，必须在类定义体外部初始化，初始化时不需要标记为static
      - 可以被非static成员函数任意访问。
    - static成员函数
      - 不具有this指针
      - 无法访问类对象的非static成员变量和非static成员函数
      - 不能被声明为const、虚函数和volatile
      - 可以被非static成员函数任意访问

- const

  - 不考虑类的情况

    - const常量必须在定义时初始化，之后便无法修改

    - const形参可以接受const和非const类型的实参

      ```
      void func(const int& i){
      	return;
      }
      
      int main(){
      	const int num1 = 1;
      	int num2 = 2;
      	
      	func(num1);
      	func(num2);
      	
      	return 0;
      }
      ```

  - 考虑类的情况

    - const成员变量

      - 不能在类定义外部初始化，只能通过构造函数初始化列表进行初始化，所以必须有构造函数
      - 不同类对const数据成员的数值可以不一样，所以不能在类中声明初始化

    - const成员函数

      - const修饰的成员函数表明该函数不会修改对象的数据成员

      - const对象不可以调用非const成员函数。非const对象则没有限制

      - 不可以改变非mutable关键字修饰的成员变量的值

        > mutable关键字：该关键字声明的变量表示可以在const成员函数中被修改



# 顶层const和底层const

## 概念区分

- 顶层const：表示const修饰的变量本身时一个常量，无法修改
- 底层const：表示const修饰得变量所指向的数据是一个常量

## 举栗

```
int a = 10;
int* const b1 = &a;//顶层const，b1本身是一个常量
const int* b2 = &a;//底层const，b2所指的数据是一个常量
const int b3 = 20;//顶层const，b3是常量不可变
const int* const b4 = &a;//前一个const是底层，后一个const是顶层，b4和b4指向的数据均不可变
const int& b5 = a;//用于声明引用变量，都是底层const。int& b5本质其实就是int* const b5,指向均不能被改变
```

## 区分作用

- 执行对象拷贝时，底层const不能赋值给非底层const

  ```
  int num = 3;
  const int* p1 = &num;
  //int* p2 = p1;  //编译报错，不能将底层const指针赋值给非底层const的指针，如果能修改的话，以后不就可以通过该变量来修改数值了吗？
  const int* p3 = p1;
  ```

- 使用强制类型转换函数`const_cast()`时，需要能够分辨底层const和顶层const，因为`const_cast()`只能修改运算对象的底层const

  ```
  int num = 4;
  const int* p1 = &num;
  //*p1 = 5;  //编译报错，不能改变底层const指针指向的内容
  int* p2 = const_cast<int*>(p1);//正确，const_cast可以改变运算对象的底层const，但是使用时需要知道num不是const修饰
  *p1 = 5;//类型转换以后，非顶层const指针可以改变指向数据的内容了
  ```

- `int const a`和`const int a`均表示定义整型常量a

- `const int* a`，可以看作`const`修饰的是`*a`，`*a`不能改变

- `int* const a`，可以看作`const`修饰的是`a`，`a`不能改变



# 类的对象存储空间

- 非静态成员的数据类型大小之和
- 编译器加入的额外成员变量（例如：指向虚函数表的指针）
- 为了边缘对齐优化加入的padding



# override和final关键字

- override<br>在父类中使用虚函数时，你可能需要在子类中对该虚函数进行重写，以下方面都可以：

  ```
  class A{
  	virtual void func();
  }
  
  class B : public A{
  	void func();//ok
  	virtual void func();//ok
  	void func() override;//ok
  }
  ```

  但是如果不适用overide关键字，当你的突然帕金森病发了，手不自觉抖了一抖，把`func()`写成了`fun()`会发生什么呢？结果编译器并不报错，因为它并不清楚你的目的是重写虚函数还是把它当作一个新函数。如果这个虚函数很重要的话，那就应该对整个程序不利。所以，override的作用就出来了，它指定的子类的这个虚函数就是重写父类的，如果你名字打错了，编译器就会给你报错提醒。

  ```
  class A{
  	virtual void func();
  }
  
  class B : public A{
  	virtual void fun();//ok，这是B新增的函数，并不是继承的
  	void fun() override;//报错，override修饰以后，这个函数必须继承自A，A找不到就报错给你看
  }
  ```

- final<br>当不希望某个类被继承时，或不希望某个虚函数被重写，可以在类名或虚函数后面添加final关键字，添加final关键字后被继承或者重写，编译器会报错。<br>举个例子：

  ```
  class Base{
      virtual void func();
  };
   
  class A : public Base
  {
      void func() final; // foo 被override并且是最后一个override，在其子类中不可以重写
  };
   
  class B final : public A // 指明B是不可以被继承的
  {
      void func() override; // Error: 在A中已经被final了
  };
   
  class C : B // Error: B is final
  {
  };
  ```



# 拷贝初始化和直接初始化

- 当用类类型对象时，初始化的拷贝形式和直接形式有所不同
  - 直接初始化直接调用与实参匹配的构造函数，拷贝构造总是调用拷贝构造函数
  - 拷贝初始化首先使用指定构造函数创建一个临时变量，然后用拷贝构造函数将那个临时对象拷贝到正在创建的对象中。

举个栗子：

```
string str1("I am a string");  //直接初始化
string str2(str1）; //直接初始化，str1是已存在的对象，直接调用拷贝构造函数对str2进行初始化
string str3 = "I am a string"; //拷贝初始化，先将字符串创建一个临时变量，在把临时对象作为参数，使用拷贝构造函数构造str3
string str3 = str1;  //拷贝初始化，这里相当于隐式调用拷贝构造函数，而不是调用赋值运算符函数
```

- 为了提升效率，允许编译器跳过创建临时对象这一步
  - 直接调用构造函数构造要创建的对象
  - 这样就等价于直接初始化，不需要创建临时对象
  - 但是需要辨别两种情况
    - 当拷贝函数为`private`时，使用拷贝初始化会编译报错
    - 使用`explicit`修饰构造函数时，如果构造函数存在隐式转换，编译会报错



# 初始化和赋值的区别

- 对于基本类型来说，初始化和赋值没什么区别

- 对于类和复杂数据类型来说，这两者区别就来了，我们举一下栗子：

  ```
  class A{
  public:
      int num1;
      int num2;
  public:
      A(int a=0, int b=0):num1(a),num2(b){};
      A(const A& a){};
      //重载 = 号操作符函数
      A& operator=(const A& a){
          num1 = a.num1 + 1;
          num2 = a.num2 + 1;
          return *this;
      };
  };
  int main(){
      A a(1,1);
      A a1 = a; //拷贝初始化操作，调用拷贝构造函数
      A b;
      b = a;//赋值操作，对象a中，num1 = 1，num2 = 1；对象b中，num1 = 2，num2 = 2
      return 0;
  }
  ```



# extern”C“用法

- 为了能够在C++代码中正确调用C语言的代码：在程序中加上extern ”C“后，相当于告诉编译器这部分代码是C语言进行编译，而非C++。哪些情况使用extern ”C“：

  - C++代码中调用C语言代码
  - 在C++中的头文件使用
  - 带多个人协同开发，可能有人擅长C语言，而有人擅长C++

  举个栗子：

  ```
  extern "C"{
      typedef unsigned int uint;
      typedef void* void_s;
   
      void_s create_handle(const char* name);
      uint operate_on_handle(void_s handle);
      void close_handle(void_s handle);
  }
  ```

- 在C语言的头文件中，对其外部函数只能指定为extern类型，C语言中不支持extern ”C“声明，在.c文件中包含extern ”C“时会出现编译语法错误。所以使用extern ”C“全部都放在cpp程序相关文件或头文件中。

  - C++调用C函数

  ```
  //cfun.h,头文件声明
  int cfunc();
  
  //cfun.c，c代码中定义
  int cfunc(){
  	return 0;
  }
  
  //xx.cpp，cpp代码中使用
  extern "C"	{
  	#include "cfun.h"
  }
  int main(){
  	cfunc();
  	return 0;
  }
  
  ```

  - C调用C++函数

  ```
  //cppfun.h
  extern "C"{
  	int cppfun();
  }
  
  //cppfun.cpp
  int add(){
  	return 0;
  }
  
  //xx.c
  #include "cfun.h"
  extern int cppfun();
  int main(){
  	cppfun();
  	return 0;
  }
  ```



# 模板函数和模板类的特例化

## 引入原因

编写单一的模板，它能适应多种类型的需求，使每种类型都具有相同的功能，但对于某种特定类型，如果要实现其特有的功能，单一模板就无法做到，这时便需要模板特例化

## 定义

对单一模板提供的特殊实例，它将一个或多个模板参数绑定到特定的类型或值上

## 模板函数特例化

必须为原函数模板中的每个模板参数都提供一个实参，且使用关键字`template<>`表明将原模板的所有模板参数提供实参<br>下面举栗子：

```
template<typename T>
int compare(const T& v1,const T& v2){
	if(v1>v2) return -1;
	if(v2>v1) return 1;
	return 0;
}

template<>
int compare(const char* const &v1,const char* const &v2){
	return strcmp(v1,v2);
}
```

## 类模板特例化

原理类似于函数模板，不是是在类中，我们可以对模板进行特例化，也可以对类进行部分特例。对类进行特例化时，仍然可以用`template<>`表示这是一个特例化版本<br>举个栗子：

```
template <typename T>
class Test{
public:
	void print(){
		cout << "General template object" << endl;
	}
};

template<>   // 对int型特例化
class Test<int>{
public:
	void print(){
		cout << "Specialized template object" << endl;
	}
};
```

### 类模板的部分特例化

不必为所有的模板参数提供实参，可以指定一部分而非所有模板参数，一个类模板的部分特例化本身仍是一个模板，使用它时还必须为其特例化版本中未指定的模板参数提供实参（特例化时类名一定要和原来的模板相同，只是参数类型不同，按最佳匹配原则，哪个最匹配就用相应的模板）<br>举个栗子：

```
 //general template
template<class T1, class T2> 
class Test {
public:
	void print(){
		cout << "General template object" << endl;
	}
}; 

 //specialization with T2 set to int
template<class T1>
class Test<T1, int>  {
public:
	void print(){
		cout << "Specialized template object" << endl;
	}
}; 
```

### 特例化类的部分成员

可以特例化类中的部分成员函数而非整个类<br>举个栗子：

```
template<typename T>
class A
{
    void func();
};
 
template<>
void A<int>::func()
{
    //进行int类型的特例化处理
    cout << "我是int型特例化" << endl;
}
 
A<string> a1;
A<int> a2;//使用特例化
a1.func();//使用的是普通模板，即A<string>::func()
a2.func();//特例化版本，执行A<int>::func()
//A<string>::func()A<int>::func()功能不同
```

## 本质

- 特例化的本质就是实例化一个模板，而非重载它
- 特例化不影响参数匹配。参数匹配都是以最佳匹配为原则
- 举个栗子：compare(3,5)，调用普通的模板，若compare("hello","jiajuu")则调用特例化版本，因为`const char*`相对于T更匹配实参类型

## 注意

模板和特例化版本都应该声明在同一个头文件中，且所有同名模板的声明都应该放在前面，后面放特例化版本



# C和C++的类型安全

## 什么是类型安全

- 类型安全很大程度上可以可以等价于内存安全，类型安全的代码不会试图访问自己没被授权的内存区域

- "类型安全"常被用来形容编程语言，其根据在于该编程语言是否提供保障类型安全的机制
- 有的时候也用”类型安全“形容某个程序，判断的标准在于该程序是否隐含类型错误
- 类型安全的编程语言与类型安全的程序之间没有必然的联系
- 好的程序员可以使用类型不那么安全的语言写出类型相当安全的程序
- 相反的，差的程序员可能使用类型相当安全的语言写出类型不太安全的程序
- 绝对类型安全的编程语言现在还暂时没有出现

## C的类型安全

- C只在局部上下文表现出类型安全

- 栗子：试图从一种结构体的指针转换成另一种结构体的指针时，编译器将会报错，除非使用显示类型转换

- 然而C语言中相当多的操作是不安全的，下面我们再举两个栗子：

  - printf格式输出

    ```
    #include <stdio.h>
    int main(){
    	printf("整型输出：%d\n",10);  //输出10
    	printf("符点型输出：%f\n",10);  //输出0.000000
    	printf("字符型输出：%s\n",10);  //运行时直接报错
    	return 0;
    }
    ```

  - malloc函数的返回值

    - `malloc()`是C中进行内存分配的函数，它的返回类型是`void *`空类型指针
    - 所以我们经常需要这么使用`char* pStr=(char*)malloc(100*sizeof(char))`，我们可以看出明显做了显式的类型转换，类型匹配尚且没有问题
    - 但是如果我们捣蛋地这么使用`int* pInt=(int*)malloc(100*sizeof(char))`，运行时可能会带来意想不到的后果，但这样的操作在C语言中并不会提醒你

## C++的类型安全

如果C++使用得当，将远比C语言更有类型安全性。相比于C语言，C++提供了一些新的机制保障类型安全：

- 操作符`new`返回的指针类型严格与对象匹配，而不是`void*`
- C语言中很多以`void *`为参数的函数可以改写为C++模板函数，而模板是支持类型检查的
- 引入`const`关键字代替`#define constants`，它是有类型、有作用域的，而`#define constants`只是简单的文本替换
- 一些#define宏可被改写为`inline`函数，结合函数的重载，可在类型安全的前提下支持多种类型，当然改写为模板也能保证类型安全
- C++提供了`dynamic_cast`关键字，使得转换过程更加安全，因为`dynamic_cast`比`static_cast`涉及更多具体的类型检查。

> 若程序员使用不得当，也会引起类型不安全的问题，因此想保证程序的类型安全性，应该尽量避免使用空类型指针`void*`和不对两种类型指针做强制转换

举个栗子：

```
//用空类型指针void*
int main(){
	int i = 5;
	void* i_p = &5;
	double d = (*(double*)i_p);
	cout<<d<<endl;//输出1.78416e-307
	return 0;
}
```

还不够，还来一个栗子：

```
class Parent{};
class Child1 : public Parent
{
public:
	int i;
	Child1(int e):i(e){}
};
class Child2 : public Parent
{
public:
	double d;
	Child2(double e):d(e){}
};
int main()
{
	Child1 c1(5);
	Child2 c2(4.1);
	Parent* pp;
	Child1* pc1;
 	
	pp=&c1; //多态常进行的操作，没有问题！
	pc1=(Child1*)pp;  // 类型向下转换 强制转换，由于类型仍然为Child1*，不造成错误
	cout<<pc1->i<<endl; //输出：5
	//child1 -> parent -> child1没问题
 
	pp=&c2;  //多态常进行的操作，没有问题！
	pc1=(Child1*)pp;  //强制转换，且类型发生变化，将造成错误
	cout<<pc1->i<<endl;// 输出：1717986918
	//child12 -> parent -> child1有问题
	
	return 0;
}
```



# 为什么析构函数一般写成虚函数

- 由于类的多态性，基类指针可以指向派生类的对象
- 如果删除该基类的指针，就会调用该指针的派生类析构函数，而派生类的析构函数又自动调用基类的析构函数，这样下来整个派生类才完全被释放
- 如果析构函数不被声明成虚函数，则编译器只会静态绑定，在删除基类指针的时候，只会调用基类的析构函数，而不调用派生类的析构函数，进而造成派生类析构不完全造成内存泄漏。所以将析构函数声明成虚函数是非常必要的
- 在实现多态时，当用基类操作派生类，在析构时基类而不析构派生类的情况发生，要将基类的析构函数声明成虚函数

举个没有写成虚函数的栗子：

```
#include <iostream>
using namespace std;

class Parent{
public:
	Parent(){
		cout << "Parent construct function"  << endl;
	};
	~Parent(){
		cout << "Parent destructor function" <<endl;
	}
};

class Son : public Parent{
public:
	Son(){
		cout << "Son construct function"  << endl;
	};
	~Son(){
		cout << "Son destructor function" <<endl;
	}
};

int main()
{
	Parent* p = new Son();
	delete p;
	p = NULL;
	return 0;
}
//运行结果：
//Parent construct function
//Son construct function
//Parent destructor function
```

举个写成虚函数的栗子：

```
#include <iostream>
using namespace std;

class Parent{
public:
	Parent(){
		cout << "Parent construct function"  << endl;
	};
	virtual ~Parent(){
		cout << "Parent destructor function" <<endl;
	}
};

class Son : public Parent{
public:
	Son(){
		cout << "Son construct function"  << endl;
	};
	~Son(){
		cout << "Son destructor function" <<endl;
	}
};

int main()
{
	Parent* p = new Son();
	delete p;
	p = NULL;
	return 0;
}
//运行结果：
//Parent construct function
//Son construct function
//Son destructor function
//Parent destructor function
```



# virtual能否声明构造和析构函数

- 析构函数
  - 析构函数必须可以声明为虚函数，不然虚析构函数怎么来的，一般情况下基类的析构析构函数都要定义成虚函数
  - 只有在基类析构函数定义为虚函数时，调用操作符`delete`销毁指向对象的基类指针时，才能准确调用派生类的析构函数（从该级向上按序调用虚函数），才能准确销毁数据
  - 而且析构函数可以是纯虚函数，含有纯虚函数的类是抽象类，此时不能被实例化。但派生类可以根据需求重新改写基类的纯虚函数。
- 构造函数
  - 构造函数不能定义为虚函数。因为虚函数需要vptr指针指向虚函数表，而vptr指针需要在构造函数被调用才会初始化，这就变成了一个死锁问题了呀(・∀・(・∀・(・∀・*)
  - 构造函数能够调用虚函数，不过此时调用的虚函数是正在构造的类中的虚函数，而非子类的虚函数，因为此时子类尚未构造好



# 重载、重写、隐藏的区别

- 重载（overload）<br>重载是指同一范围定义的同名成员函数才存在重载关系。主要特点是函数名相同，参数类型和数目有所不同，不能出现参数个数和种类均相同，也不能仅仅依靠返回值不同来区分的函数。重载和函数成员是否是虚函数无关。举个栗子：

  ```
  class A{
  public:
      virtual int fun();
      void fun(int);
      void fun(double, double);
      static int fun(char);
  }
  ```

- 重写（override）<br>重写指的是在派生类中覆盖基类中的同名函数，重写就是重写函数体，而且要求：

  - 基类函数必须是虚函数
  - 重写函数与基类的虚函数有相同的参数个数
  - 重写函数与基类的虚函数有相同的参数类型
  - 重写函数与基类的虚函数有相同的返回值类型

  举个栗子：

  ```
  //父类
  class A{
  public:
      virtual int fun(int a){}
  }
  //子类
  class B : public A{
  public:
      //重写,一般加override可以确保是重写父类的函数
      virtual int fun(int a) override{}
  }
  ```

- 重载和重写的区别：

  - 重载是不同函数之间的水平关系；重写是父类和子类的之间的垂直关系
  - 重载要求参数列表不同，返回值不要求；重写要求参数列表相同
  - 重载根据调用时实参表和形参表的对应关系来选择函数体；重写根据对象类型决定

- 隐藏（hide）<br>隐藏指的是某些情况下，派生类中的函数屏蔽了基类中的同名函数，包括以下情况：

  - 两个函数参数相同，但是基类函数不是虚函数（区别于重写时基类的函数是虚函数），举个栗子：

    ```
    //父类
    class A{
    public:
        void fun(int a){
    		cout << "A中的fun函数" << endl;
    	}
    };
    //子类
    class B : public A{
    public:
        //隐藏父类的fun函数
        void fun(int a){
    		cout << "B中的fun函数" << endl;
    	}
    };
    int main(){
        B b;
        b.fun(2); //调用的是B中的fun函数
        b.A::fun(2); //调用A中fun函数
        return 0;
    }
    ```

  - 两个函数的参数不同，无论基类函数是不是虚函数，都会被隐藏（区别于重载时两个函数在同一个类中），举个栗子：

    ```
    //父类
    class A{
    public:
        virtual void fun(int a){
    		cout << "A中的fun函数" << endl;
    	}
    };
    //子类
    class B : public A{
    public:
        //隐藏父类的fun函数
       virtual void fun(char* a){
    	   cout << "A中的fun函数" << endl;
       }
    };
    int main(){
        B b;
        b.fun(2); //报错，调用的是B中的fun函数，参数类型不对
        b.A::fun(2); //调用A中fun函数
        return 0;
    }
    ```



# C++多态的实现

- 静态多态
  - 函数重载
  - 运算符重载
  - 复用函数名，编译阶段已确定了函数的地址，是编译时绑定，属于早绑定，。也叫编译时多态
- 动态多态（我们常说的多态多指动态多态嗷！）
  - 派生类和虚函数共同实现的运行时多态
  - 运行阶段才确定函数的地址，是运行时绑定，属于晚绑定，也叫运行时多态

> 在基类的函数前加上`virtual`关键字，在派生类中重写该函数，运行时会根据所指对象的实际类型来调用相应的函数，如果对象是派生类，就调用派生类的函数，如果对象类型是基类，就调用基类的函数。

举个栗子吧：

```
#include <iostream>
using namespace std;

class Base{
public:
	virtual void fun(){
		cout << " Base::func()" <<endl;
	}
};

class Son1 : public Base{
public:
	virtual void fun() override{
		cout << " Son1::func()" <<endl;
	}
};

class Son2 : public Base{

};

int main()
{
	Base* base = new Son1;
	base->fun();
	base = new Son2;
	base->fun();
	delete base;
	base = NULL;
	return 0;
}
// 运行结果
// Son1::func()
// Base::func()

```

## 底层原理

- 虚表：虚函数表的缩写，若类含有`virtual`关键字修饰的方法，编译器会全局生成一个虚函数表
- 虚表指针：在含有虚函数的类实例化对象时，对象地址的前四个字节存储的就是指向虚表的指针

- 基类对象的对象模型：

  ![基类对象的对象模型](http://jiajuu.top/2021/03/09/cpp知识点整理第一弹-基础篇/基类对象的对象模型.png)

- 派生类对象的对象模型：

  ![派生类对象的对象模型](http://jiajuu.top/2021/03/09/cpp知识点整理第一弹-基础篇/派生类对象的对象模型.png)

- 实现多态的过程：

  1. 编译器发现基类中含有虚函数时，自动为含有虚函数的类生成一个虚表，该表是一个一维数组，虚表里存储着虚函数的入口地址
  2. 编译器会为每个对象的前四个字节存储了一个虚表指针，即`vptr`，指向对象所属类的虚表<br>在构造时，会根据对象的类型区初始化虚表指针`vptr`，从而让`vptr`指向正确的虚表<br>从而在调用虚函数时，能够找到正确的函数
  3. 在派生类定义对象时，程序运行会自动调用构造函数，在构造函数中创建虚表并对虚表初始化<br>在构造子类对象时，会先调用父类的构造函数<br>此时编译器只”看到了“父类，并为父类对象初始化虚表指针，令其指向父类的虚表<br>当调用子类的构造函数时，为子类对象初始化虚表指针，令其指向子类虚表
  4. 当派生类对基类的虚函数没有重写时，派生类的虚表指针指向的时基类的虚表<br>当派生类对基类的虚函数重写时，派生类的虚表指针指向的是自身的虚表<br>当派生类中有自己的虚函数时，在自己虚表中将此虚函数地址追加到虚表后面。

  > 指向派生类的基类指针在运行时，就能够根据派生类对虚函数重写的情况动态进行调用了，从而实现多态。



# C++的构造函数

- 默认构造函数
- 初始化构造函数（有参数）
- 拷贝构造函数
- 移动构造函数
- 委托构造函数
- 转换构造函数

举个栗子：

```
#include <iostream>
using namespace std;

class Student{
public:
    Student(){//默认构造函数，没有参数
        this->age = 20;
        this->num = 1000;
    };  
    Student(int a, int n):age(a), num(n){}; //初始化构造函数，有参数和参数列表
    Student(const Student& s){//拷贝构造函数，这里与编译器生成的一致
        this->age = s.age;
        this->num = s.num;
    }; 
    //一个构造函数接收一个不同于其类类型的形参，可以视为将其形参转换成类的一个对象。
    Student(int r){   //转换构造函数,形参是其他类型变量，且只有一个形参
        this->age = r;
		this->num = 1002;
    };
    ~Student(){}
public:
    int age;
    int num;
};

int main(){
    Student s1;
    Student s2(18,1001);
    int a = 10;
    Student s3(a);
    Student s4(s3);
    
    printf("s1 age:%d, num:%d\n", s1.age, s1.num);
    printf("s2 age:%d, num:%d\n", s2.age, s2.num);
    printf("s3 age:%d, num:%d\n", s3.age, s3.num);
    printf("s2 age:%d, num:%d\n", s4.age, s4.num);
    return 0;
}
//运行结果
//s1 age:20, num:1000
//s2 age:18, num:1001
//s3 age:10, num:1002
//s2 age:10, num:1002
```

> - 默认构造函数和初始化构造函数在定义类的对象，完成对象的初始化工作
> - 复制构造函数用于复制本类的对象
> - 转换构造函数用于将其他类型的变量，隐式转换为本类对象



# 深拷贝和浅拷贝

- 浅拷贝

浅拷贝只是拷贝一个指针，并没有新开辟一个地址，拷贝的指针和原来的指针指向同一块地址，如果原来的指针所指向的资源释放了，那么再释放浅拷贝的指针的资源就会出现错误。

- 深拷贝

深拷贝不仅拷贝值，还开辟出一块新的空间用来存放新的值，即使原先的对象被析构掉，释放内存了也不会影响到深拷贝得到的值。在自己实现拷贝赋值的时候，如果有指针变量的话是需要自己实现深拷贝的。

- 举个栗子：

```
#include <iostream>  
#include <string.h>
using namespace std;
 
class Student
{
private:
	int num;
	char *name;
public:
	Student(){
        name = new char(20);
		cout << "Student" << endl;
    };
	~Student(){
        cout << "~Student " << &name << endl;
        delete name;
        name = NULL;
    };
	Student(const Student &s){//拷贝构造函数
        //浅拷贝，当对象的name和传入对象的name指向相同的地址
        name = s.name;
        //深拷贝
        //name = new char(20);
        //memcpy(name, s.name, strlen(s.name));
        cout << "copy Student" << endl;
    };
};
 
int main()
{
	{// 花括号让s1和s2变成局部对象，方便测试
		Student s1;
		Student s2(s1);// 复制对象
	}
	system("pause");
	return 0;
}
//浅拷贝执行结果：
//Student
//copy Student
//~Student 0x7fffed0c3ec0
//~Student 0x7fffed0c3ed0
//*** Error in `/tmp/815453382/a.out': double free or corruption (fasttop): 0x0000000001c82c20 ***

//深拷贝执行结果：
//Student
//copy Student
//~Student 0x7fffebca9fb0
//~Student 0x7fffebca9fc0
```

> 从执行结果可以看出，浅拷贝在对象的拷贝创建时存在风险，即被拷贝的对象析构释放资源之后，拷贝对象析构时会再次释放一个已经释放的资源，深拷贝的结果是两个对象之间没有任何关系，各自成员地址不同。



# 内联函数和宏定义

## 内联函数介绍

内联(inline)函数和普通函数相比可以加快程序运行的速度，因为不需要中断调用，在编译的时候内联函数可以直接嵌入到目标代码中。

## 内联函数适用场景

- 使用宏定义的地方都可以使用内联函数
- 作为类成员接口函数来读写类的私有成员或者保护成员，会提高效率

## 为什么不能把所有函数都写成内联函数

- 内联函数以代码复杂为代价，是空间换时间的手段，它以省去函数调用的开销来提高执行效率。
- 所以一方面如果内联函数体内代码执行时间相比函数调用开销较大，则没有太大的意义了
- 另外一方面，每一处内联函数的调用都要复制代码，消耗更过的内存空间

以下情况不适宜使用内联函数：

- 函数体内的代码较长，将导致内存消耗代价
- 函数体内有循环，函数执行时间要比函数调用开销大

## 内联函数和宏定义区别

- 内联函数在编译时展开；宏在预编译时展开
- 内联函数直接嵌入到目标代码中；宏是简单的做文本替换
- 内联函数有参数类型检测、语法判断等功能，且具有返回值；而宏没有
- 内联函数是函数，强调函数特性，具有重载等功能；宏不是函数
- 宏定义时要注意书写（参数要括起来）否则会出现歧义，内联函数不会产生歧义
- 内联函数代码是被放在符号表中，使用时像宏一样展开，没有调用的开销，效率很高
- 内联函数可以作为某个类的成员函数，这样可以使用类的保护成员和私有成员，进而提效率；而当一个表达式涉及到类保护成员或私有成员时，宏就不能实现了

## 构造函数、析构函数、虚函数可否声明为内联函数

首先，需要表明的是将这些函数声明成内联函数，在语法上没有错误。因为inline同register一样，只是个建议，编译器并不一定真正的内联操作。

> register关键字：请求编译器尽可能的将变量存在CPU内部寄存器上，而不是通过内存寻址访问，以提高访问效率

举个栗子：

```
#include <iostream>
using namespace std;
class A
{
public:
    inline A() {
		cout << "inline construct()" <<endl;
	}
    inline ~A() {
		cout << "inline destruct()" <<endl;
	}
    inline virtual void  virtualFun() {
		cout << "inline virtual function" <<endl;
	}
};
 
int main()
{
	A a;
	a.virtualFun();
    return 0;
}
//输出结果
//inline construct()
//inline virtual function
//inline destruct()
```

- 构造函数和析构函数声明为内联函数并没有意义

《Effective C++》中阐述到：**将构造函数和析构函数声明为inline是没有什么意义的，即编译器并不真正对函数声明为inline的构造和析构函数进行内联操作，因为编译器会在构造和析构函数中添加额外的操作（申请/释放内存，构造/析构对象），导致构造函数/析构函数并不像看上去的那么精简。**其次，class中函数默认是inline型的，编译器也只是有选择性的inline，将构造函数和析构函数声明为内联函数没有什么意义。

- 虚函数声明为inline，需要分情况讨论

有的人认为虚函数被声明为inline，但是编译器并没有对其内联，它们给出的理由是inline是编译时期决定的，但虚函数是运行期才决定的，所以编译时期并不知道运行时要调用哪条函数的情况下，虚函数声明inline并没有意义。

看似有点道理，其实并不全面。如果虚函数在编译器编译时期就能决定调用哪条函数的话，那么就能内联了，那什么情况下编译器在编译时期就能确定调用哪条函数呢？答案就是当用对象的指针指向本身调用虚函数（此时并不具有多态）时便可以内联展开

综上所述，得到的结论：

1. 当指向派生类的指针（具有多态性）调用声明为inline的虚函数，并不会内联展开
2. 当指向本身类的指针（不具有多态性）调用声明inline的虚函数，可以内联展开，不过在这个函数并不复杂的前提下



# auto、decltype和decltype(auto)

- auto

C++11标准引入的`auto`类型说明符，用它就能让编译器替我们去分析表达式所属的类型。和原来那些只对应某种特定的类型说明符不同，<b>`auto`让编译器通过初始值来进行类型推演。从而获得定义变量的类型，所以说`auto`定义的变量必须有初始值。</b>

举个栗子：

```
//普通；类型
int a = 1, b = 3;
auto c = a + b;// c为int型

//const类型
const int i = 5;
auto j = i; // 变量i是顶层const, 会被忽略, 所以j的类型是int
auto k = &i; // 变量i是一个常量, 对常量取地址是一种底层const, 所以b的类型是const int*
const auto l = i; //如果希望推断出的类型是顶层const的, 那么就需要在auto前面加上cosnt

//引用和指针类型
int x = 2;
int& y = x;
auto z = y; //z是int型不是int& 型
auto& p1 = y; //p1是int&型
auto p2 = &x; //p2是指针类型int*
```

- decltype

有的时候我们还会遇到这种情况，<b>我们希望从表达式中推断出要定义变量的类型，但却不想用表达式的值去初始化变量。</b>还有<b>可能是函数的返回类型是某表达式的值类型。</b>在这个时候`auto`就显得苍白无力，所以C++11又引入第二种类型说明`decltype`，<b>它可以选择并返回操作数的数据类型。在此过程中，编译器只是分析表达式并得到它的类型，却不进行实际的计算表达式的值</b>

举个栗子：

```
int func() {return 0};

//普通类型
decltype(func()) sum = 5; // sum的类型是函数func()的返回值的类型int, 但是这时不会实际调用函数func()
int a = 0;
decltype(a) b = 4; // a的类型是int, 所以b的类型也是int

//不论是顶层const还是底层const, decltype都会保留   
const int c = 3;
decltype(c) d = c; // d的类型和c是一样的, 都是顶层const
int e = 4;
const int* f = &e; // f是底层const
decltype(f) g = f; // g也是底层const

//引用与指针类型
//1. 如果表达式是引用类型, 那么decltype的类型也是引用
const int i = 3, &j = i;
decltype(j) k = 5; // k的类型是 const int&

//2. 如果表达式是引用类型, 但是想要得到这个引用所指向的类型, 需要修改表达式:
int i = 3, &r = i;
decltype(r + 0) t = 5; // 此时是int类型

//3. 对指针的解引用操作返回的是引用类型
int i = 3, j = 6, *p = &i;
decltype(*p) c = j; // c是int&类型, c和j绑定在一起

//4. 如果一个表达式的类型不是引用, 但是我们需要推断出引用, 那么可以加上一对括号, 就变成了引用类型了
int i = 3;
decltype((i)) j = i; // 此时j的类型是int&类型, j和i绑定在了一起
```

- decltype(auto)

`decltype(auto)`是C++14新增的类型指示符，可以用来声明变量以及指示函数返回类型。在使用时，又将等号左边的表达式替换掉`auto`，再根据`decltype`的语法规则来确定类型。

举个栗子：

```
int e = 4;
const int* f = &e; // f是底层const
decltype(auto) j = f;//j的类型是const int* 并且指向的是e
```



# 访问权限与继承权限

## 访问权限

- `public`修饰的变量和函数在类的内部外部都可以访问。
- `protected`修饰的变量和函数只能在类的内部和其派生类中访问。
- `private`修饰的元素只能在类内访问。

派生类可以继承基类中除了构造/析构、赋值运算符重载函数之外的成员，但是这些成员的访问属性在派生过程中也是可以调整的。

![访问权限](http://jiajuu.top/2021/03/09/cpp知识点整理第一弹-基础篇/访问权限.png)

> 注意：外部访问并不是真正的外部访问，而是在通过派生类的对象对基类成员的访问。
>
> - 内部访问：由派生类中新增的成员函数对从基类继承来的成员的访问
> - 外部访问：在派生类外部，通过派生类的对象对从基类继承来的成员的访问

## 继承权限

- public继承

公有继承的特点是基类的公有成员和保护成员作为派生类的成员时，都保持原有的状态，而基类的私有成员任然是私有的，不能被这个派生类的子类所访问

- protected继承

保护继承的特点是基类的所有公有成员和保护成员都成为派生类的保护成员，并且只能被它的派生类成员函数或友元函数访问，基类的私有成员仍然是私有的

- private继承

私有继承的特点是基类的所有公有成员和保护成员都成为派生类的私有成员，并不被它的派生类的子类所访问，基类的成员只能由自己派生类访问，无法再往下继承



# 判断大小端存储

- 大端存储：字数据的高字节存储在低地址
- 小段存储：字数据的低字节存储在低地址

举个栗子：

我们有一个32位的数值——0x12345678

小端模式中的存储方式为：

![小端模式存储方式](http://jiajuu.top/2021/03/09/cpp知识点整理第一弹-基础篇/小端模式存储方式.png)

大端模式中的存储方式为：

![大端模式存储方式](http://jiajuu.top/2021/03/09/cpp知识点整理第一弹-基础篇/大端模式存储方式.png)

判断方式：

1. 使用强制类型转换

```
#include <iostream>
using namespace std;
int main()
{
    int a = 0x1234;
    //由于int和char的长度不同，借助int型转换成char型，只会留下低地址的部分
    char c = (char)(a);
    if (c == 0x12)
        cout << "big endian" << endl;
    else if(c == 0x34)
        cout << "little endian" << endl;
}
```

2. 使用union联合体

```
#include <iostream>
using namespace std;
//union联合体的重叠式存储，endian联合体占用内存的空间为每个成员字节长度的最大值
union endian
{
    int a;
    char ch;
};
int main()
{
    endian value;
    value.a = 0x1234;
    //a和ch共用4字节的内存空间
    if (value.ch == 0x12)
        cout << "big endian"<<endl;
    else if (value.ch == 0x34)
        cout << "little endian"<<endl;
}
```

# volatile、mutable和explicit关键字

## volatile

- `volatile`关键字是一种类型修饰符，<b>如果不用它声明的类型变量可以被某些编译器未知的元素更改</b>，例如：操作系统、硬件或者其他线程。如果变量经过`volatile`关键字修饰后，编译器对访问该变量的代码就不再进行优化，从而可以提供对特殊地址的稳定访问。

- 当使用`volatile`关键字声明变量的值时，<b>系统总是重新从它所在的内存读取数据</b>，即便它前面的指令刚刚从该处读取过数据

- <b>`volatile`关键字修饰的变量的值是易变的，每次用到这个变量的值时都要去内存重新读取该变量的值，而不是直接读寄存器内的备份。多线程中被几个任务共享的变量需要需要被修饰为`volatile`</b>

- volatile指针

  - volatile指针和const修饰词类似，const有常量指针和指针常量的说法，volatile也有类似的概率

  - 修饰指针指向的对象、数组：

    ```
    const char* cp;
    volatile char* vp;
    ```

  - 修饰指针自身

    ```
    char* const cp;
    char* volatile vp;
    ```

  > 注意：
  >
  > 1. 可以把非volatile int赋值给volatile int，但是不能把非volatile对象赋给一个volatile对象。
  > 2. 除了基本类型外，对用户定义类型也可以用volatile类型进行修饰。
  > 3. C++中有volatile标识符的类只能访问它接口的子集，一个由类的实现者控制的子集。用户只能用const_cast来获取对类型接口的完全访问。此外，volatile向const一样会从类传递到它的成员。

- 多线程下的volatile

  部分变量使用volatile关键字声明的。当两个线程都要用到某个变量且该变量会被改变时，应该用volatile声明，<b>该关键字的作用是预防编译器把变量优化，从内存装入CPU寄存器中。</b>如果变量被装入寄存器，那么两个线程有可能一个使用内存中的变量，一个使用寄存器的变量，这将造成程序的错误执行。<b>volatile的作用便是让编译器每次操作该变量都一定从内存中取出，而不是使用已经存在寄存器中的值。</b>

## mutable

- `mutable`关键字和`const`关键字的意思正好相反，表示”可变的“
- 在C++中，`mutable`关键字是为了突破`const`的限制而设置的。被`mutable`关键字修饰的变量，将永远处于可变的状态，即使在一个const函数中
- 我们知道，如果类的成员函数不会改变对象的状态，那么这个成员函数一般会声明`const`
- 但有时候我们需要在`const`修饰的函数中修改一些和类状态的数据成员，那么这个就应该被`mutable`来修饰。

## explicit

`explicit`关键字用来修饰类的构造函数，被修饰构造函数的类，不能发生相应的隐式类型转换，只能以显式方式进行类型转换

注意：

- `explicit`关键字只能用于类内部的构造函数声明上
- `explicit`关键字作用域单个参数的构造函数
- 被`explicit`修饰的构造函数的类，不能发生相应的隐式类型转换



# 调用拷贝构造函数的情况

- 用类的实例化对象去初始化类的另一个对象

- 函数的参数是类的对象时（非引用传递）

- 函数的返回值是函数体内局部对象时，由于返回方式是值传递，所以会在返回值的地方调用拷贝构造函数

  举个栗子：

  ```
  class A
  {
  public:
  	A() {};
  	A(const A& a)
  	{
  		cout << "copy constructor is called" << endl;
  	};
  	~A() {};
  };
  
  void useClassA(A a) {}
  
  A getClassA()//此时会发生拷贝构造函数的调用，虽然发生NRV优化，但是依然调用拷贝构造函数
  {
  	A a;
  	return a;
  }
  
  
  //A& getClassA2()  //VS2019下，此时编辑器会进行（Named return Value优化）NRV优化,不调用拷贝构造函数 ，如果是引用传递的方式返回当前函数体内生成的对象时，并不发生拷贝构造函数的调用
  //{
  //	A a;
  //	return a;
  //}
  
  
  int main()
  {
  	A a1, a2,a3,a4;
  	A a2 = a1;  //调用拷贝构造函数,对应情况1
  	useClassA(a1);//调用拷贝构造函数，对应情况2
  	a3 = getClassA();//发生NRV优化，但是值返回，依然会有拷贝构造函数的调用 情况3
  	a4 = getClassA2(a1);//发生NRV优化，且引用返回自身，不会调用拷贝构造函数
      return 0;
  }
  ```

  >1. 情况1就直接调用拷贝构造函数
  >2. 情况2的过程是调用函数时根据参入的实参生成一个临时对象，再用拷贝构造函数去初始化这个临时对象，函数调用结束后析构临时对象
  >3. 情况3在执行return时，会首先生成一个临时对象，调用拷贝构造函数把返回对象拷贝给临时对象，函数执行完先析构局部变量，在析构临时变量，依然会调用拷贝构造函数



# new的不同类型

- plain new
- nothrow new
- placement new

## plain new

其实就是我们常用的new，我们最普通的new

```
void* operator new(std::size_t) throw(std::bad_alloc);
void operator delete(void *) throw();
```

因此plain new在空间分配失败的情况下，会抛出std::bad_alloc异常，而非返回NULL，因此不能通过返回值是否为NULL来判断是否分配成功，我们举个栗子：

```
#include <iostream>
#include <string>
using namespace std;
int main()
{
	try
	{
		char *p = new char[10e11];
		delete p;
	}
	catch (const std::bad_alloc &ex)
	{
		cout << ex.what() << endl;
	}
	return 0;
}
//执行结果：bad allocation
```

## nothrow new

nothrow new在空间分配失败时不会抛出异常，而是返回NULL

```
void * operator new(std::size_t,const std::nothrow_t&) throw();
void operator delete(void*) throw();
```

所以可以通过返回值是否为NULL来判断是否分配成功，我们举个栗子：

```
#include <iostream>
#include <string>
using namespace std;

int main()
{
	char *p = new(nothrow) char[10e11];
	if (p == NULL) 
	{
		cout << "alloc failed" << endl;
	}
	delete p;
	return 0;
}
//运行结果：alloc failed
```

## placement new

允许在一块已经分配成功的内存上重新构造对象或对象数组。placement new不用担心内存分配失败，因为它根本不分配内存，它做的唯一一件事情就是调用对象的构造函数。

```
void* operator new(size_t,void*);
void operator delete(void*,void*);
```

注意：

- 主要作用就是反复使用一块较大的动态分配的内存来构造不同类型的对象或者他们的数组
- placement new构造起来的对象数组，要显式的调用他们的析构函数来销毁（析构函数并不释放对象的内存），千万不要使用delete，这是因为placement new构造起来的对象或数组大小并不一定等于原来分配的内存大小，使用delete会造成内存泄漏或者之后释放内存时出现运行时错误。

举个栗子：

```
#include <iostream>
#include <string>
using namespace std;
class ADT{
	int i;
	int j;
public:
	ADT(){
		i = 10;
		j = 100;
		cout << "ADT construct i=" << i << "j="<<j <<endl;
	}
	~ADT(){
		cout << "ADT destruct" << endl;
	}
};
int main()
{
	char *p = new(nothrow) char[sizeof ADT + 1];
	if (p == NULL) {
		cout << "alloc failed" << endl;
	}
	ADT *q = new(p) ADT;  //placement new:不必担心失败，只要p所指对象的的空间足够ADT创建即可
	//delete q;//错误!不能在此处调用delete q;
	q->ADT::~ADT();//显示调用析构函数
	delete[] p;
	return 0;
}
//输出结果：
//ADT construct i=10j=100
//ADT destruct
```





# NULL和nullptr的区别

- NULL来自于C语言，一般由宏定义实现，而nullptr则是C++11新增的关键字
- C语言中，NULL被定义为(void*)0，而在C++中NULL被定义为整型0

> 可能有同学会问：为什么C++不能把NULL也定义成(void*)0呢？
>
> <br>首先我们了解背景故事：
>
> 1. 在C和C++中，特定指针可以赋值给通用指针，用于临时储存地址，但缺点是：
>    1. 因为没有类型，无法确定内存发小，所以不能解引用
>    2. 无法调整权重，所以不能进行算数运算（例如：p++）
> 2. 在C中，通用指针可以赋值给特定指针；但在C++中通用指针不能赋值给特定指定，这是给了杜绝不同类型的指针相互赋值
>
> 看完背景故事我们大概就清楚了！
>
> <br>答：我们假如C++NULL也是(void*)0，则`int* p = NULL`相当与`int* p = (void*) 0`，但是这会在C++中编译是无法通过的，因为C++中通用指针不可以赋值给特定指针，所以为了兼容C语言，NULL被规定为0；

```
#ifdef __cplusplus
#define NULL 0
#else
#define NULL ((void *)0)
#endif
```

- 在C++中指针必须有明确的类型定义，因为C++允许有函数重载的特性，所以将NULL定义为整型0会带来一个无法与整数0区分的问题。举个栗子：

```
#include <iostream>
using namespace std;

void fun(char* p) {
	cout << "char*" << endl;
}

void fun(int p) {
	cout << "int" << endl;
}

int main()
{
	fun(NULL);
	return 0;
}
//输出结果：int
```

- 为了解决这个问题，C++11引入了nullptr关键字，nullptr可以明确区分整型和指针类型，能够根据环境自动转换成相应的指针类型，但不会被转换为任何整型，所以不会造成参数传递错误。nullptr的实现方式：

```
const class nullptr_t{
public:
    template<class T>  inline operator T*() const{ return 0; }
    template<class C, class T> inline operator T C::*() const { return 0; }
private:
    void operator&() const;
} nullptr = {};
```

> 通过模板类和运算符重载对不同类型的指针进行实例化，从而解决了(void*)指针带来参数类型不明的问题<br>且由于nullptr明确了指针类型，所以不会与整型变量相混淆

- 但是nullptr仍然存在一定的问题，我们再举出一个栗子：

```
#include <iostream>
using namespace std;

void fun(char* p)
{
	cout<< "char* p" <<endl;
}
void fun(int* p)
{
	cout<< "int* p" <<endl;
}

void fun(int p)
{
	cout<< "int p" <<endl;
}
int main()
{
    fun((char*)nullptr);//语句1
	fun(nullptr);//语句2
    fun(NULL);//语句3
    return 0;
}
//运行结果：
//语句1：char* p
//语句2:报错，有多个匹配
//3：int p
```

>在这种情况下存在对不同指针类型的函数重载，此时如果传入nullptr指针则仍然存在无法区分应实际调用哪个函数，这种情况下必须显示的指明参数类型。



# C++内存分区

- 堆

在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束时，这些存储单元自动被释放，栈内存分配运行内置于处理器的指令集中，所以效率很高，但分配的内存容量有限

- 栈

就是那些由new分配的内存块，编译器不需要管这块内存块的释放，由程序员手动释放，所以一般一个new便对应一个delete。如果程序员没有释放，那么在程序结束后，操作系统会自动回收

- 全局/静态存储区

全局变量和静态变量被分配到同一块内存中，在以前的C语言中，全局变量和静态变量又分为初始化的和未初始化的，在C++里面没有这个区分，它们共同占用同一块内存区，在该区定义的变量若没有初始化也会被自动初始化（例如：int型会被初始化为0）。

- 常量存储区

这是一块比较特殊的存储区，里面存放着常量，不允许修改

- 代码区

存放函数体的二进制代码

![内存分区](http://jiajuu.top/2021/03/09/cpp知识点整理第一弹-基础篇/内存分区.png)



# 异常处理的方法

## try、throw和catch关键字

- 程序的执行流程是先执行try包裹的语句块
- 如果执行过程中没有异常发生，则不会进入任何catch包裹的语句块
- 如果发生异常，则使用throw进行异常抛出，再由catch进行捕获
- throw可以抛出各种数据类型的信息，代码中使用的是数字，也可以自定义异常class
- <b>catch根据throw抛出的数据类型进行精确捕获（不会出现类型转换），如果匹配不到就直接报错，可以使用catch(...)的方式捕获任何异常（不推荐）</b>
- 如果catch了异常，当前函数如果不进行处理，或者已经处理了想通知上一层的调用者，可以在catch里面再throw异常。

我们举一个主要用法的栗子：

```
#include <iostream>
using namespace std;
int main()
{
    double m = 1, n = 0;
    try {
        cout << "before dividing." << endl;
        if (n == 0)
            throw - 1;  //抛出int型异常
        else if (m == 0)
            throw - 1.0;  //拋出 double 型异常
        else
            cout << m / n << endl;
        cout << "after dividing." << endl;
    }
    catch (double d) {
        cout << "catch (double)" << d << endl;
    }
    catch (...) {
        cout << "catch (...)" << endl;
    }
    cout << "finished" << endl;
    return 0;
}
//运行结果
//before dividing.
//catch (...)
//finished
```

## 函数的异常声明列表

有时候，程序员在定义函数的时候知道函数可能发生的异常，可以在函数声明和定义时，指出所能抛出异常的列表，举栗：

```
int fun() throw(int,double,A,B,C){...};
```

这种写法表明函数可能会抛出int,double型或者A、B、C三种类型的异常，如果throw中为空，表明不会抛出任何异常，如果没有throw则可能抛出任何异常

## C++标准异常类

C++ 标准库中有一些类代表异常，这些类都是从 exception 类派生而来的

![标准异常类](http://jiajuu.top/2021/03/09/cpp知识点整理第一弹-基础篇/标准异常类.jpg)

- bad_typeid

使用typeid运算符，如果其操作数是一个多态类的指针，而该指针的值为 NULL，则会拋出此异常，我们拿出栗子：

```
#include <iostream>
#include <typeinfo>
using namespace std;

class A{
public:
  virtual ~A();
};
 
using namespace std;
int main() {
	A* a = NULL;
	try {
  		cout << typeid(*a).name() << endl; // Error condition
  	}
	catch (bad_typeid){
  		cout << "Object is NULL" << endl;
  	}
    return 0;
}
//运行结果：bject is NULL
```

- bad_cast

在用 dynamic_cast 进行从多态基类对象（或引用）到派生类的引用的强制类型转换时，如果转换是不安全的，则会拋出此异常，拿出栗子：

```
#include <typeinfo.h>
#include <iostream>
 
class Shape {
public:
   virtual void virtualfunc() const {}
};
 
class Circle: public Shape {
public:
   virtual void virtualfunc() const {}
};
 
using namespace std;
int main() {
   Shape shape_instance;
   Shape& ref_shape = shape_instance;
   try {
      Circle& ref_circle = dynamic_cast<Circle&>(ref_shape); 
   }
   catch (bad_cast b) {
      cout << "Caught: " << b.what();
   }
}
```

- bad_alloc

在用 new 运算符进行动态内存分配时，如果没有足够的内存，则会引发此异常

- out_of_range

用 vector 或 string的at 成员函数根据下标访问元素时，如果下标越界，则会拋出此异常