# 运算符

## 算术运算符

```dart
print(2 + 3 == 5);
print(2 - 3 == -1);
print(2 * 3 == 6);
print(5 / 2 == 2.5); // 结果是一个浮点数
print(5 ~/ 2 == 2); // 结果是一个整数
print(5 % 2 == 1); // 取余

print('5/2 = ${5 ~/ 2} r ${5 % 2}' == '5/2 = 2 r 1');
```

```dart
var a, b;

a = 0;
b = ++a; // 在 b 赋值前将 a 增加 1。
assert(a == b); // 1 == 1

a = 0;
b = a++; // 在 b 赋值后将 a 增加 1。
assert(a != b); // 1 != 0

a = 0;
b = --a; // 在 b 赋值前将 a 减少 1。
assert(a == b); // -1 == -1

a = 0;
b = a--; // 在 b 赋值后将 a 减少 1。
assert(a != b); // -1 != 0
```

## 关系运算符

```dart
assert(2 == 2);
assert(2 != 3);
assert(3 > 2);
assert(2 < 3);
assert(3 >= 3);
assert(2 <= 3);
```

```dart
bool identical(
Object? a,
Object? b
)
```

检查两个引用是否是同一对象。

## 类型判断运算符

as、is、is! 在运行时判断对象类型的运算符。

```dart
if (employee is Person) {
  // Type check
  employee.firstName = 'Bob';
}
```

## 赋值运算符

`??=`为值为 null 的变量赋值。

```dart
a = value;
b ??= value;
```

## 逻辑运算符

```dart
if (!done && (col == 0 || col == 3)) {
  // ...Do something...
}
```

## 按位和移位运算符

```dart
final value = 0x22;
final bitmask = 0x0f;

assert((value & bitmask) == 0x02); // 按位与 (AND)
assert((value & ~bitmask) == 0x20); // 取反后按位与 (AND NOT)
assert((value | bitmask) == 0x2f); // 按位或 (OR)
assert((value ^ bitmask) == 0x2d); // 按位异或 (XOR)
assert((value << 4) == 0x220); // 位左移 (Shift left)
assert((value >> 4) == 0x02); // 位右移 (Shift right)
```

## 条件表达式

根据布尔表达式确定赋值时，请考虑使用 ? 和 :。

`条件 ? 表达式 1 : 表达式 2`

```dart
var visibility = isPublic ? 'public' : 'private';
```

如果赋值是根据判定是否为 null 则考虑使用 ??。

`表达式 1 ?? 表达式 2`

```dart
String playerName(String? name) => name ?? 'Guest';
```

## 级联运算符

`.., ?..` 可以让你在同一个对象上连续调用多个对象的变量或方法。

```dart
var paint = Paint()
  ..color = Colors.black
  ..strokeCap = StrokeCap.round
  ..strokeWidth = 5.0;
```

```dart
querySelector('#confirm') // Get an object.
  ?..text = 'Confirm' // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
```

可以嵌套

```dart
final addressBook = (AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
```

## 其他运算符

`()`使用方法
`[]`访问 List
`.`访问成员
`?.`条件访问成员

?. 如果为null，返回null，否则返回调用
