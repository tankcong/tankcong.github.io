---
layout: post
title: Clean Code (Unit Tests)
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Notes
    - Clean Code
---

# Unit Tests
个人最感兴趣的一章，因为我**从没**写过单 元 测 试。。。。现在甚至想把书中的每个字都搬到这里来。
做外包项目节奏太快，无奈。。。有机会一定要在项目中尝试一次敏捷。  
书中一直强调，好的代码是改出来的而不是第一次就写出来的，而重构现有代码并且不用担心会影响已上线功能的前提
就是，有足够多高质量的测试代码。这样，每次重构都确保能够通过之前的每条测试，不断迭代，直到达成最终的Clean Code。

## The Three Laws of TDD

> **1. You may not write production code until you have written a failing unit test.  
> 在编写不能通过的单元测试前，不可编写生产代码。**
> 
> **2. You may not write more of a unit test than is sufficient to fail, and not compiling is failing.
> 只可编写刚好无法通过的单元测试，不能编译也算不通过。**
> 
> **3. You may not write more production code than is sufficient to pass the currently failing test.
> 只可编写刚好足以通过当前失败测试的生产代码。**

保持30秒一个循环。测试与生产代码**一起**编写，测试只比生产代码早写几秒钟。

## Keeping Tests Clean

> **Test code is just as important as production code.**
> It is not a second-class citizen. It requires thought, design, and care. It must be kept as clean as production code. 

## One Assert per Test
尽量确保每个测试一个断言，每个测试只包含一个概念。

## F.I.R.S.T.
整洁测试的五条规则：

* **快速(FAST)**
测试运行的够快，确保能够频繁运行。
* **独立(Independent)**
测试相互独立。确保能独立运行每个测试，以任何顺序运行测试。
* **可重复(Repeatable)**
确保在任何环境中可重复通过。生产/测试环境。
* **自足验证(Self-Validating)**
确保测试总是有布尔值输出。不应该通过查看日志来确认测试是否通过。不应该手动对比文本文件来确认测试是否通过。
* **及时(Timely)**
测试应及时编写。单元测试应该恰好在使其通过的生产代码之前编写。

## Given-When-Then
可以通过given-when-then约定让测试更可读。

```
public void testGetPageHierarchyAsXml() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

    whenRequestIsIssued("root", "type:pages");

    thenResponseShouldBeXML();
  }

  public void testGetPageHierarchyHasRightTags() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

    whenRequestIsIssued("root", "type:pages");

    thenResponseShouldContain(
      "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
  }
```

