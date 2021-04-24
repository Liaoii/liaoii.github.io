---
title: "接口"
catalog: true
date: 2021-02-09 09:16:15
subtitle:
header-img:
tags:
- JavaSE
---

### 为什么需要接口

- 提供一种将接口与实现分离的更加结构化的方法
- 可以进行多重继承，使其能够向上转型为多个基类型
- 防止客户端程序员创建该接口的对象

### 抽象类和抽象方法

> 抽象类是介于普通类与接口之间的一种中庸之道。

**为什么需要抽象类和抽象方法**

基类存在的目的是为了给它的所有导出类创建一个通用接口，不同的子类可以用不同的方式表示此接口。通用接口建立起一种基本形式，以此表示所有导出类的共同部分。

此时为基类中的方法提供具体实现意义并不大，因此可以将方法标记为抽象方法，抽象方法表示方法是不完整的，仅有声明而没有方法体。抽象方法声明方式如下：

abstract void method();

包含抽象方法的类叫做抽象类，如果一个类包含一个或多个抽象方法，该类必须被限定为抽象的，否则编译器会报错。如果一个抽象类不完整，那么产生它的对象也没有意义，并且为抽象类创建对象是不安全的，所以编译器也强制要求不能创建抽象类的对象，否则就会报错。

如果从一个抽象类继承，并想创建该新类的对象，那么就必须为基类中的所有抽象方法提供方法定义。如果不这样做，那么导出类也得是抽象类，编译器会要求强制使用abstract关键字。

**没有任何抽象方法的抽象类**

如果有一个类，让其包含任何abstract方法都显得没有实际意义，而且也想要阻止产生这个类的任何对象，就可以将类声明为abstract，但不包含任何抽象方法。

**使用抽象类和抽象方法的好处**

使用抽象类可以使类的抽象性明确起来，并告诉用户和编译器打算怎样来使用它们。抽象类还是可以起到很有用的重构作用，因为它们使得我们可以很容易地将公共方法沿着继承层次结构向上移动。

### 接口

interface关键字使抽象的概念更向前迈进了一步。在抽象类中既可以包含抽象方法，也可以包含提供具体实现的方法。interface关键字产生一个完全抽象的类，接口中没有任何具体的实现。接口允许创建者确定方法名、参数列表和返回类型，但是没有任何方法体。接口只提供了形式，而未提供任何具体实现。接口被用来建立类与类之间的协议。

interface不仅仅是一个极度抽象的类，它允许通过创建一个能够被向上转型为多种基类的类型来实现某种类似多重继承变种的特性。

**接口的特点**

- 接口中的方法都隐式或显示的被设定为public
- 可以在接口中定义域，这些域默认都是static和final的，但是可以进行手动权限控制。

### Java中的多重继承

接口不仅仅只是一种更纯粹形式的抽象类，它的目标比这要高。因为接口是根本没有任何具体实现的，也就是说，没有任何与接口相关的存储，因此，也就无法阻止多个接口的组合。组合多个类的接口的行为被称作多重继承。

在导出类中，不强制要求必须有一个是抽象的或具体的基类。如果要从一个非接口的类继承，那么只能从一个类去继承。其余的基元素都必须是接口。需要将所有的接口名都置于implements关键字之后，用逗号将它们一一隔开。可以继承任意多个接口，并可以向上转型为每个接口，因为每一个接口都是一个独立类型。

```java
interface CanFight {
    void fight();
}
interface CanSwim {
    void swim();
}
interface CanFly {
    void fly();
}
class ActionCharacter {
    public void fight() {}
}
class Hero extends ActionCharacter implements CanFight, CanSwim, CanFly {
    public void swim() {}
    public void fly() {}
}
```

如果基类中与接口中存在相同的方法定义，会出现什么情况。此时基类中的方法会自动作为对接口中方法的实现。

### 抽象类与基类如何选择

使用接口的核心原因是为了能够向上转型为多个基类型。使用接口的第二个原因与使用抽象类相同，可以防止客户端程序员创建该接口的对象，并确保这仅仅是建立一个接口。

要如何在抽象类和接口中进行选择，如果要创建不带任何方法定义和成员变量的基类，那么就应该选择接口而不是抽象类。如果知道某事物应该成为一个基类，那么第一选择应该是使它成为一个接口。

### 接口也可以进行继承

通过继承，可以很容易地在接口中添加新的方法声明，还可以通过继承在新接口中组合数个接口。这两种情况都可以获得新的接口。

一般情况下，只可以将extens用于单一类，但是可以引用多个基类接口，只需用逗号将接口名一一分隔开即可。

```java
interface Monster {
    void menace();
}
interface DangerousMonster extends Monster {
    void destroy();
}
interface Lethal {
    void kill();
}
class DragonZilla implements DangerousMonster {
    public void menace() {}
    public void destroy() {}
}
interface Vampire extends DangerousMonster, Lethal {
    void drinkBlood();
}
class VeryBadVampire implements Vampire {
    public void menace() {}
    public void destroy() {}
    public void kill() {}
    public void drinkBlood() {}
}
```

### 组合接口时出现的名字冲突

当覆盖、实现、重载混合在一起时，就有可能产生冲突。最好的方法是将基类和接口的所有方法放在一起按重载的规则进行判断，如果有方法签名相同而返回值不同的多个方法就会产生冲突。

```java
interface I1 {
    void f();
}
interface I2 {
    int f(int i);
}
interface I3 {
    int f();
}
class C {
    public int f() {
        return 1;
    }
}
//class C2 extends C implements I1 {}
//interface I4 extends I1, I3 {}

```

### 接口中的域

接口中的任何域都自动是public static final的，所以接口就成为了一种很便捷的用来创建常量组的工具。可以用接口来产生与enum具有相同效果的类型。Java中标识具有常量初始化值的static final时，会使用大写字母的风格，并使用下划线来分隔多个单词。

在接口中定义的域不能是空final，但是可以被非常量表达式初始化。

```java
public interface RandVals {
    Random RAND = new Random();
    int RANDOM_INT = RAND.nextInt();
    long RANDOM_LONG = RAND.nextLong() * 10;
    float RANDOM_FLOAT = RAND.nextLong() * 10;
    double RANDOM_DOUBLE = RAND.nextDouble() * 10;
}
```

接口中的域在接口第一次被加载时就完成了初始化。

接口中域的值被存储在该接口的静态存储区域内。

### 嵌套接口

**在类中嵌套接口**

```java
class Out {
    private interface A {}
    interface B {}
    protected interface C {}
    public interface D {}

    private class AImpl1 implements A {}
    class AImpl2 implements A {}
    protected class AImpl3 implements A {}
    public class AImpl4 implements A {}
    class BImpl implements B {}
    protected class CImpl implements C {}
    public class DImpl implements D {}
    
    public A getA() {
        return new AImpl4();
    }
    private Out.A aRef;
    public void receiveA(Out.A a) {
        aRef = a;
    }
}
public class NestingInterfaces {
    private class BImpl implements Out.B {}
    class CImpl implements Out.C {}
    protected class DImpl implements Out.D {}
}
```

非嵌套的类与接口只能用public和包访问权限来进行修饰，但是在类中嵌套的类或者接口可以指定为任何访问权限。

嵌套的接口虽然是private的，但是该private接口却可以被实现为其他任何访问权限的类，但是该类不管是什么访问权限，都只能在它所在的类中被使用。即使某个方法返回一个private接口的引用，该引用也无法被使用，只有将该引用交给有权使用它的对象才行。private接口可以强制该接口中的实现类不允许向上转型。

**在接口中嵌套接口**

```java
interface OutInterface {
    interface A {}
    //private interface B {}
}
```

在接口中嵌套的接口只能是public的。