# Clean Code (Classes, System, Emergence)

将类、系统和简洁设计这三个相关的章节一起总结。

## Getting Clean via Emergent Design

简洁设计的四条规则：
> * Runs all the tests
> 运行所有测试；
> * Contains no duplication
> 不可重复；
> * Expresses the intent of the programmer
> 表达了程序员的意图；
> * Minimizes the number of classes and methods
> 尽可能减少类和方法的数量；

以上规则按其重要程度排列。

### Simple Design Rule 1: Runs All the Tests
这要是本书一直强调的，良好的代码一定是**可测试**的。
> 遵循有关的编写测试并持续运行测试的见到你、明确地规则，系统会更贴近OOP中高内聚、低耦合的目标。
> 从而引进更好的设计。

### Simple Design Rules 2–4: Refactoring
注意重构的前提：  
**测试消除了对清理代码就会破坏代码的恐惧。**  
然后再重构中，消除重复、保证表达力、尽可能减少类的方法和数量。

### DRY
老生常谈。确保**DRY**，确保**SRP**。
注意这种形态的重复：
```
int size()
boolean isEmpty() {}
```
如果`size()`跟`isEmpty()`是分开实现的，那么也算重复。可以通过这样来消除重复：
```
boolean isEmpty() {
    return 0 == size();
}
```

## Expressive
通过：
* 好的名称（类名、函数名）
* 短小的函数、类
* 良好的命名
* 良好的单元测试
来达到代码良好的表达力。不过最重要的一点，也是本书自始至终灌输的，**尝试**。
> Care is a precious resource.

## Minimal Classes and Methods
尽量保证函数、类短小，从而来保持系统短小。但不必过于拘泥于此，避免*类型爆炸*。
更重要的是**测试**，**DRY**，**Expressive**。
