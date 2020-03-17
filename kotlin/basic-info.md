# Basic

## Basic Types

Kotlin 中，所有东西均为对象，因此可以调用任何变量的成员函数、属性

一些是内置的，因为他们的实现是优化过的，但是用户用起来就像普通的类

### Numbers

|Type|Bit width|
|-|-|
|Double|64|
|Float|32|
|Long|64|
|Int|32|
|Short|16|
|Byte|8|

#### 字面常量

|类别|例子|
|-|-|
|十进制|123|
|十六进制|0x0F|
|二进制|0b00001011|
|Long|123L|
|Double|123.5 123.5e10|
|Float|123.5F/f|

#### 下划线，让数字更易读

```
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

#### 表现

在 java 平台，数字作为原生类型物理地进行存储。如果我们需要一个 nullable 的引用或者泛型，会进行装箱

数字装箱后的不是同一个对象，但他们相等

```
val a: Int = 10000
print(a === a) // true
val boxedA: Int? = a
val boxedB: Int? = a
print(boxedA === boxedB) // false
print(boxedA == boxedB) // true
```

#### 显示转换

较小的类型不能隐式转换为较大的类型，必须进行显式转换

```
val a: Int? = 1 // 一个装箱的 Int
val b: Long? = a // 隐式转换成装箱的 Long

val b: Byte = 1
val i: Int = b //错误

val i: Int = b.toInt() //正确，显式转换
```

每个数字类型支持如下转换：

- toByte(): Btye
- toShore(): Short
- toInt(): Int
- toLong(): Long
- toFloat(): Float
- toDouble(): Double
- toChar(): Char

弃用隐式转换没有明显的影响，因为类型会从上下文推断出来，并且数学操作符会通过合适的转换进行重载

```
val l = 1L + 3 // Long + Int => Long
```

#### 运算

[Operator overloading](http://kotlinlang.org/docs/reference/operator-overloading.html)

运算被定义为相应的类成员，编译器会将函数调用优化为相应的指令

对于位运算，没有特殊字符来表示，只可用‘中缀式’调用相应方法

```
val x = (1 shl 1) and 0x000FF000
```

位运算列表，只能用于 Int Long

- shl(bits) 有符号左移 <<
- shr(bits) 有符号右移 >>
- ushr(bits) 无符号右移 >>>
- and(bits) 位与
- or(bits) 为或
- xor(bits) 位异或
- inv() 为非

### 字符

### 布尔

### 数组

### 字符串
