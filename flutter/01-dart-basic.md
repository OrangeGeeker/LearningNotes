# Dart 基础

## 入口

```dart
main(){

}

void main(){

}
```

## 变量

var 定义变量，也可以使用类型定义变量

```dart
var str = 'hello dart';
print(str);

String strv = 'hello dart';
print(strv);

int myNum = 1234;
print(myNum);
```

## 常量

final 或 const 进行修饰

- final 惰性初始化，第一次使用前才初始化
- final 运行时常量

```dart
final name = 'jack';
final String nickname = 'ma';

const bar = 1000;
const dboule alt = 1.332;

final a = new DateTime.now();
//const b = new DateTime.now();

final b;
b = 2;
//const c;
//c = 2;
```

## 数据类型

var 定义的变量可以进行类型推导，但是定义后不可更改。

### 字符串

- 单引号或双引号定义字符串
- 三个单引号或三个双引号，可以定义多行字符串

```dart

String str = '1';
String str2 = "2";

String str3 = ''' this is test
str
str
''';
```