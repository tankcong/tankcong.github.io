---
layout: post
title: RXJava中observeOn跟subscribeOn的区别
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Android
    - RXJava
---

## RXJava中observeOn跟subscribeOn的区别

刚接触`RXJava`对工作线程的理解不是特别清晰，现在结合[这篇](https://medium.com/@diolor/observe-in-the-correct-thread-1939bb9bb9d2)文章后
总结一下。

* `Observable.create()`创建的`Observable`是**被观察者**、是事件的**发送方**。
* `Observable.subscribe()`中的参数`Subscriber`是**观察者**，是事件的**订阅方**。
* 基本流程是观察者**订阅**被观察者的事件（或者说操作结果）

明确了以上三点以后就容易理解`observeOn`跟`subscribeOn`的区别了：

* `subscribeOn`改变的是`Observable`所**执行事件**的线程
* `observeOn`改变的是`Observable`将事件**发送到**得线程，也就是`Subscriber`执行的线程
* 默认情况下，调用链上的操作都是工作在`subscribe()`方法被调用所在的线程上。

Show Me The Code:  

```
    void onRunSchedulerExampleButtonClicked() {
        sampleObservable()
                // subscribe on a I/O thread
                .subscribeOn(Schedulers.io())
                // Be notified on the main thread
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Subscriber<String>() {

                    //Main Thread
                    @Override public void onCompleted() {
                        Log.d(TAG, "onCompleted()");
                    }

                    //Main Thread
                    @Override public void onError(Throwable e) {
                        Log.e(TAG, "onError()", e);
                    }

                    //Main Thread
                    @Override public void onNext(String string) {
                        Log.d(TAG, "onNext(" + string + ")");
                    }
                });
    }

    static Observable<String> sampleObservable() {
        return Observable.defer(new Func0<Observable<String>>() {

            // I/O Thread
            @Override public Observable<String> call() {
                try {
                    // Do some long running operation
                    Thread.sleep(TimeUnit.SECONDS.toMillis(5));
                } catch (InterruptedException e) {
                    throw OnErrorThrowable.from(e);
                }
                return Observable.just("one", "two", "three", "four", "five");
            }
        });
    }
```