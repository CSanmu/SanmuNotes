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

# Stream

# Optional

# Date-Time