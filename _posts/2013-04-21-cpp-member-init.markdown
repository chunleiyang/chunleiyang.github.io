---
layout: post
title: "C++成员初始化"
categories: [Programming]
tags: [cpp]
---


&emsp;&emsp;我们知道在C++里，对象的构造是有构造函数完成的，一般的，我们会在构造函数体内完成成员的初始化工作，像下面这样：  

{% highlight cpp  %}
class X {
    public:
        X(int val) {
            i = val;
            j = i;
        }
    public:
        int i;
        int j;
};
{% endhighlight %}

&emsp;&emsp;这是比较简单的一种情况，也是最常用的一种方式，因为它没有涉及到有虚函数以及继承链里有虚基类的情况。在有虚函数的情况下，一般是编译器会在构造函数中（由编译器合成或者开发者提供）安插vptr指针，并妥当设置好它的值，使得它指向该类的虚函数表，虚函数表也是在编译期间完成的，这里不再展开细说。  
  
&emsp;&emsp;构造函数的另外一种语法就是本文的主题：*成员初始化列表* 。像下面这样：  

{% highlight cpp  %}
class X {
    public:
        X(int val) : i(val), j(i) {}
    public:
        int i;
        int j;
};
{% endhighlight %}
  
    
&emsp;&emsp;成员初始化列表并不是一种函数调用，初始化成员的次序非常讲究，编译器真正初始化成员的顺序是它们在类中申明的次序，而并不是列在成员初始化列表里的顺序，上面的示例没有任何问题，咱们再来看另外一种情况，你会得到一个怪异的结果：

{% highlight cpp  %}
class X {
    public:
        X(int val) j(i), (val) {}
    public:
        int i;
        int j;
};
{% endhighlight %}

  
&emsp;&emsp;看出区别了吗？成员初始化列表里面的次序发生了变化，我们来简单的测试一下到底有什么不同：

{% highlight cpp  %}
#include <iostream>
    
using std::cout;
using std::endl;

class X {
    public:
        X(int val):i(val),j(i){}
    public:
        int i;
        int j;
};
    
int main() {
    X x(100);
    cout << x.i << endl << x.j << endl;
    return 0;
}
{% endhighlight %}

&emsp;&emsp;输出结果：

{% highlight cpp %}
100
100
{% endhighlight %}

&emsp;&emsp; 再来看后一种情况：  

{% highlight cpp  %}
#include <iostream>
    
using std::cout;
using std::endl;
    
class X {
    public:
        X(int val):j(val),i(j){}
    public:
        int i;
        int j;
};
    
int main() {
    X x(100);
    cout << x.i << endl << x.j << endl;
    return 0;
}
{% endhighlight %}

&emsp;&emsp; 输出结果：

{% highlight cpp %}
10653684
100
{% endhighlight %}

&emsp;&emsp;显然这不是我们想要的结果，这是因为，编译器是按照申明次序来初始化的，所以先初始化i，这时候j是未初始化过的，因此得到的是脏数据。gcc／g++能对这种情况给出warning，像下面这样：

{% highlight cpp %}
g++ -c -Wall -O3 hello.cpp -o hello.o
hello.cpp: In constructor 'X::X(int)':
hello.cpp:12: warning: 'X::j' will be initialized after
hello.cpp:11: warning:   'int X::i'
hello.cpp:9: warning:   when initialized here
g++ -o hello hello.o
{% endhighlight %}

&emsp;&emsp;如果没有对成员初始化列表有这样的理解，这样的警告会让人很困惑，另外，下面四种情况下是必须要用成员初始化列表的语法来初始化成员的：

1. 初始化一个reference member
2. 初始化一个const member
3. 调用一个base class的构造函数，而它拥有一组参数
4. 调用一个member class的构造函数，而它拥有一组参数
  
&emsp;&emsp;最后一点，当成员都是基本类型时，两种构造方法在效率上没有任何区别，当成员有class对象时，情况就不一样，在构造函数体中通过赋值运算符＝进行初始化时，会产生临时对象，在用成员初始化列表进行构造时，会直接调用class成员的copy构造函数，效率上会有所提升，不过现在的编译器会做何种优化也视不同的编译器而异。举个例子：

    
{% highlight cpp  %}
class Word {
    public;
        Word(){name=0;cnt=0;}
    public:
        string name;
        int cnt;
};
{% endhighlight %}

&emsp;&emsp;编译器对构造函数可能做的扩张如下：  

{% highlight cpp  %}
Word::Word() {
    name.string::String();
    string temp = string(0);
    name.string::operator=(temp);
    temp.string::~string();
    cnt=0
}
{% endhighlight %}

&emsp;&emsp; 另外一个例子：  

{% highlight cpp  %}
class Word {
    public:
        Word():name(0) {
            cnt=0;
        }
    public:
        string name;
        int cnt;
};
{% endhighlight %}

&emsp;&emsp; 编译器对构造函数做对扩展可能如下：  

{% highlight cpp  %}
Word::Word() {
    name.string::string(0);
    cnt = 0;
}
{% endhighlight %}

&emsp;&emsp; 完！

