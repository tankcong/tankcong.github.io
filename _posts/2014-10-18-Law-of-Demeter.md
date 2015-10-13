---
layout: post
title: Law of Demeter
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Java
---

Law of Demeter
=====================
[《程序员修炼之道》](http://book.douban.com/subject/1152111/)
中提到，通过编写尽可能遵守[Law of Demeter(LOD)](https://en.wikipedia.org/wiki/Law_of_Demeter)
的代码，可以实现目标：  
```
Minimize Coupling Between Modules  
使模块之间的耦合减至最少  
```  

> Law of Demeter says that a method M of object O can access / invoke methods of:  
> 1. O itself  
> 2. M’s input arguments  
> 3. Any object created in M  
> 4. O’s parameters / dependencies  

示例如下：

```
class Demeter {

    private ObjectA a = new ObjectA();

    private int MethodB(){};

    public void MethodC(ObjectD d) {
        int i = MethodB();          //自身拥有的方法，OK
        ObjectC c = new ObjectC();  
        c.setField();               //方法直接创建的对象，OK
        d.doSomething();            //方法传入的任何参数，OK
        a.toString();               //拥有的成员，OK
        ObjectE e = a.getE();
        //不允许，MedthodC并不拥有ObjectE，考虑改成process(e.getEName())
        //不建议调用依赖对象ObjectA的实现的模块ObjectE
        process(e.name);      
    }

}
```
  遵循LoD，缩小了调用类中响应集的规模，使代码适应性更好更健壮。但因为不建议调用依赖对象的实现，
  在实践中可能产生大量的包装方法将请求转发，这些包装方法可能带来巨大的时间、空间开销。  
  有时通过反转LoD，为了性能的提升而使若干模块紧密耦合。前提是在那些被耦合的模块内部是众所周知
  和可以接受的。


相关：  
Code Complete[耦合分类](https://github.com/tankcong/tankcong.github.io/blob/master/Kinds%20of%20Coupling.md)

