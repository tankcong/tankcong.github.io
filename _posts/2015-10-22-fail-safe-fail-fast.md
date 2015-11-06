---
layout: post
title: Fail-Safe & Fail-Fast Iterator in Java
author: 聪记
header-img: img/post_header_universe.jpg
tags:
    - Java
    - Concurrency
---

## Fail-Safe & Fail-Fast Iterator in Java

在看Java Concurrent包中`CopyOnWriteArrayList`的时候又看到了fail-fast和fail-safe这两个
概念。整理一下：  

二者都是`Iterator`对**ConcurrentModification**的处理机制，前者立即抛出
`ConcurrentModificationException`，后者不触发异常但是会牺牲数据的一致性。

### Fail-Fast
[JavaDoc](http://docs.oracle.com/javase/7/docs/api/java/util/ConcurrentModificationException.html):

>    This exception may be thrown by methods that have detected concurrent
> modification of an object when such modification is not permissible.  

`ArrayList`[SourceCode](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/8u40-b25/java/util/ArrayList.java#ArrayList):
```
final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
```
其中`modCount`是当前List Size，`expectedModCount`是`Iterator`创建时的Size，每次调用`Iterator.next()`时都会调用
`checkForComodification`。所以只要任何一个线程(包括当前线程)在遍历时修改了List大小都会抛出
此异常。

`ArrayList.iterator()`的JavaDoc:
```
Returns an iterator over the elements in this list in proper sequence.
The returned iterator is fail-fast.
```

### Fail-Safe
拿`CopyOnWriteArrayList`举例，[JavaDoc](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CopyOnWriteArrayList.html):

> The "snapshot" style iterator method uses a
> reference to the state of the array at the point that the iterator
> was created. This array never changes during the lifetime of the
> iterator, so interference is impossible and the iterator is
> guaranteed not to throw **ConcurrentModificationException**.
> The iterator will not reflect additions, removals, or changes to
> the list since the iterator was created.

再看下`CopyOnWriteArrayList.add()`，[SourceCode](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/8u40-b25/java/util/concurrent/CopyOnWriteArrayList.java#CopyOnWriteArrayList.add%28java.lang.Object%29):
```
public void add(int index, E element) {
     Object[] elements = getArray();
         int len = elements.length;
             Object[] newElements;
             int numMoved = len - index;
             if (numMoved == 0)
                 newElements = Arrays.copyOf(elements, len + 1);
             else {
                 newElements = new Object[len + 1];
                 System.arraycopy(elements, 0, newElements, 0, index);
                 System.arraycopy(elements, index, newElements, index + 1,
                                numMoved);
             }
         newElements[index] = element;
         setArray(newElements);
 }
```
很明显，修改集合值的时候，先复制了一份新的元素集合，当在新的元素集合修改完之后再修改原集合。
原集合在省城`Iterator`的时候已经保存，所以并不会观察到新的修改。代价是不能保证数据的一致性，同时引入了额外的内存开销。
