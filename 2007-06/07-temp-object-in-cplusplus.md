C++中的临时对象
========

还有20分钟就要高考了，祝天下含辛茹苦的考生们都能够取得自己好成绩。周日电话给弟弟，本来想跟他聊聊高考的，想听听作为一个过来人，去年时他的感受是什么样，现在回想起来又有什么收获或是体验。但他在玩就没有打扰他了。

书归正传，我们知道在C++的创建对象是一个费时，费空间的一个操作。有些固然是必不可少，但还有一些对象却在我们不知道的情况下被创建了。通常以下三种情况会产生临时对象：

1. 以值的方式给函数传参；
2. 类型转换；
3. 函数需要返回一个对象时；

现在我们依次看这三种情况：

### 一，以值的方式给函数传参。

我们知道给函数传参有两种方式。1，按值传递；2，按引用传递。按值传递时，首先将需要传给函数的参数，调用拷贝构造函数创建一个副本，所有在函数里的操作都是针对这个副本的，也正是因为这个原因，在函数体里对该副本进行任何操作，都不会影响原参数。我们看以下例子：

```c++
/*-----------------------
Platform:WinXp + VC6
-----------------------*/
#include <stdio.h>
class CTemp
{
public:
    int a;
    int b;
public:
    CTemp(CTemp& t){ printf("Copy function!\n");a = t.a;b = t.b;};
    CTemp(int m = 0,int n = 0);
    virtual ~CTemp(){};
public:
    int GetSum(CTemp ts);
};
CTemp::CTemp(int m , int n)
{
    printf("Construct function!\n");
    a = m;b=n;
    printf("a = %d\n",a);
    printf("b = %d\n",b);
}
int CTemp::GetSum(CTemp ts)
{
    int tmp = ts.a + ts.b;
    ts.a = 1000;           //此时修改的是tm的一个副本

    return tmp;
}
//--------------Main函数-----------------
void main()
{
    CTemp tm(10,20);
    printf("Sum = %d \n",tm.GetSum(tm));
    printf("tm.a = %d \n",tm.a);
}
```

Output:

```text
Construct function!
a = 10
b = 20
Copy function!
Sum = 30
tm.a = 10
```

我们看到有调用了拷贝构造函数，这是`tm`在传给`GetSum`做参数时：

1. 调用拷贝构造函数来创建一个副本为`GetSum`函数体内所用。
2. 在`GetSum`函数体内对tm副本进行的修改并没有影响到tm本身。

**解决办法：**

针对第一种情况的解决办法是传入对象引用(记住：引用只是原对象的一个别名(Alias))，我们将`GetSum`代码修改如下：

```c++
int CTemp::GetSum(CTemp& ts)
{
    int tmp = ts.a + ts.b;
    ts.a = 1000;     //此时通过ts这个引用参考(refer to)对象本身
    return tmp;
}
```

Output:

```text
Construct function!
a = 10
b = 20
Sum = 30
tm.a = 1000
```

可以通过输出看本，通过传递常量引用，减少了一次临时对象的创建。这个改动也许很小，但对多继承的对象来说在构建时要递归调用所有基类的构造函数，这对于性能来说是个很大的消耗，而且这种消耗通常来说是没有必要的。

### 二，类型转换生成的临时对象。

我们在做类型转换时，转换后的对象通常是一个临时对象。编译器为了通过编译会创建一起我们不易察觉的临时对象。再次修改如上`main`代码：

```c++
void main()
{
 CTemp tm(10,20),sum;
 sum = 1000;  //调用CTemp(int m = 0,int n = 0)构造函数
 printf("Sum = %d \n",tm.GetSum(sum));
}
```

Output:

```text
Construct function!
a = 10
b = 20
Construct function!
a = 0
b = 0
Construct function!
a = 1000
b = 0
Sum = 1000
```

`main`函数创建了两个对象，但输出却调用了三次构造函数，这是为什么呢？

关键在`sum = 1000;`这段代码。本身1000和sum类型不符，但编译器为了通过编译以1000为参调用构造函数创建了一下临时对象。

**解决办法：**

我们对`main`函数中的代码稍作修改，将`sum`申明推迟到`=`号之前：

```c++
void main()
{
 CTemp tm(10,20);
 CTemp sum = 1000;
 printf("Sum = %d \n",tm.GetSum(sum));
}
```

Output:

```text
Construct function!
a = 10
b = 20
Construct function!
a = 1000
b = 0
Sum = 1000
```

只作了稍稍改动，就减少了一次临时对象的创建。

1. 此时的`=`号由原本的赋值变为了构造。
2. 对`Sum`的构造推迟了。当我们定义`CTmep sum`时，在`main`的栈中为`sum`对象创建了一个预留的空间。而我们用1000调用构造时，此时的构造是在为`sum`预留的空间中进行的。因此也减少了一次临时对象的创建。

### 三，函数返回一个对象。

当函数需要返回一个对象，他会在栈中创建一个临时对象，存储函数的返回值。看以下代码:

```c++
#include <stdio.h>
class CTemp
{
public:
    int a;
public:
    CTemp(CTemp& t) //Copy Ctor!
    {
        printf("Copy Ctor!\n");
        a = t.a;
    };
    CTemp& operator=(CTemp& t) //Assignment Copy Ctor!
    {
        printf("Assignment Copy Ctor!\n");
        a = t.a;
        return *this;
    }
    CTemp(int m = 0);
    virtual ~CTemp(){};
};
CTemp::CTemp(int m) //Copy Ctor!
{
    printf("Construct function!\n");
    a = m;
    printf("a = %d\n",a);
}
CTemp Double(CTemp& ts)
{
    CTemp tmp;      //构建一个临时对象
    tmp.a = ts.a*2;
    return tmp;

}
//-------------Main函数-----------------
void main()
{
    CTemp tm(10),sum;
    printf("\n\n");
    sum = Double(tm);

    printf("\n\nsum.a = %d \n",sum.a);
}
```

Output:

```text
Construct function!
a = 10
Construct function!
a = 0

Construct function!
a = 0
Copy Ctor!
Assignment Copy Ctor!

sum.a = 20
```

我特地加宽了语句:`sum = Double(tm);`。这条语句竟生成了两个对象，Horrible! 我们现在将这条语句逐步分解一下：

1. 我们显式创建一个tmp临时对象,

  语句：CTemp tmp;

2. 将temp对象返回，返回过程中调用Copy cotr创建一个返回对象,
  语句：return tmp;

3. 将返回结果通过调用赋值拷贝函数,赋给sum

  语句: sum = 函数返回值;(该步并没有创建对象，只是给sum赋值)

`tm.Double`返回一个用拷贝构造函数生成的临时对象，并用该临时对象给sum赋值.

上面的第1步创建对象可以不用创建，我们可以直接对返回值进行操作，有些C++编译器中会有一种优化，叫做(NRV,named return value).不过本人使用的VC++6.0并没有这个启用这个优化。

第2步创建的返回对象是难以避免的，你或许想可以返回一个引用，但你别忘记了在函数里创建的局部对象，在返回时就被销毁了。这时若再引用该对象会产生未预期的行为。(C#中解决了这个问题)。

**解决方法：**

我们将对象直接操作(Manipulate)返回对象,再结合上面的减少临时对象的方法，将函数Double的代码，及main函数中的代码修改如下：

```c++
CTemp Double(CTemp& ts)
{
    return ts.a*2;
}
/*--------上面的代码相当于-------
CTemp _ret
void Double(CTemp& ts)
{
    _ret.a = ts.a*2;
}
---------------*/

//---------Main函数-----------
void main()
{
    CTemp tm(10);
    printf("\n\n");

    CTemp sum = Double(tm);

    printf("\n\nsum.a = %d \n",sum.a);
}
```

Output:

```text
Construct function!
a = 10

Construct function!
a = 20

sum.a = 20
```

发现减少了一次构造函数调用(tmp)，一次拷贝构造函数(tmp拷贝给返回对象)调用和一次赋值拷贝函数调用.(Assignment Copy Ctor),这是因为：

返回对象直接使用为`sum`预留的空间，所以减少了返回临时对象的生成——返回对象即是`sum`，返回对象的创建即是`sum`对象的创建.多么精妙!
