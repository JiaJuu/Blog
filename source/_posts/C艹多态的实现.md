---
title: C++多态的实现
date: 2021-03-30 14:38:22
tags: C++
categories: C++
cover: https://jiajuu.top/2021/03/30/C艹多态的实现/C艹多态的实现.jpg
---

> 昨天被面试官的一个问题面懵逼了(((φ(◎ロ◎;)φ)))！！
>
> 大概的问题就是如果不用指针或者引用，那么还能实现多态吗？
>
> 平时都是使用指针的方式实现多态，真没注意这些小细节，所以一被细问就直接凉透了。
>
> 这也怪不得谁，只能怪自己的确是有些菜，所以只能记录一波。

我们平时都是用指针的方式实现多态的。按照惯例，我们举个最最最简单的栗子：

```
#include<iostream>
using namespace std;
class A{
public:
	virtual void func(){
		cout<<"这是A Class"<<endl;
	}
};
class B  : public B{
public:
	virtual void func(){
		cout<<"这是B Class"<<endl;
	}
};
int main(){
	B b;
	A* a1 = new B();
	A* a2 = b;
	a1->func();
	a2->func();
	return 0;
}
//输出：这是B Class
```

我们也可以使用引用来实现多态。我们继续举栗子：

```
#include<iostream>
using namespace std;
class A{
public:
	virtual void func(){
		cout<<"这是A Class"<<endl;
	}
};
class B  : public B{
public:
	virtual void func(){
		cout<<"这是B Class"<<endl;
	}
};
int main(){
	B b;
	A& a = b;
	a.func();
	return 0;
}
//输出：这是B Class
```

> 上面两种方式都理所当然的能实现多态。

但是如果我们不用指针也不用引用呢？会肾么事呢？

```
#include<iostream>
using namespace std;
class A{
public:
	virtual void func(){
		cout<<"这是A Class"<<endl;
	}
};
class B  : public B{
public:
	virtual void func(){
		cout<<"这是B Class"<<endl;
	}
};
int main(){
	B b;
	A a = b;
	a.func();
	return 0;
}
//输出：这是A Class
```

> 这是为肾么呢？
>
> 面试完后我自己仔细回想了一番，给一个解释说服自己。
>
> 答：
>
> 1. 因为引用`A& a` 的本质其实也是`A* const a`，所以我们可以把指针和引用都当作指针。
> 2. 指针的赋值其实就是把指向内存中的地址，不涉及拷贝构造函数和`=`运算符重载函数
> 3. 但是如果我们不使用指针和引用 ，我们直接使用赋值，那么这个过程会调用拷贝构造函数或者`=`运算符重载函数
> 4. 相当于以子类对象来初始化父类对象，这过程并不会改变虚指针`vptr`的指向。

上面仅仅是我个人的看法，所以并不保证正确。所以我又找度娘找了找权威的解释：

《深度探索C++对象模型》：

>"一个pointer或一个reference之所以支持多态，是因为它们并不引发内存任何“与类型有关的内存委托操作； 会受到改变的。只有它们所指向内存的大小和解释方式而已"
>
>
>
>对这句话的解释就是:
>
>- 指针和引用类型只是要求了基地址和这种指针所指对象的内存大小，与对象的类型无关，相当于把指向的内存解释成指针或引用的类型。
>- 而把一个派生类对象直接赋值给基类对象，就牵扯到对象的类型问题，编译器就会回避之前的的虚机制。从而无法实现多态。
>
>转载自[LupinLeo大佬的CSDN博客](https://blog.csdn.net/shichao1470/article/details/89893508)



这个博文后续我还会记录C++多态的底层原理...未完待续...