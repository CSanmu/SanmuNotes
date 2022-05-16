[toc]

java8 是一个有里程碑的一个版本，提供了很多的新特性，但这些新特性是实打实有用的，而不是一些鸡肋

# Interface
## 接口新特性

java8 之前，往接口里新加一个方法，那么所有的实现类都需要变动，都需要同步实现这个方法。
java8 给接口新增了两个关键字：<font color=red>default</font> <font color=red>static</font>
使得可以往接口里添加默认方法，子类可以无需变动。

~~~java
interface InterfaceOne {

    /**
     * 实现类必须要实现的方法
     */
    void method1();

    /**
     * 接口方法，子类可以选择实现也可以选择不实现
     */
    default void method2() {
        System.out.println("this is InterfaceOne#method2");
    }

    /**
     * 静态方法，子类无需实现
     */
    static void method3() {
        System.out.println("this is InterfaceOne#method3");
    }
}

interface InterfaceTwo {

    /**
     * 接口方法，子类可以选择实现也可以选择不实现
     */
    default void method2() {
        System.out.println("this is InterfaceTwo#method2");
    }

    /**
     * 接口方法，子类可以选择实现也可以选择不实现
     */
    default void method3() {
        System.out.println("this is InterfaceTwo#method3");
    }

    default void method4() {
        System.out.println("this is InterfaceTwo#method4");
    }
}

public class InterfaceDemo implements InterfaceOne, InterfaceTwo {

    /**
     * 由于 method1 是 InterfaceOne 接口里的抽象方法，因此必须实现
     */
    @Override
    public void method1() {

    }

    /**
     * 由于 InterfaceOne InterfaceTwo 都有一个 default 方法 method2，因此也必须重新实现
     */
    @Override
    public void method2() {
        InterfaceOne.super.method2();
    }

    public static void main(String[] args) {
        InterfaceDemo demo = new InterfaceDemo();
        demo.method4();
        InterfaceOne.method3();
    }
}
~~~
如上代码块，InterfaceOne#method1 是一个抽象方法，因此 InterfaceDemo 实现类
必须重写该类，InterfaceOne 接口有一个 default method2 方法，InterfaceTwo 也有一个 default method2 方法
，InterfaceDemo 实现类不知道使用哪个，因此我们需要重写该方法。

其中，default 方法可以通过实现类的实例直接调用,这个 default 方法就很像继承特性，没有重写的话直接调用父类方法，
但是接口不是继承，而是实现，两者概念上还是有区别的。static 方法可以通过 **接口.方法** 直接调用，跟平时的静态方法一样

## 接口和抽象类的区别
这样看下来，接口和抽象类越来越像了，这样下去，抽象类会不会被淘汰呢？

其实抽象类和接口本质上还是不同的
+ 接口是一个更加抽象的概念，一个实现类可以实现多个接口，并且接口里的方法默认都是抽象方法，字段默认都是静态常量
+ 抽象类是一个父类，只能单继承，抽象类中的抽象方法必须手动指定，抽象类底层还是一个类

# Lambda



## 函数式接口

### 定义

函数式接口是指一个接口内只有一个抽象方法，若干个 default 方法的接口，一般函数式接口都会使用 **@FunctionalInterface** 注解进行标注，但是并不是说没有该注解就不是一个函数式接口了，该注解只是用于编译期检查，如果有多个抽象方法，那么编译将不同过。

先来看下怎么定义一个函数式接口：

```java
@FunctionalInterface
interface FunctionInterfaceOne {

    void methodOne(String msg);

}

public class FunctionInterfaceDemo {


    public static void main(String[] args) {
        // 匿名类
        FunctionInterfaceOne firstInstance = new FunctionInterfaceOne() {
            @Override
            public void methodOne(String msg) {
                System.out.println("this is " + msg);
            }
        };
        // lambda 表达式
        FunctionInterfaceOne secondInstance = msg -> System.out.println("this is " + msg);
        firstInstance.methodOne("firstInstance");
        secondInstance.methodOne("secondInstance");
    }
}
```

如上代码，**FunctionInterfaceOne** 就是一个函数式接口，在 main 方法中使用两种方式定义了一个 FunctionInterfaceOne 对象，一个是以前常用的匿名类形式，一个就是 java8 的 lambda 表达式。该函数式接口表达的是接受一个参数，并且不输出返回值。

目前我们常用的函数式接口主要有:

+ **BiConsumer<T,U>：**代表了一个接受两个输入参数的操作，并且不返回任何结果
+ **BiFunction<T,U,R>：**代表了一个接受两个输入参数的方法，并且返回一个结果
+ **BinaryOperator<T>：**代表了一个作用于于两个同类型操作符的操作，并且返回了操作符同类型的结果
+ **BiPredicate<T,U>**：代表了一个两个参数的boolean值方法
+ **BooleanSupplier：**代表了boolean值结果的提供方
+ **Consumer<T>：**代表了接受一个输入参数并且无返回的操作
+ **Function<T,R>：**接受一个输入参数，返回一个结果。
+ **Predicate<T>：**接受一个输入参数，返回一个布尔值结果。
+ **Supplier<T>：**无参数，返回一个结果。
+ java.lang.Runnable
+ java.util.concurrent.Callable
+ java.util.Comparator

除了几个 java8 前就有的复合函数式接口定义的接口，函数式接口大都定义在 java.utils.function 包下

## Lambda 实战

1. Lambda 表达式，也可称为闭包，它是推动 Java 8 发布的最重要新特性。

2. Lambda 允许把函数作为一个方法的参数（函数作为参数传递进方法中）,用代码来展示会更加清晰明了

~~~java
@FunctionalInterface
interface FunctionInterfaceOne {

    void methodOne(String msg);

}

public class FunctionInterfaceDemo {

    public void methodOne(FunctionInterfaceOne functionInterfaceOne) {
        functionInterfaceOne.methodOne("FunctionInterfaceDemo#methodOne");
    }

    public static void main(String[] args) {
        FunctionInterfaceDemo demo = new FunctionInterfaceDemo();
        demo.methodOne(msg -> System.out.println(msg));
    }
}
~~~

如上代码，又一个函数式接口 FunctionInterfaceOne，类 FunctionInterfaceDemo#methodOne 接收一个 FunctionInterfaceOne 类型的实例，在 main 方法中，直接通过 lambda 表达式（msg -> System.out.println(msg)）构建出来一个实例传入该方法中。按照以前的写法就是设计一个匿名内部类，传入该方法，或者为该接口定义一个实现类，然后生成一个实例，将实例作为参数传入 FunctionInterfaceDemo#methodOne 方法中。

3. 使用 Lambda 表达式可以使代码变的更加简洁紧凑。

### 语法

> (parameters) -> expression 
>
> 或 
>
> (parameters) ->{ statements; }

+ **可选类型声明：**不需要声明参数类型，编译器可以统一识别参数值。

> ```java
> () -> 5  // 不需要参数,返回值为 5 
> ```

+ **可选的参数圆括号：**一个参数无需定义圆括号，但多个参数需要定义圆括号。

> msg -> System.out.println(msg)
>
> (a,b) -> a+b

+ **可选的大括号：**如果主体包含了一个语句，就不需要使用大括号。

> ```java
> (a, b) -> {
>     System.out.println(a + b);
>     return a + b
> }
> ```

+ **可选的返回关键字：**如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定表达式返回了一个数值。

> (a,b) -> a+b
>
> 或
>
> (a,b) -> return a+b

# Stream

# Optional

# Date-Time