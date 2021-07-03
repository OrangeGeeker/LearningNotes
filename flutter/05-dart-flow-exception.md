# 流程控制语句 异常

## if

```dart
if (isRaining()) {
  you.bringRainCoat();
} else if (isSnowing()) {
  you.wearJacket();
} else {
  car.putTopDown();
}
```

## 循环

for 循环中的闭包会自动捕获循环的 索引值 以避免 JavaScript 中一些常见的陷阱

```dart
var message = StringBuffer('Dart is fun');
for (var i = 0; i < 5; i++) {
  message.write('!');
}
```

```dart
for (var candidate in candidates) {
  candidate.interview();
}
```

```dart
var collection = [1, 2, 3];
collection.forEach(print); // 1 2 3
```

```dart
while (!isDone()) {
  doSomething();
}
```

```dart
do {
  printLine();
} while (!atEndOfPage());
```

```dart
while (true) {
  if (shutDownRequested()) break;
  processIncomingRequests();
}
```

```dart
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}
```

```dart
candidates
    .where((c) => c.yearsExperience >= 5)
    .forEach((c) => c.interview());
```

## Switch Case

Switch 语句在 Dart 中使用 == 来比较整数、字符串或编译时常量，比较的两个对象必须是同一个类型且不能是子类并且没有重写 == 操作符。 枚举类型非常适合在 Switch 语句中使用。

Dart 中的 Switch 语句仅适用于有限的情况，比如使用解释器和扫描器的场景。

每个 case 子句都可以有局部变量且仅在该 case 语句内可见。

```dart
var command = 'OPEN';
switch (command) {
  case 'CLOSED':
    executeClosed();
    break;
  case 'PENDING':
    executePending();
    break;
  case 'APPROVED':
    executeApproved();
    break;
  case 'DENIED':
    executeDenied();
    break;
  case 'OPEN':
    executeOpen();
    break;
  default:
    executeUnknown();
}
```

空 case 允许 fall-through

```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED': // case 语句为空时的 fall-through 形式。
  case 'NOW_CLOSED':
    // case 条件值为 CLOSED 和 NOW_CLOSED 时均会执行该语句。
    executeNowClosed();
    break;
}
```

在非空 case 语句中想要实现 fall-through 的形式，可以使用 continue 语句配合 label 的方式实现:

```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
    executeClosed();
    continue nowClosed;
  // 继续执行标签为 nowClosed 的 case 子句。

  nowClosed:
  case 'NOW_CLOSED':
    // case 条件值为 CLOSED 和 NOW_CLOSED 时均会执行该语句。
    executeNowClosed();
    break;
}
```

## 断言

可以在条件表达式为 false 时使用 — assert(条件, 可选信息); — 语句来打断代码的执行

```dart
// 确保变量值不为 null (Make sure the variable has a non-null value)
assert(text != null);

// 确保变量值小于 100。
assert(number < 100);

// 确保这是一个 https 地址。
assert(urlString.startsWith('https'));
```

assert 的第二个参数可以为其添加一个字符串消息。

```dart
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

如何判断 assert 是否生效？assert 是否生效依赖开发工具和使用的框架：

- Flutter 在调试模式时生效。
- 一些开发工具比如 dartdevc 通常情况下是默认生效的。
- 其他一些工具，比如 dart 以及 dart2js 通过在运行 Dart 程序时添加命令行参数 --enable-asserts 使 assert 生效。

在生产环境代码中，断言会被忽略，与此同时传入 assert 的参数不被判断。

## 异常

### 抛出

Dart 提供了 Exception 和 Error 两种类型的异常以及它们一系列的子类，你也可以定义自己的异常类型。但是在 Dart 中可以将任何非 null 对象作为异常抛出而不局限于 Exception 或 Error 类型。

```dart
throw FormatException('Expected at least 1 section');
```

```dart
throw 'Out of llamas!';
```

因为抛出异常是一个表达式，所以可以在 => 语句中使用，也可以在其他使用表达式的地方抛出异常：

```dart
void distanceTo(Point other) => throw UnimplementedError();
```

### 捕获

可以使用 on 或 catch 来捕获异常，使用 on 来指定异常类型，使用 catch 来捕获异常对象，两者可同时使用。

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

如果 catch 语句没有指定异常类型则表示可以捕获任意异常类型

```dart 
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 指定异常
  buyMoreLlamas();
} on Exception catch (e) {
  // 其它类型的异常
  print('Unknown exception: $e');
} catch (e) {
  // // 不指定类型，处理其它全部
  print('Something really unknown: $e');
}
```

你可以为 catch 方法指定两个参数，第一个参数为抛出的异常对象，第二个参数为栈信息 StackTrace 对象

```dart
try {
  // ···
} on Exception catch (e) {
  print('Exception details:\n $e');
} catch (e, s) {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
```

关键字 rethrow 可以将捕获的异常再次抛出

```dart
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // 运行时错误
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    rethrow; // 允许调用者查看异常。
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
```

### Finally

无论是否抛出异常，finally 语句始终执行，如果没有指定 catch 语句来捕获异常，则异常会在执行完 finally 语句后抛出

```dart
try {
  breedMoreLlamas();
} finally {
  // 总是清理，即便抛出了异常。
  cleanLlamaStalls();
}
```

```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // 先处理异常。
} finally {
  cleanLlamaStalls(); // 然后清理。
}
```