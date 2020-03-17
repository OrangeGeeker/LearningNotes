# RxJava

```java
compile "io.reactivex.rxjava2:rxjava:2.1.4"
compile "io.reactivex.rxjava2:rxandroid:2.0.1"
```

## RxJava 4个基础接口

- Publisher 发布者
- Subscriber 订阅者
- Subscription
- Processor

## 观察者模式基本实现

Observable 和 Observer 不支持背压

Flowable 和 Subscriber 支持背压

## 创建 Observable

- 使用 `create()` 来创建一个 Observable
- 使用操作符来将现存的数据结构转化

```java
Observable<String> observable = Observable.create(
    new ObservableOnSubscribe<String>(){
        @Override
        public void subscribe(@NonNull ObservableEmitter<String> e) throws Exception{
            e.onNext("hello");
            e.onNext("~");
            e.onNext("world");
            e.onNext("!");
            e.onComplete();
        }
    }
);

Observer<Integer> observer = new Observer<Integer>(){
    @Override
    public void onSubscribe(Disposable d){

    }
    @Override
    public void onNext(String s){

    }
    @Override
    public void onError(Throwable e){

    }
    @Override
    public void onComplete(){

    }

observable.subscribe(observer);
}
```

- 代码中，传入了一个 `ObservableOnSubscribe` 对象作为参数，其会被存储在返回的 `Observable` 中，它的作用相当于一个计划表，当 `Observable` 被订阅的时候，`ObservableOnSubscribe` 的 `subscribe()` 方法会被调用，事件序列就会触发
- `ObservableEmitter` 为事件发射器，进行事件的发送
  - `onNext(T value)` 可以发送无限个 `onNext()`
  - `onComplete()` 发送后，回调 `Observer#onComplete()`，表示事件队列完结。`Observable` 会继续发送之后的事件，但是 `Observer` 不会接收事件
  - `onError`(Throwable error) 发送后，回调 `Observer#onErroe()`，表示事件队列出错。`Observable` 会继续发送后续事件，但 `Observer` 不会接收事件
  - `onComplete()` 和 `onError()` 有且只有一个，是互斥的
- `Observable`
  - 最先调 `Observer#onSubscribe()`，然后调用 `ObservableOnSubscribe` 的 `subscribe()`
  - `Observer#onNext()` 在 `emitter.onNext()` 调用后并且观察者收到时调用

## Disposable

- Disposable 当调用其 `dispose()` 时，Observer 不会接收事件，Observable 会继续发送事件，在 Activity 等退出时调用。
- 多个 Disposable 使用 `CompositeDisposable`，每次得到一个 Disposable，调用 `CompositeDisposable.add()` 添加到容器中，在退出时，调用 `CompositeDisposable.clear()`

## Schedualer 线程控制

不指定线程的情况下， RxJava 遵循线程不变原则，在哪个线程调用 subscribe() 就在哪个线程生产事件、消费事件，如果需要切换线程，就需要用到 Scheduler

- Schedulers.immediate()：运行在当前线程，默认的 Scheduler
- Schedulers.newThread()：总是启动新线程，并在新线程执行操作
- Schedulers.io()：I/O 操作所使用的 Scheduler，和 Schedulers.newThread() 的区别是，内部实现是用一个无限数量上限的线程池，可以重用空闲线程。
- Schedulers.computation()：计算所使用的 Scheduler，值的是 CPU 密集型计算，即不会被 I/O 等限制性能的操作，例如图形计算。这个 Scheduler 使用固定线程池，大小为 CPU 核数
- Schedulers.mainThread()：Android 专有，操作将在主线程运行

### 使用 subscribeOn() 和 observerOn() 进行线程控制。

- `subscirbeOn()` 指定 `subscribe()` 发生的线程，即 ObservableOnSubsribe 被激活时所处的线程，或者叫事件产生线程，只有第一次有效
- `observerOn()` 指定 `Observer` 所运行在的线程，或者叫事件消费线程，可以指定多次

例如：“后台线程取数据、主线程显示” 程序策略

```java
Observable.just(1, 2, 3, 4)
    .subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
    .observeOn(AndroidSchedulers.mainThread()) // 指定 Observer 的回调发生在主线程
    .subscribe(new Action1<Integer>() {
        @Override
        public void call(Integer number) {
            Log.d(tag, "number:" + number);
        }
    });
```
