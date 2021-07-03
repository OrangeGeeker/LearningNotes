# 函数

函数的类型是 Function。

```dart
bool isNoble(int atomicNumber) {
  return _nobleGases[] != null;
}
```

箭头函数：函数体只包含一个表达式

`bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;`

## 参数

**必要参数**定义在参数列表前面，**可选参数**定义在必要参数后面。

**可选参数**可以是**命名的**或**位置的**。

### 命名参数

命名参数默认是可选参数，除非标记为 required。

调用函数时，可以使用`参数名: 参数值`的形式来指定命名参数。

```dart
enableFlags(bold: true, hidden: false);
```

定义函数时，使用`{参数1， 参数2}`来指定命名参数。

```dart
void enableFlags({bool? bold, bool? hidden}){
}
```

如果函数是可选参数并且不能为 null。

虽然命名参数是可选参数的一种类型，但是你仍然可以使用 required 来标识一个命名参数是必须的参数，此时调用者必须为该参数提供一个值。

```dart
const Scrollbar({Key? key, required Widget child})
```

### 位置参数

使用 `[]` 将一系列参数包裹起来作为位置参数。

```dart
void say(String from, String msg, [String? device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}

say('Bob', 'Howdy');

say('Bob', 'Howdy', 'smoke signal');
```

### 默认参数

用`=`为函数的命名参数和位置参数定义默认值，默认值必须为编译时常量，没有指定时，默认值为 null。

```dart
void enableFlags({bool bold = false, bool hidden = false}) {
}

enableFlags(bold: true);
```

```dart
String say(String from, String msg, [String device = 'carrier pigeon']) {
  var result = '$from says $msg with a $device';
  return result;
}

print(say('Bob', 'Howdy'));
```

```dart
void doStuff(
    {List<int> list = const [1, 2, 3],
    Map<String, String> gifts = const {
      'first': 'paper',
      'second': 'cotton',
      'third': 'leather'
    }}) {
  print('list: $list');
  print('gifts: $gifts');
}

doStuff();
```

## main() 函数

main 函数的返回值为 void 并且有一个 `List<String>` 类型的可选参数。

```dart
// 使用命令 dart args.dart 1 test 运行该应用
void main(List<String> arguments) {
  print(arguments);

  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
```

```dart
void main() {
  print('Hello World');
}
```

## 函数时一级对象

函数可以作为参数传递给另一个函数。

```dart
void printElement(int element) {
  print(element);
}

var list = [1, 2, 3];

list.forEach(printElement);
```

```dart
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
print(loudify('hello'));
```

## 匿名函数

```
([[类型] 参数[, …]]) {
  函数体;
};
```

```dart
const list = ['apples', 'bananas', 'oranges'];
list.forEach((item) {
  print('${list.indexOf(item)}: $item');
});
```

如果函数体内只有一行返回语句，你可以使用胖箭头缩写法。

```dart
list.forEach(
    (item) => print('${list.indexOf(item)}: $item'));
```

## 词法作用域

```dart
bool topLevel = true;

void main() {
  var insideMain = true;

  void myFunction() {
    var insideFunction = true;

    void nestedFunction() {
      var insideNestedFunction = true;

      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNestedFunction);
    }
  }
}
```

## 词法闭包

**闭包**即一个函数对象，即使函数对象的调用在它原始作用域之外，依然能够访问在它词法作用域内的变量。

函数可以封闭定义到它作用域内的变量。

```dart
// 返回一个将 [addBy] 添加到该函数参数的函数。
Function makeAdder(int addBy) {
  return (int i) => addBy + i;
}

void main() {
  // 生成加 2 的函数。
  var add2 = makeAdder(2);

  // 生成加 4 的函数。
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```

## 测试函数是否相等

```dart
void foo() {} // 定义顶层函数 (A top-level function)

class A {
  static void bar() {} // 定义静态方法
  void baz() {} // 定义实例方法
}

void main() {
  Function x;

  // 比较顶层函数是否相等。
  x = foo;
  assert(foo == x);

  // 比较静态方法是否相等。
  x = A.bar;
  assert(A.bar == x);

  // 比较实例方法是否相等。
  var v = A(); // A 的实例 #1
  var w = A(); // A 的实例 #2
  var y = w;
  x = w.baz;

  // 这两个闭包引用了相同的实例对象，因此它们相等。
  assert(y.baz == x);

  // 这两个闭包引用了不同的实例对象，因此它们不相等。
  assert(v.baz != w.baz);
}
```
## 返回值

所有的函数都有返回值。没有显示返回语句的函数最后一行默认为执行 return null;。

```dart
foo() {}

assert(foo() == null);
```