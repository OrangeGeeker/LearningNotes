# 数据类型

## 数值

```dart
int a = 123; //int
double b = 1.23; //double
num c = 1; //int
num d = 1.1; //double

num x = 1;
x += 2.5; //double
```

int double 都是`[num][num]`子类, num 修饰的变量，既可以是 int 也可以是 double。

```dart
var x = 1;
var hex = 0xDEADBEEF;
var exponent = 8e5;
var y = 1.1;
var exponents = 1.42e5;
```

整形字面量必要时会自动转换成浮点数字面量。

```dart
double z = 1;
```

## 字符串

Dart 字符串包含了 UTF-16 编码的字符序列。

- 使用单引号或双引号定义字符串。
- 三个单引号或三个双引号，可以定义多行字符串。

```dart
String str = '1';
String str2 = "2";

String str3 = '''this is test
str
str
''';
String str4 = """this is test
str
str
""";
```

- 字符串插值 `${表达式}`

如果表达式结果是一个对象，则调用该对象的 toString 方法。

```dart
String str1 = 'hello';
String str2 = 'world';
print(str1 + str2);
print("$str1 $str2");
print("${1 + 2}");
print('${str1.toUpperCase()}');
```

以下输出均为 true

```dart
var s = 'string interpolation';

print('Dart has $s, which is very handy.' ==
    'Dart has string interpolation, ' + 'which is very handy.');
print('That deserves all caps. ' + '${s.toUpperCase()} is very handy!' ==
    'That deserves all caps. ' + 'STRING INTERPOLATION is very handy!');

// 代码中文解释
var s2 = '字符串插值';

print('Dart 有$s2，使用起来非常方便。' == 'Dart 有字符串插值，使用起来非常方便。');
print('使用${s2.substring(3, 5)}表达式也非常方便。' == '使用插值表达式也非常方便。');
```

- raw 字符串

字符串前面加上 r，不会被转义。

```dart
var s = r'在 raw 字符串中，转义字符串 \n 会直接输出 “\n” 而不是转义为换行。';
```

- 字符串字面量是一个编译时常量，只要是编译时常量都可以作为字符串字面量的插值表达式。

```dart
// 可以将下面三个常量作为字符串插值拼接到字符串字面量中。(These work in a const string.)
const aConstNum = 0;
const aConstBool = true;
const aConstString = 'a constant string';

// 而下面三个常量不能作为字符串插值拼接到字符串字面量。
var aNum = 0;
var aBool = true;
var aString = 'a string';
const aConstList = [1, 2, 3];

const validConstString = '$aConstNum $aConstBool $aConstString';
```

## 布尔

使用 bool 修饰，布尔类型有两个对象 true 和 false，两者都是编译时常量。

## 数组

```dart
var list = [1, 2, 3];
```

List 字面量前添加 const 关键字会创建一个编译时常量

```dart
var constantList = const [1, 2, 3];
// constantList[1] = 1;
```

### 扩展操作符 和 空感知扩展操作符

`(...)` 和 `(...?)`

```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
print(list2.lenth == 4);
```

如果扩展操作符右边可能为 null，可以使用 null-aware 扩展操作符来避免产生异常。

```dart
var list;
var list2 = [0, ...?list];
print(list2.length == 1);
```

### 集合中 if 和 集合中 for 操作

```dart
var nav = [
  'Home',
  'Furniture',
  'Plants',
  if (promoActive) 'Outlet'
];
```

```dart
var listOfInts = [1, 2, 3];
var listOfStrings = [
  '#0',
  for (var i in listOfInts) '#$i'
];
print(listOfStrings);
```

## Sets

Set 是无序集合

```dart
var a = {'a', 'b', 'c', 'd', 'e'};
var b = <String>{'a', 'b', 'c', 'd', 'e'};

var elements = <String>{};
elements.add('fluorine');
elements.addAll(a);

print(elements.length);
```

在 Set 字面量前添加 const 关键字创建一个 Set 编译时常量

```dart
final constantSet = const {
  'fluorine',
  'chlorine',
  'bromine',
  'iodine',
  'astatine',
};

constantSet.add('helium');
```
从 Dart 2.3 开始，Set 可以像 List 一样支持使用扩展操作符

## Maps

```dart
var gifts = {
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
}

var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

Map 构造器构造

```dart
var gifts = Map<String, String>();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = Map<int, String>();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';
```

### Symbols

Symbol 表示 Dart 中声明的操作符或者标识符。你几乎不会需要 Symbol，但是它们对于那些通过名称引用标识符的 API 很有用，因为代码压缩后，尽管标识符的名称会改变，但是它们的 Symbol 会保持不变。

可以使用在标识符前加 # 前缀来获取 Symbol：

#radix
#bar

Symbol 字面量是编译时常量。
