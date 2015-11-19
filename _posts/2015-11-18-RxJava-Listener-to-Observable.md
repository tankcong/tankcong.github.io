---
layout: post
title: 将Listener转换为RxJava中的Observable
author: 聪记
header-img: img/post_header_universe.jpg
tags:
    - RxJava
    - Java
---

## 将Listener转换为RxJava中的Observable

在使用RxJava中经常需要把现有的`Listener`转换为`Observable`，二者有很多共通之处，都可以被看做是
观察者模式([Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern))。Rx中
的`Observable`和`Subscriber`分别对应于Listener中的Publisher和Listener，所以在转换的时候通
常是将`Publisher`转换为Rx中的`Observable`。  
考虑如下代码，`ValueProcessor`作为Publisher，当Processor调用`process`后会回调`ValueListener`。

```
public class ListenerToRxNormal {

    public static void main(String[] args) {
        ValueListener listener = new ValueListener() {
            @Override
            public void onValueChanged(String value) {
                // be notified
            }
        };

        ValueProcessor processor = ValueProcessor.newInstance();
        processor.registerListener(listener);
        processor.process("abc");

    }

    interface ValueListener {
        void onValueChanged(String value);
    }

    static class ValueProcessor {

        ValueListener valueListener;

        private ValueProcessor(){}

        static ValueProcessor newInstance() {
            return new ValueProcessor();
        }

        public void registerListener(ValueListener valueListener) {
            this.valueListener = valueListener;
        }

        public void process(String value) {
            this.valueListener.onValueChanged(value);
        }
    }
}

```

再看下RxJava的版本：

```
    static class ValueProcessor {

        ValueListener valueListener;

        private ValueProcessor() {
        }

        static ValueProcessor newInstance() {
            return new ValueProcessor();
        }

        public void registerListener(ValueListener valueListener) {
            this.valueListener = valueListener;
        }

        public void unRegisterListener() {
            this.valueListener = null;
        }


        public void process(String value) {
            this.valueListener.onValueChanged(value);
        }

        public Observable<String> valueObservable() {
            return Observable.create(new Observable.OnSubscribe<String>() {
                @Override
                public void call(final Subscriber<? super String> subscriber) {
                    ValueListener listener = new ValueListener() {

                        @Override
                        public void onValueChanged(String value) {
                            if(!subscriber.isUnsubscribed()) {
                                subscriber.onNext(value);
                                subscriber.onCompleted();
                            } else {
                                unRegisterListener();
                            }
                            // unsubscribe callback
                            subscriber.add(Subscriptions.create(new Action0() {
                                @Override
                                public void call() {
                                    unRegisterListener();
                                }
                            }));
                        }
                    };
                    registerListener(listener);
                }
            });
        }
    }

```

注意体会这里`Observable`作为事件发送者的含义，还有当`Subscriber`取消订阅时候的处理。调用代码：

```
 ValueProcessor processor = ValueProcessor.newInstance();
     processor.valueObservable().subscribe(new Action1<String>() {
         @Override
         public void call(String s) {
             System.out.println("call: " + s);
         }
     });
processor.process("value");

```

再看下JakeWharton大神Rx for Android UI库[RxBinding](https://github.com/JakeWharton/RxBinding)
中的应用：

```
public final class RxView {

  // ···

  /**
   * Create an observable which emits on {@code view} click events. The emitted value is
   * unspecified and should only be used as notification.
   * <p>
   * <em>Warning:</em> The created observable keeps a strong reference to {@code view}. Unsubscribe
   * to free this reference.
   * <p>
   * <em>Warning:</em> The created observable uses {@link View#setOnClickListener} to observe
   * clicks. Only one observable can be used for a view at a time.
   */
  @CheckResult @NonNull
  public static Observable<Void> clicks(@NonNull View view) {
    checkNotNull(view, "view == null");
    return Observable.create(new ViewClickOnSubscribe(view));
  }

  // ···
}

final class ViewClickOnSubscribe implements Observable.OnSubscribe<Void> {
  private final View view;

  ViewClickOnSubscribe(View view) {
    this.view = view;
  }

  @Override public void call(final Subscriber<? super Void> subscriber) {
    checkUiThread();

    View.OnClickListener listener = new View.OnClickListener() {
      @Override public void onClick(View v) {
        if (!subscriber.isUnsubscribed()) {
          subscriber.onNext(null);
        }
      }
    };
    view.setOnClickListener(listener);

    subscriber.add(new MainThreadSubscription() {
      @Override protected void onUnsubscribe() {
        view.setOnClickListener(null);
      }
    });
  }

```
S
这里`RxView`就是Publisher。这里还涉及到了Rx的内存泄露问题，简单地说就是Subscriber持有Observable
的强引用，而Observable又持有View的强引用，最终会导致Activity/Fragment的内存泄露。后续再分析RxJava
在Android中使用的内存泄露问题。
