# RxJava 操作符

## map / flatMap / concatMap

map：对发射的每一个事件应用一个函数，每个事件按照指定的函数去变化，并返回指定的类型。

flatMap：将 Observable 发送的事件，变换为多个发送事件的 Observable。然后将这些分散的 Observable 个屏道一个单独的 Observable 里。

- flatMap 不能保证事件的顺序

concatMap：是有序的，其他同 flatMap。

## amb / concat / merge / switchOnNext

amb：给定两个或多个Flowable，只发射最先发射数据的Flowable

comcat：将两个发射器连接成一个发射器进行事件发射，按顺序发射，不会交错。

merge：给定多个 Flowable，按照 Flowable 数组中数据发射的顺序组合成新的 Flowable，各 Flowable 数据可能会交错（等价于转换操作中的flatMap）

switchOnNext：给定能发射多个 Flowable 的 Flowable，顺序发射各子 Flowable，最新发射的子 Flowable 覆盖当前子 Flowable 中还未发射的元素（由 switchMap 实现）

```java
Flowable<String> f1 = Flowable.intervalRange(1, 10, 1, 1, TimeUnit.SECONDS)
    .map(new Function<Long, String>() {
      @Override public String apply(@NonNull Long aLong) throws Exception {
        return "f1-"+aLong;
      }
    });

Flowable<String> f2 = Flowable.intervalRange(1, 3, 2, 2, TimeUnit.SECONDS)
    .map(new Function<Long, String>() {
      @Override public String apply(@NonNull Long aLong) throws Exception {
        return "f2-"+aLong;
      }
    });

Flowable.ambArray(f1, f2).map(new Function<String, String>() {
  @Override public String apply(@NonNull String s) throws Exception {
    return "amb "+s;
  }
}).subscribe(new Consumer<String>() {
  @Override public void accept(String s) throws Exception {
    Log.i(TAG, "accept: "+s);
  }
});

Flowable.concat(f1, f2).map(new Function<String, Object>() {
  @Override public Object apply(@NonNull String s) throws Exception {
    return "concat "+s;
  }
}).subscribe(new Consumer<Object>() {
  @Override public void accept(Object o) throws Exception {
    Log.i(TAG, "accept: "+o);
  }
});

Flowable.merge(f1, f2).map(new Function<String, String>() {
  @Override public String apply(@NonNull String s) throws Exception {
    return "merge "+s;
  }
}).subscribe(new Consumer<String>() {
  @Override public void accept(String s) throws Exception {
    Log.i(TAG, "accept: "+s);
  }
});

final Flowable[] flowables = new Flowable[]{f1, f2};

Flowable.switchOnNext(Flowable.intervalRange(0,2,0,3,TimeUnit.SECONDS).map(new Function<Long, Publisher<?>>() {
  @Override public Publisher<?> apply(@NonNull Long aLong) throws Exception {
    return flowables[aLong.intValue()];
  }
})).map(new Function<Object, String>() {
  @Override public String apply(@NonNull Object s) throws Exception {
    return "switchOnNext "+s;
  }
}).subscribe(new Consumer<String>() {
  @Override public void accept(String s) throws Exception {
    Log.i(TAG, "accept: "+s);
  }
});

//Flowable.intervalRange(0, 2, 0, 3, TimeUnit.SECONDS).map(i -> flowables[i.intValue()])
//    .switchMap((io.reactivex.functions.Function) Functions.identity())
//    .map(x -> "switchMap-" + x).subscribe(System.out::println);
```

## zip / combineLatest

zip：通过一个函数，将多个 Observable 各去除一个事件组合到一起，然后发送这些组合到一起的结果。

- 分别从 Observable 各取出一个事件事件严格按照顺序，只能被使用一次
- 只发送与数据项最少的那个 Observable 相等的数据，多余的 Observable 事件不会被发送

combineLatest：每个 Flowable 中的发射的元素都与其他 Flowable 最近发射的元素结合变换，直到所有的 Flowable 的元素发射完毕

```java
Observable.zip(getStringObservable(), getIntegerObservable(),
    new BiFunction<String, Integer, String>() {
      @Override public String apply(@NonNull String s, @NonNull Integer integer)
          throws Exception {
        return s + integer;
      }
    }).subscribe(new Consumer<String>() {
  @Override public void accept(String s) throws Exception {
    Log.i(TAG, "zip accept: " + s);
  }
});
```

## buffer / groupBy / window

buffer 和 window 按照事件或元素数量转换。buffer 直接转换成元素集，window 转换成另一个 flowable

groupBy：按照 key 来分组，需要元素发射完成才能消费，如果只是对数据处理使用 Java8 groupBy 更方便

## take / skip / first / elementAt

take：类似 java8 的 limit 操作，最多接收 count 个数据。还有 takeLast / takeUntil / takeWhen

skip：类似java8 skip 操作，跳过 count 开始接收，但是这里的可以扩展到时间区间上

first/firstElement/last/lastElement，由 Flowable -> Single/Maybe

## debounce / throttleFirst / sample

debounce:防抖动，元素发射后在设定的超时时间内没有其它元素发射，则将此元素用于后续处理

throttleFirst:取发射后元素，经过间隔时间后的第一个元素进行发射

sample:对于源数据，发射间隔时间内的最后出现的元素

## interval / intervalRange

注意使用 Disposable 进行销毁

## timer

## distinct

去掉重复的事件。

## filter

通过一个函数去进行事件的过滤。

## defer

每次订阅都会创建一个新的 Observable，并且如果没有被订阅，不会产生新的 Observable

## reduce

每次用一个方法处理一个值，可以有一个 seed 作为初始值

## scan

reduce 是只显示结果，scan 回把每一个步骤输出

## doOnNext / doOnComplete / doOnSubscribe

让订阅者在接收数据前做一些事情

## 创建 Observable / Flowable

### just

将一个或多个对象转换为 Observable

```java
Observable.just("1", "2", "3")
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(new Consumer<String>() {
      @Override public void accept(@NonNull String s) throws Exception {
        Log.i(TAG, "accept : onNext : " + s);
      }
    });
```

### from

#### fromArray / fromIterable 直接从数组或迭代器中产生

```java
List<String> list = new ArrayList<String>();
list.add("1");
list.add("2");
list.add("3");
Flowable.fromIterable(list)
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(new Consumer<String>() {
      @Override public void accept(@NonNull String s) throws Exception {
        Log.i(TAG, "accept : onNext : " + s + "\n");
      }
    });

Flowable.fromArray(list.toArray())
    .subscribeOn(Schedulers.io())
    .subscribe(new Consumer<Object>() {
      @Override public void accept(Object o) throws Exception {
        Log.i(TAG, "accept : onNext : " + Thread.currentThread().getName());
        Log.i(TAG, "accept : onNext : " + o.toString() + "\n");
      }
    });

Flowable.just("hello")
    .subscribe(new Comsumer<String>() {
      @Override public void accept(String s) throws Exception {
        Log.i(TAG, "accept " + s);
      }
    });
```

#### fromFuture / fromCallable

事件从非主线程中产生 / 事件从主线程中产生，在需要消费时生产

```java
ExecutorService executorService = Executors.newFixedThreadPool(3);
Log.i(TAG, "fromFuture: MAIN " + Thread.currentThread().getId());

Callable<String> callable = new Callable<String>() {
  @Override public String call() throws Exception {
    Log.i(TAG, "call: " + Thread.currentThread().getId());
    return null;
  }
};

Future<String> future = executorService.submit(callable);

Consumer<String> onNext =new Consumer<String>() {
  @Override public void accept(String s) throws Exception {
    Log.i(TAG, "accept:"+Thread.currentThread().getId());
  }
};

Flowable.fromCallable(callable).subscribe(onNext);
Flowable.fromFuture(future).subscribe(onNext);
Log.i(TAG, "fromFuture: END");
```

### single & completable & maybe

Single 可以发射一个单独onSuccess 或 onError消息

```java
Single.create(new SingleOnSubscribe<Integer>() {
  @Override public void subscribe(@NonNull SingleEmitter<Integer> e) throws Exception {
    e.onSuccess(100);
  }
}).subscribe(new SingleObserver<Integer>() {
  @Override public void onSubscribe(@NonNull Disposable d) {
    Log.i(TAG, "single onSubscribe: ");
  }

  @Override public void onSuccess(@NonNull Integer integer) {
    Log.i(TAG, "single onSuccess: " + integer);
  }

  @Override public void onError(@NonNull Throwable e) {
    Log.i(TAG, "single onError: ");
  }
});
```

Completable 发送一个单独的成功或异常信号

```java
Completable.create(new CompletableOnSubscribe() {
  @Override public void subscribe(@NonNull CompletableEmitter e) throws Exception {
      e.onComplete();
  }
}).subscribe(new CompletableObserver() {
  @Override public void onSubscribe(@NonNull Disposable d) {
    Log.i(TAG, "completable onSubscribe: ");
  }

  @Override public void onComplete() {
    Log.i(TAG, "completable onComplete: ");
  }

  @Override public void onError(@NonNull Throwable e) {
    Log.i(TAG, "completable onError: ");
  }
});
```

Maybe 可以发射0个或1个通知或错误的信号

```java
Maybe.create(new MaybeOnSubscribe<Integer>() {
  @Override public void subscribe(@NonNull MaybeEmitter<Integer> e) throws Exception {
    e.onSuccess(299);
  }
}).subscribe(new MaybeObserver<Integer>() {
  @Override public void onSubscribe(@NonNull Disposable d) {
    Log.i(TAG, "maybe onSubscribe: ");
  }

  @Override public void onSuccess(@NonNull Integer integer) {
    Log.i(TAG, "maybe onSuccess: " + integer);
  }

  @Override public void onError(@NonNull Throwable e) {
    Log.i(TAG, "maybe onError: ");
  }

  @Override public void onComplete() {
    Log.i(TAG, "maybe onComplete: ");
  }
});
```