# 类

Dart 是支持基于 mixin 继承机制的面向对象语言，所有对象都是一个类的实例，而除了 Null 以外的所有的类都继承自 Object 类。

**基于 mixin 的继承**意味着尽管每个类（top class Object? 除外）都只有一个超类，一个类的代码可以在其它多个类继承中重复使用。

**扩展方法**是一种在不更改类或创建子类的情况下向类添加功能的方式。

## 使用类的成员

对象的成员由函数和数据组成。

使用`.`访问对象的实例变量或方法

```dart
var p = Point(2, 2);

// 获取 y 值
assert(p.y == 2);

// 调用变量 p 的 distanceTo() 方法。
double distance = p.distanceTo(Point(4, 4));
```

使用`?.`，可以避免 null 异常

```dart
// If p is non-null, set a variable equal to its y value.
var a = p?.y;
```