---
layout: post
title: Clean Code (Comments, Formatting)
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Notes
    - Clean Code
---

# Clean Code (Comments, Formatting)

## Comments

### Comments Do Not Make Up for Bad Code

### Explain Yourself in Code

> 代码才是唯一真正准确的信息来源。注释的恰当用法是弥补我们在用代码表达意图时的失败。  
> “Don’t comment bad code—rewrite it.”——Brian W.Kernighan and P.J.Plaugher

### 值得写的注释
* 法律信息
* 提供信息的注释  

```
// format matched kk:mm:ss EEE, MMM dd, yyyy
```
* 对意图的解释  

```
// This is our best attempt to get a race condition 
// by creating large number of threads.
```
* 阐释  
把难懂的参数或返回值翻译为可读的形式。

* 警示  

```
// SimpleDateFormat is not thread safe.
```
* TODO  
TODO注释定期查看、及时清理，比如IDE里自动生成的  

```
// TODO Auto-generated catch block
```
* 放大  
放大某处看似不合理之处的重要性  

```
// the trim is real important. It removes the starting spaces that
// could cause the item to be recognized as another list.
```

## Formatting
良好的代码格式关乎沟通，而沟通是专业开发者的头等大事。

* 向报纸学习，源文件顶部是高层次概念和算法。细节向下逐渐展开，直至找到源文件中最
底层的函数和细节。  
* 适当的空行分隔
* 紧密相关的代码互相靠近
  * 相关函数，有调用关系的函数，调用者尽可能放在被调用者上面
  * 概念相关（共同的命名模式，执行统一任务的不同变种等）  
* 变量声明尽可能靠近使用位置
  * Local Variable在函数顶部
  * Control Variable在循环语句中
  * Instance Variable在类顶部
  
