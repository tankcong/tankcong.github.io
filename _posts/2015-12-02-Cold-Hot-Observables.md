---
layout: post
title: Cold Observable / Hot Observable
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - JAVA
    - RxJava
---

## Cold Observable / Hot Observable

Hot Observable 和 Cold Observable是Rx中一个非常重要的概念。二者最主要的区别在于：  

* Cold Observable是在被订阅的时候开始运行，同时产生的值不被各个Subscriber共享
* Hot Observable无论有没有Subscriber订阅，事件始终发生，当有多个Subscriber订阅时，Hot Observable发出的事件共享

做个比喻，如果Cold Observable是一部电影的话，每次被打开（订阅）观看，每个人看的进度都不同，Hot Observable则是现场直播，
无论观众多少，进度、时间都一样。

Cold:

```
    Action1<Long> subscriber1 = (result) -> System.out.println("subscriber1: " + result);
    Action1<Long> subscriber2 = (result) -> System.out.println("                subscriber2: " + result);
    Observable<Long> observable = Observable.interval(1000, TimeUnit.MILLISECONDS, Schedulers.computation())
            .observeOn(Schedulers.newThread());
    observable.subscribe(subscriber1);
    Thread.sleep(1000L);
    observable.subscribe(subscriber2);

```

执行结果：

```
subscriber1: 0
subscriber1: 1
                subscriber2: 0
subscriber1: 2
                subscriber2: 1
subscriber1: 3
                subscriber2: 2
subscriber1: 4
                subscriber2: 3
subscriber1: 5
                subscriber2: 4
subscriber1: 6
                subscriber2: 5

```

每次subscribe，Observable都被触发并独立的发送事件。
这里采用ConnecableObservable实现Hot Observable:

```
    Action1<Long> subscriberA = (result) -> System.out.println("subscriberA: " + result);
    Action1<Long> subscriberB = (result) -> System.out.println("    subscriberB: " + result);
    Action1<Long> subscriberC = (result) -> System.out.println("        subscriberC: " + result);
    ConnectableObservable<Long> observable = Observable.interval(1000, TimeUnit.MILLISECONDS, Schedulers.computation())
            .observeOn(Schedulers.newThread()).publish();
    observable.connect(); //在这里激活HotObservable
    Thread.sleep(1000L);
    observable.subscribe(subscriberA);
    Thread.sleep(1000L);
    observable.subscribe(subscriberB);
    Thread.sleep(1000L);
    observable.subscribe(subscriberC);

```

结果：

```
subscriberA: 1
subscriberA: 2
    subscriberB: 2
subscriberA: 3
    subscriberB: 3
        subscriberC: 3
subscriberA: 4
    subscriberB: 4
        subscriberC: 4
subscriberA: 5
    subscriberB: 5
        subscriberC: 5
```

可以看到，因为没有任何subscriber订阅，事件0依旧产生并被抛弃，后续订阅的Subscriber共享同一事件。
还可以使用Subject实现Hot Observable:

```
    Observable<Long> observable = Observable.interval(1000, TimeUnit.MILLISECONDS, Schedulers.computation()).
            observeOn(Schedulers.newThread());
    PublishSubject<Long> publishSubject = PublishSubject.create();
    observable.subscribe(publishSubject);
    Thread.sleep(1000L);
    publishSubject.subscribe(subscriberA);
    Thread.sleep(1000L);
    publishSubject.subscribe(subscriberB);
    Thread.sleep(1000L);
    publishSubject.subscribe(subscriberC);

```

Subject既可以作为Observable又可以作为Observer(Subscriber)。当其作为Observer时，它可以订阅目标Cold Observable
使对方开始发送事件，同时它又作为Observable转发或者发送新的事件，这就让Cold Observable借助Subject转换为Hot Observable。
关于Subject更多的内容在这里：

* [Rx Subject](http://reactivex.io/documentation/subject.html)  
* [To Use Subject Or Not To Use Subject](http:ggdavesexton.com/blog/post/To-Use-Subject-Or-Not-To-Use-Subject.aspx)
* [Subject part 1](http://akarnokd.blogspot.hu/2015/06/subjects-part-1.html)


### Readings

* **[cold vs hot observables](https://github.com/Reactive-Extensions/RxJS/blob/master/doc/gettingstarted/creating.md#cold-vs-hot-observables)**
* **[Hot and cold rx-java Observable](http://www.java-allandsundry.com/2015/03/hot-and-cold-rx-java-observable.html)**
* **[RxJava Tip for the Day - Share, Publish, Refcount and All That Jazz](http://nerds.weddingpartyapp.com/tech/2015/01/21/rxjava-share-publish-refcount-and-all-that-jazz/)**

