# Dart 介绍

## 定义类

```java
class Bicycle{
    int cadence;
    int speed;
    int gear;
}

void main(){
}
```

- 主函数是 main() 或者 main(List<String> args)
- Dart 可以将函数、变量、定义在类外部。main 函数级别做高
- 默认都是 public，Dart 没有 public private protected 关键字

## 构造

`Bicycle(this.cadence, this.speed, this.gear);`

- 构造方法可以没有方法体
- 无方法体的构造后，如果没有分号会报错，提示“必须提供方法体”
- 构造方法中使用 this，来引用实例的变量

```java
Bicycle(this.cadence, this.speed, this.gear);

Bicycle(int cadence, int speed, int gear){
    this.cadence = cadence;
    this.speed = speed;
    this.gear = gear;
}
```

## 实例化

```java
void main(){
    var bike = Bicycle(2, 0, 1);
    print(bike);
}
```

- new 关键字可以省略
- 如果不想让变量改变，var 关键字之前添加 final

## toString

```java
@override
String toString() => 'Bicycle: $speed mph';
```

- `@override` 注解，重写
- Dart 支持 单引号和双引号 来定义字符串
- 字符串插入表达式，将值插入到字符串中，`${expression}`
- 如果表达式是一个标识符，可以省略括号，`$variableName`
- 单行函数使用 fat arrow `=>` 标注

## 添加只读变量

添加下划线，将变量变为只读变量。同时添加一个 getter 方法

```java
class Bicycle {
  int cadence;
  int _speed;
  int gear;

  Bicycle(int cadence, int gear);
  
  int get speed => _speed;

  @override
  String toString() => 'Bicycle: $speed mph';
}

void main() {
  var bike = Bicycle(2, 1);
  print(bike);
}
```

- 未初始化的变量，值是 null，包括数字类型
- 下划线前缀的标识符均为私有类型
- Dart 默认给 public 的实例变量，提供隐含的 getters 和 setters。除非想要将变量强制变为只读/只写的，计算或验证一个值，或在其他地方更新一个值，才需要定义自己写的 getters setters

## 可选参数

```
import 'dart:math';

class Rectangle {
  int width;
  int height;
  Point origin;

  Rectangle({this.origin = const Point(0, 0), this.width = 0, this.height = 0});

  @override
  String toString() =>
      'Origin: (${origin.x}, ${origin.y}, width: $width), height: $height';
}

main() {
  print(Rectangle(origin: const Point(10, 20), width: 100, height: 200));
  print(Rectangle(origin: const Point(10, 10)));
  print(Rectangle(widht: 200));
  print(Rectangle());
} // Included main() to suppress uncaught exception.
```

- `this.origin, this.width, this.height` 使用了简化方式在构造方法中给变量赋值
- 这三个是可选命名变量，命名变量使用大括号括起来
- `this.origin = const Point(0,0)` 语法指定了变量默认的值。指定默认必须是编译时的常量
- 在 Dart 中，一个构造方法即可实现重载

## 创建工厂

工厂模式，比直接创建实例有许多优势，例如隐藏实例化细节，提供返回子类型，返回已经存在的实例。

```
import 'dart:math';

abstract class Shape {
  num get area;
}

class Circle implements Shape {
  final num radius;
  Circle(this.radius);
  num get area => pi * pow(radius, 2);
}

class Square implements Shape {
  final num side;
  Square(this.side);
  num get area => pow(side, 2);
}

main() {
  final circle = Circle(2);
  final square = Square(2);
  print(circle.area);
  print(square.area);
}
```

- Dart 支持抽象类
- 可以在一个文件中定义多个类
- dart.math 是核心库的一个
- Dart 2 中，库的常量是小写的，如 `pi`
- 代码中两个 getters 计算了值

### 创建 top-level 方法

在任何 class 之外

```
Shape shapeFactory(String type) {
  if (type == 'circle') return Circle(2);
  if (type == 'square') return Square(2);
  throw 'Can\'t create $type.';
}

```

在 main 方法中换成以下

```
final circle = shapeFactory('circle');
final square = shapeFactory('square');
```

- 如果调用方法使用的是其他字符串，那么会抛出异常
- 可以继承 Exception 类来自定义异常，或者抛出一个字符串来描述问题，如例子中
- 当一个异常发成，DartPad 回报高，可以把代码包裹在 try-catch 中进行，并打印异常。

### 创建一个工厂构造方法

给 Shape 类添加构造方法

```
abstract class Shape {
  factory Shape(String type) {
    if (type == 'circle') return Circle(2);
    if (type == 'square') return Square(2);
    throw 'Can\'t create $type.';
  }
  num get area;
}
```

## 实现接口

Dart 没有 interface 关键字，每个类都定义了一个接口

```
class CircleMock implements Circle {
  num area;
  num radius;
}
```

## 函数编程

- 将方法当作参数传递
- 将方法赋值给变量
- 将多参数的方法变为多个单参数的方法
- 创建没有名字的方法，作为常量（lambda 表达式）

```
String scream(int length) => "A${'a' * length}h!";

main(){
    final values = [1,2,3,5,10];
    for(var length in values){
        print(scream(length));
    }
}
```

- 字符串使用 `*`，代表字符串重复多少次

```
values.map(scream).forEach(print);
```

```
values.skip(1).take(3).map(scream).forEach(print);
```
