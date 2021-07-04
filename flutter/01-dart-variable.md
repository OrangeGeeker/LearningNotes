# 变量

## 程序入口

```dart
void main(){
  print('Hello World!');
}
```

## 重要概念

- 所有变量引用的都是对象，每个对象都是一个类的实例。
- 数字、函数、null 都是对象，除了 null 以外，所有类都继承于 `[Object][]` 类。
- Dart 是强类型语言，但其支持类型推断，因此可以不用显示声明变量类型。
- 如果启用空安全，变量未声明为可空类型时，不能为 null。可以在类型后加上`?`将类型声明为可空。表达式后加`!`来断言表达式不为空（为空时抛出异常）。
- 如果想要显示声明允许任意类型，使用`Object?`、`Object`、或特殊类型`dynamic`将检查延迟到运行时进行。
- Dart 支持泛型。
- Dart 支持顶级函数，同时还支持定义属于类或对象的函数。还可以在函数中定义函数。
- Dart 没有访问限定符，如果一个标识符以下划线开头，则表示该标识符在库内是私有的。
- 标识符可以以字母或下划线开头
- Dart 表达式有值，语句没有值。
- Dart 工具可以显示 警告 和 错误 两种类型的问题。警告表明代码可能有问题但不会阻止其运行。错误分为编译时错误和运行时错误；编译时错误代码无法运行；运行时错误会在代码运行时导致 异常。

## 变量

- 变量仅存储对象的引用。
- 如果一个对象的引用不局限于单一的类型，可以将其指定为`Object`或`dynamic`类型。

```dart
var str = 'hello dart';
String str2 = 'hello dart';
int myNum = 1234;
Object name = 'Bob';
```

风格建议指南中建议，通过`var`声明局部变量而非使用指定的类型。

## 空安全

### 空安全原则

- 默认不可空。除非将变量显示声明为可空，否则一定时非空类型。
- 渐进迁移。可以自由选择何时迁移，多少代码迁移。可以使用混合模式的空安全，在一个项目中同时使用空安全和非空安全的代码。
- 完全可靠。Dart 的空安全时非常可靠的，编译器包含了很多优化。如果类型系统推断出某个变量不为空，那么它永远不为空。当你将整个项目和其依赖完全迁移至空安全后，你会享有健全性带来的所有优势——更少的BUG、更小的二进制文件以及更快的执行速度。

### 启用

2.13 以前的版本创建的项目，尚未迁移至空安全，创建后需要迁移。

通过 SDK 限制，例如`pubspec.yaml`设置

```
environment:
  sdk: ">=2.12.0 <3.0.0"
```

### 迁移

[https://dart.cn/null-safety/migration-guide](https://dart.cn/null-safety/migration-guide)

## 默认值

- 未初始化的变量默认值为`null`如果未迁移至空安全，所有变量都为可空类型）。即使数字也是如此。

```
int? lineCount;
assert(lineCount == null);
```

`assert()`的调用将会在生产环境的代码中被忽略掉。在开发过程中，`assert(condition)将会在条件判断为 false 时抛出一个异常。详情请查阅 Assert。

- 如果启用了空安全，则在使用前必须初始化变量值。

```dart
int line = 1;

int lineCount;
if(weLikeToCount) {
  lineCount = countLines();
}else{
  lineCount = 0;
}

print(lineCount);
```

## late 修饰符

- 声明一个延迟初始化的非空变量。
  
对于启用了空安全，声明非空变量缺没有在声明时初始化，有时 Dart 不能在使用变量前，推断出变量的值是非空的。例如顶级变量和实例变量。

如果你确定变量使用前会进行赋值，可以通过`late` 关键字来不让 Dart 报错。运行时如果没有初始化，则会抛出异常。

```dart
late String desc;

void main() {
  desc = "final string";
  print(desc);
}
```

- 延迟初始化变量。
  - 变量可能不需要，或者初始化的花费很高。
  - 初始化一个实例变量，但它的初始化需要访问`this`。

如果在`late`修饰的变量声明时，进行了初始化，那么初始化的代码会在变量第一次使用时执行，这种延迟初始化的方式在一些情况下非常有用。


## 常量

常量使用 final 或 const 进行修饰，不可修改。

- final 是运行时常量，const 是编译时常量。
- final 惰性初始化，第一次使用前才初始化。
- 实例变量可以是 final 但不可以是 const。
- 如果使用 const 修饰类中的变量，必须加上 static 在前面。

```dart
final name = 'jack';
final String nickname = 'bob';

const bar = 1000;
const dboule alt = 1.332 * bar;

final a = new DateTime.now();
//const b = new DateTime.now();

final b;
b = 2;
//const c;
//c = 2;
```
- const 还可以用来创建常量值，该常量值可以赋值给任何变量。也可以将构造函数声明为 const，这种类型的构造函数创建的对象是不可改变的。

```dart
var foo = const [];
final bar = const [];
const baz = [];//相当于 const[]
```

可以在常量中使用类型检查和强制类型转换 `is` `as`，集合中的 if 以及展开操作符

```dart
const Object i = 3;
const list = [i as int];
const map = {if (i is int) i: 'int'};
const set = {if (list is List<int>) ...list};
```
