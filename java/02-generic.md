# 泛型

1.5 引入，本质是参数化类型，即变量的类型是一个参数，使用时在指定为具体类型

## 泛型类

- 类名后加一个尖括号，括号里写泛型变量，作为泛型的声明
- 泛型变量可以有多个
- 泛型类的声明，需要加上泛型变量

```java
class Point<T> {
  private T x;
  private T y;

  public void setX(T x){
    this.x = x;
  }

  public void getX(){
    return this.x;
  }
}

Point<Integer> p = new Point<>;
p.setX(100);
```

## 泛型接口

非泛型类需要定义具体的泛型，重写该泛型的方法

泛型类只需要定义同一个泛型变量

## 泛型方法

在返回值前声明泛型变量

返回值也可以替换成泛型

## 类型擦除

java 的泛型只存在于编译期

```java
public class ErasedTypeEquivalence {
    public static void main(String[] args) {
        Class c1 = new ArrayList<String>().getClass();
        Class c2 = new ArrayList<Integer>().getClass();
        System.out.println(c1 == c2);
    }
}
```

两个 list 的参数类型不一样，运行后，代码输出是 true，说明 JVM 当作同一个类，而在 C++ C# 中，他们就是不同的类

泛型参数会擦除到它的第一个边界，比如 Point 类，就会擦出到 Object，存入的变量是以 Object 类型处理，取出的时候编译器自动插入转型代码，不需要我们的手动转型

### 创建对象

```java
public class Erased<T> {
    private final int SIZE = 100;
    public static void f(Object arg) {
    if(arg instanceof T) {} // Error
    T var = new T(); // Error
    T[] array = new T[SIZE]; // Error
    T[] array = (T)new Object[SIZE]; // Unchecked warning
    }
}
```

任何在运行期需要知道确切类型的代码都无法工作

如上，无法创建对象。可以使用工厂或者模板方法来生成对象

## 泛型数组

Java 中不允许直接创建泛型数组

`Generic<Integer> ga = new Generic<Integer>[]`

`T[]`

由于擦除，运行期的数组类型只能是 Object[]，如果我们立即把它转型为 T[]，那么在编译期就失去了数组的实际类型，编译器也许无法发现潜在的错误。

因此，更好的办法是在内部最好使用 Object[] 数组，在取出元素的时候再转型。看下面的例子：

```java
public class GenericArray2<T> {
    private Object[] array;
    public GenericArray2(int sz) {
        array = new Object[sz];
    }
    public void put(int index, T item) {
        array[index] = item;
    }
    @SuppressWarnings("unchecked")
    public T get(int index) { return (T)array[index]; }
    @SuppressWarnings("unchecked")
    public T[] rep() {
        return (T[])array; // Warning: unchecked cast
    }
    public static void main(String[] args) {
        GenericArray2<Integer> gai =
        new GenericArray2<Integer>(10);
        for(int i = 0; i < 10; i ++)
        gai.put(i, i);
        for(int i = 0; i < 10; i ++)
        System.out.print(gai.get(i) + " ");
        System.out.println();
        try {
            Integer[] ia = gai.rep();
        } catch(Exception e) { System.out.println(e); }
    }
}
```

### 类型标识

使用 Class 对象作为类型的标识是更好的设计

```java
public class GenericArrayWithTypeToken<T> {
    private T[] array;
    @SuppressWarnings("unchecked")
    public GenericArrayWithTypeToken(Class<T> type, int sz) {
        array = (T[])Array.newInstance(type, sz);
    }
    public void put(int index, T item) {
        array[index] = item;
    }
    public T get(int index) { return array[index]; }
    // Expose the underlying representation:
    public T[] rep() { return array; }
    public static void main(String[] args) {
        GenericArrayWithTypeToken<Integer> gai =
        new GenericArrayWithTypeToken<Integer>(
        Integer.class, 10);
        // This now works:
        Integer[] ia = gai.rep();
    }
}
```

在构造器中传入了 `Class<T>` 对象，通过 `Array.newInstance(type, sz)` 创建一个数组，这个方法会用参数中的 Class 对象作为数组元素的组件类型。

这样创建出的数组的元素类型便不再是 Object，而是 T。

这个方法返回 Object 对象，需要把它转型为数组。不过其他操作都不需要转型了，包括 rep() 方法，因为数组的实际类型与 T[] 是一致的。

这是比较推荐的创建泛型数组的方法。

## 边界类型

`<T extends BoundingType>` BoundingType 可以是类，也可以是接口

此处的 extends 表示的是子类型，不是继承，T 是在 BoundingType 基础上创建的

多个边界，用 & 连接

```java
public interface Comparable<T> {
    public boolean compareTo(T i);
}

//添加上extends Comparable之后，就可以Comparable里的函数了
public static <T extends Comparable>  T min(T...a){
    T smallest = a[0];
    for(T item:a){
        if (smallest.compareTo(item)){
            smallest = item;
        }
    }
    return smallest;
}
```

## 通配符

### 无边界通配符 `?`

用于填充类型参数 T

```java
Point<?> point;
point = new Point<Integer>(3,3);
point = new Point<Float>(4.3f,4.3f);
point = new Point<Double>(4.3d,4.90d);
point = new Point<Long>(12l,23l);
```

```java
Point<? extends Number> point;
point = new Point<Integer>(3,3);
point = new Point<Float>(4.3f,4.3f);
point = new Point<Double>(4.3d,4.90d);
point = new Point<Long>(12l,23l);
```

### 通配符限定

#### 通配符的 extends 绑定，表示填充为 XXX 的子类

```java
Point<? extends Number> point;
point = new Point<Float>();
point = new Point<Number>();
point = new Point<String>(); //错误 因为 String 不是 Number 的子类
```

只能取值，不能添加

```java
Point<? extends Number> point;
point = new Point<Integer>(2);
Number n = point.getX();
point.setX(new Integer(222)); //错误
```

通配符 `<? extends Number>` 表示某种特定类型，但是不关心实际类型是什么，此时 `setX(T a)` 就变成了 `setX(? extends Number)`

point 的泛型变量 T 类型是 `<? extends Number>`，Number 是它的上边界，但是我们不知道其持有的具体类型，是未知的，因此无法 set 值，唯一可以添加的是 null，但是没有意义

如果返回 T 类型，虽然我们不知道实际类型，但是我们知道它肯定是 Number 的子类，因此肯定能转型成 Number

#### 通配符的 super 绑定，表示填充为 XXX 的父类

```java
class CEO extends Manager {
}
class Manager extends Employee {
}
class Employee {
}
```

```java
List<? super Manager> list; // 将 T 填充为任意 Manager 的父类
list = new ArrayList<Employee>();
list = new ArrayList<Manager>();
list = new ArrayList<CEO>(); //编译错误，因为 CEO 不是 Manager 的父类
```

只能添加，不能取值

```java
List<? super Manager> list = new ArrayList<Employee>();
//存
list.add(new Employee()); //编译错误
list.add(new Manager());
list.add(new CEO());

Employee item = list.get(0); //错误
```

list 类型是由 `List<? super Manger>`决定的，即 Manager 的任意父类，但是是哪一个父类，并不能确定，因此 add Employee 是错误的，因为不能确定 Employee 是 `<? super Manger>` 的父类

list 取值的时候，item 的类型是 `<? super Manger>`，

#### 总结

- 如果你想从一个数据类型里获取数据，使用 ? extends 通配符（能取不能存）
- 如果你想把对象写入一个数据结构里，使用 ? super 通配符（能存不能取）
- 如果你既想存，又想取，那就别用通配符。

构造泛型实例时，如果省略了填充类型，则默认填充为无边界通配符！