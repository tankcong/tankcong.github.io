# Clean Code (Functions)
[《Clean Code》](http://book.douban.com/subject/3032825/) Book Notes. Chapter *Functions*

语录摘抄再前面，Chapter 3, Section 12
## 如何写出好的函数
> &emsp;&emsp;Writing software is like any other kind of writing. When you write a paper or
> an article, you get your thoughts down first, then you massage it until it reads
> well. The first draft might be clumsy and disorganized, so you wordsmith it and
> restructure it and refine it until it reads the way you want it to read.  
> &emsp;&emsp;When I write functions, they come out long and complicated. They have lots of
> indenting and nested loops. They have long argument lists. The names are arbitrary,
> and there is duplicated code. But I also have a suite of unit tests that cover
> every one of those clumsy lines of code.  
> &emsp;&emsp;**So then I massage and refine that code, splitting out functions, changing names,
> eliminating duplication. I shrink the methods and reorder them. Sometimes I break
> out whole classes, all the while keeping the tests passing.**  
> &emsp;&emsp;**In the end, I wind up with functions that follow the rules I’ve laid down in this
> chapter. I don’t write them that way to start. I don’t think anyone could.**  

一句话，好代码是改出来的。

## 小，只做一件事，一个抽象级(Small, Do One Thing, One Level of Abstraction per Function)
三节综合在一起。每个函数确保**只做一件事, 做好这件事**，函数内的方法确保在**一个抽象级**，结果会自然引出
函数体**短小**。  
这里涉及到对抽象级的理解。比方说要完成A工作的工序是b()->c()->d()，那么b()、c()、d()就算同一
抽象级，同理，b()、c()、d()中也按照抽象级不断拆分，直至足够细分短小。
```
protected void init() {
    initDatas();
    initViews();
    initControllers();
}

private void initDatas() {
    readPreferences();
    requestData();
}

...
```
**向下规则**，每个函数后面都跟着位于下一抽象层级的函数（每个函数都描述当前抽象层级），这样在查
看函数列表时，就能遵循抽象层级向下阅读。

## Switch语句(Switch Statement)
看到这章想起了[《程序员修炼之道》](http://book.douban.com/subject/1152111/)里的代码技巧，
通过定义子类来重构switch语句。Clean Code里也提出利用多态，通过抽象工厂来重构switch语句，不过
对于switch的使用需要就事论事，不必拘泥。

## 使用描述性的名称(Use Descriptive Names)
适当使用长名称。长而具有描述性的名称，要比短而令人费解的名称好。长而具有描述性的名称，要比描述性的
长注释好。使用某种命名约定，让函数名称中的多个单词容易阅读，然后使用这些单词给函数取个能说清其
公用的名称。
```
includeSetupAndTeardownPages
includeSetupPages
includeSuiteSetupPage
```

## 函数参数 (Function Arguments)
函数的参数个数**尽可能少**，无参数最好，其次是单参数、双参数，尽量避免三个或更多参数。
从测试的角度来看，没有参数非常容易测试，单参数也方便测试，当有两个或更多参数时，测试时需要覆盖
所有可能值的组合则成倍增加。  
避免将Bool值作为一元函数的参数没因为这会使函数不止做一件事。True这样做，False那样做。`render(boolean isSuite)`
可以拆分优化为`renderForSuite()`和`renderForSingleTest()`  
如果函数看起来需要三个或更多参数，就说明其中一些参数应该**封装为类**了。
```
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

## 无副作用 (Have No Side Effects)
注意函数中可能被隐藏起来的事。
```
public boolean checkPassword(String userName, String password) {
    // do something verification
    if(isValid) {
        Session.initialize(); //这里隐藏了Session的初始化过程，如果有人只是想验证
                              //用户名密码，则会有可能重置会话数据，造成**时序性耦合**
                              //可以修改为checkPasswordAndInitializeSession
    }
}
```

避免使用输出参数。也就是输出实际是改变了函数的入参。印象中Android中几个关于Rect的运算API都是
输出参数。

## 使用异常代替返回错误码 (Prefer Exceptions to Returning Error Codes)
使用异常的好处是可以**抽离Try/Catch代码块**，函数逻辑是*一件事*，错误处理是*一件事*。  
还可以使用派生的异常类来避免不断增加的错误码。

## 不要重复自己(DRY)
DRY是编程中的一项基本准则，从函数的角度来看，所有重复的代码都应该封装成函数。  
在代码上的重复现在已经基本没有了，但是Android资源上经常一懒就重复了，比如Color、Dimen之类的定义
并没有放入Values.
