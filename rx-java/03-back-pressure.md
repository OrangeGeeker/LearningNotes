# BackPressure

## 背压

异步订阅事件中，为了控制事件流量而做的限制，因为事件发送速度可能会超过事件消费速度，此时发送的事件会缓存起来

- Observable 默认没有 BackPressure 限制
- 减少发送的事件数量、减少发送事件的速度

### 减少发送事件的数量

会丢失大部分事件

- 使用 Filter
- 使用 Sample

### 延时发送

- Thread.sleep()

## Flowable Subscriber

```java
Flowable<Integer> upstream = Flowable.create(new FlowableOnSubscribe<Integer>(){
    @Override
    public void subscribe(FlowableEmitter<Integer> emitter) throws Exception{
        emitter.onNext(1);
        emitter.onNext(2);
        emitter.onNext(3);
        emitter.onComplete();
    }
}, BackpressureStrategy.ERROR);

Subscriber<Integer> downStream = new Subscriber<Integer>(){
    @Override
    public void onSubscribe(Subscription s){
        s.request(Long.MAX_VALUE);
    }

    @Override
    public void onNext(Integer integer){}

    @Override
    public void onError(Throwable t) {
            Log.w(TAG, "onError: ", t);
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "onComplete");
    }
}

upstream.subscribe(downstream);
```

- Subscription 调用其 cancel() 也可以切断事件流，相当于 Disposable.dispose()
- Subscription#request(long n) 这个方法表示 Subscriber 请求的事件个数，如果不设置，是不会接收到事件的。可以当成 Subscriber 处理事件的能力，这样 Flowable 会根据它的处理能力来决定发送多少事件，就不会造成无脑发送事件导致 OOM，既没有丢失事件，也没有时间上的多余消耗。
- Flowable 默认缓存为 128，如果不设置 request 个数，仍然会发送事件，只有当调用 request 时，会从缓存中取出事件消费

## 背压策略

BackpressureStrategy

- ERROR 超过默认 128 抛出异常
- BUFFER 无限制，同 Observable
- DROP 每次缓存默认 128 个事件，其余忽略。当调用 request(128) 直到事件消费完成，再接着缓存事件
- LATEST 保留最新事件

对于不是创建的 Flowable，可以调用 onBackpressureBuffer、onBackpressureDrop 等进行设置

## FlowableEmitter

long requested()

在 onSubscribe() 中调用 Subscription.request(n)，因为 onSubscribe 最先执行，因此在 FlowableOnSubscribe 的 subscribe 中，可以调用 FlowableEmitter#requested 获取到这个数值，来进行特定的数量事件发送

对于 request() 的多次调用，会进行加法，发送事件 onNext() 会进行减法

每个线程里都有一个 requested

```java
public static void main(String[] args) {
    practice1();
    try {
        Thread.sleep(10000000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}

public static void practice1() {
    Flowable
        .create(new FlowableOnSubscribe<String>() {
            @Override
            public void subscribe(FlowableEmitter<String> emitter) throws Exception {
                try {
                    FileReader reader = new FileReader("test.txt");
                    BufferedReader br = new BufferedReader(reader);

                    String str;

                    while ((str = br.readLine()) != null && !emitter.isCancelled()) {
                        while (emitter.requested() == 0) {
                            if (emitter.isCancelled()) {
                                break;
                            }
                        }
                        emitter.onNext(str);
                    }

                    br.close();
                    reader.close();

                    emitter.onComplete();
                } catch (Exception e) {
                    emitter.onError(e);
                }
            }
        }, BackpressureStrategy.ERROR)
        .subscribeOn(Schedulers.io())
        .observeOn(Schedulers.newThread())
        .subscribe(new Subscriber<String>() {

            @Override
            public void onSubscribe(Subscription s) {
                mSubscription = s;
                s.request(1);
            }

            @Override
            public void onNext(String string) {
                System.out.println(string);
                try {
                    Thread.sleep(2000);
                    mSubscription.request(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }

            @Override
            public void onError(Throwable t) {
                System.out.println(t);
            }

            @Override
            public void onComplete() {
            }
        });
}
```
