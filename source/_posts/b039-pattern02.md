---
title: "策略模式（Strategy）"
catalog: true
date: 2021-03-02 17:03:37
subtitle:
header-img:
tags:
- Design Patterns
---

### 策略模式

策略模式定义了算法族，分别封装起来，让它们之间可以互相替换，此模式让算法的变化独立于使用算法的客户。

### 相关设计原则

1. 找出应用中可能需要变化之处，把它们独立出来，不要和那些不需要变化的代码混在一起
2. 针对接口编程，而不是针对实现编程
3. 多用组合，少用继承

### 使用继承来进行设计

Duck类是所有鸭子的基类，具有共同的行为quack()和swim()，由于不同品种的鸭子具有不同的样子，所以display()设计成抽象方法。现有三个导出类MallardDuck、RedheadDuck、RubberDuck。

```java
//父类
public abstract class Duck {
    public void quack() {
        System.out.println("呱呱");
    }
    public void swim() {
        System.out.println("鸭子可以游泳");
    }
    //不同的鸭子其样子也不同
    public abstract void display();
}
//野鸭
public class MallardDuck extends Duck{
    public void display() {
        System.out.println("头是绿色的");
    }
}
//红头鸭子
public class RedheadDuck extends Duck {
    public void display() {
        System.out.println("头是红色的");
    }
}
//橡胶鸭子
public class RubberDuck extends Duck {
    public void display() {
        System.out.println("全身都是黄色的");
    }
    //覆盖了叫声
    public void quack() {
        System.out.println("吱吱吱");
    }
}
```

### 在基类中进行扩展

为鸭子扩展会飞的行为

```java
public abstract class Duck {
    public void quack() {
        System.out.println("呱呱");
    }
    public void swim() {
        System.out.println("鸭子可以游泳");
    }
    //不同的鸭子其样子也不同
    public abstract void display();
    public void fly() {
        System.out.println("鸭子会飞了");
    }
}
```

在基类中添加新的方法会使所有导出类都具备新行为。但是某些特殊的导出类并不适合该行为。比如RubberDuck就不会飞，此时可是在RubberDuck类覆盖fly()方法来解决这个问题。

```java
//橡胶鸭子
public class RubberDuck extends Duck {
    public void display() {
        System.out.println("全身都是黄色的");
    }
    public void quack() {
        System.out.println("吱吱吱");
    }
    public void fly() {
        System.out.println("我不可以飞的");
    }
}
```

如果要添加一个新的导出类，就得考虑是否需要覆盖基类中的某些方法。

```java
//诱饵鸭
public class DecoyDuck extends Duck {
    public void display() {
        System.out.println("诱饵鸭");
    }
    public void quack() {
        System.out.println("我不可以发出叫声");
    }
    public void fly() {
        System.out.println("我不可以飞的");
    }
}
```

### 使用继承来进行设计存在的问题

- 代码在多个导出类中重复
- 运行时的行为不容易改变
- 很难知道所有导出类的全部行为
- 任何都可能造成导出类不想要的改变

### 使用接口进行设计

使用接口来只让部分导出类具有某种行为，而不是全部导出类都具有某种行为。

![使用接口进行设计](interface.png)

如果某个导出类具有某种行为，就让它实现某个接口。

### 使用接口进行设计存在的问题

在实现了接口的导出类中都需要提供实现，造成代码无法复用。如果存在很多导出类，且需要修改某个行为时，需要在每一个导出类中都进行修改。

### 使用设计原则解决问题

不管在何处工作，构建些什么，用何种编程语言，在软件开发上，一直伴随着的那个不变的真理就是：不管当初软件设计得多好，一段时间之后，总是需要成长与改变，否则软件就会“死亡”。

**目前问题总结**

在目前的情况下，使用继承并不能很好地解决问题，因为导出类中的行为是不断变化的，并且让所有导出类都具备这些行为是不恰当的。使用接口一开始似乎还不错，解决了导出类之间行为的差异的问题，但是Java接口不具有实现代码，所以实现接口无法达到代码的复用。以致于无论何时需要修改某个行为，必须得往下追踪并在每一个定义此行为的导出类中修改它，一不小心就会造成新的错误。

**设计原则**

- 找出应用中可能需要变化之处，把它们独立出来，不要和那些不需要变化的代码混在一起。或者说，把会变化的部分取出并封装起来，以便以后可以轻易地改动或扩充此部分，而不影响不需要变化的其他部分。

如果每次新的需求一来，都会使某方面的代码发生变化，那么就可以确定，这部分的代码需要被抽取出来，和其他稳定的代码有所区分。

- 针对接口编程，而不是针对实现编程

**具体实现**

```java
//FlyBehavior及实现
public interface FlyBehavior {
    void fly();
}
public class FlyNoWay implements FlyBehavior {
    public void fly() {
        System.out.println("I can't fly.");
    }
}
public class FlyWithWings implements FlyBehavior {
    public void fly() {
        System.out.println("I'm flying!");
    }
}
//QuackBehavior及实现
public interface QuackBehavior {
    void quack();
}
public class Quack implements QuackBehavior {
    public void quack() {
        System.out.println("Quack");
    }
}
public class Squeak implements QuackBehavior {
    public void quack() {
        System.out.println("Squeak");
    }
}
public class MuteQuack implements QuackBehavior {
    public void quack() {
        System.out.println("<< Silence >>");
    }
}
//Duck及实现
public abstract class Duck {
    FlyBehavior flyBehavior;
    QuackBehavior quackBehavior;
    public Duck() {}
    public abstract void display();
    public void performFly() {
        flyBehavior.fly();
    }
    public void performQuack() {
        quackBehavior.quack();
    }
    public void swim() {
        System.out.println("All ducks float");
    }
}
public class MallardDuck extends Duck {
    public MallardDuck() {
        quackBehavior = new Quack();
        flyBehavior = new FlyWithWings();
    }
    public void display() {
        System.out.println("I'm a real Mallard duck");
    }
}
```

**动态改变行为**

```java
public abstract class Duck {
    FlyBehavior flyBehavior;
    QuackBehavior quackBehavior;
    public Duck() {}
    public abstract void display();
    public void performFly() {
        flyBehavior.fly();
    }
    public void performQuack() {
        quackBehavior.quack();
    }
    public void swim() {
        System.out.println("All ducks float");
    }
    //可以进行修改的方法
    public void setFlyBehavior(FlyBehavior fb) {
        this.flyBehavior = fb;
    }
    public void setQuackBehavior(QuackBehavior qb) {
        this.quackBehavior = qb;
    }
}
```

**新的类图**

![策略模式](pattern01.png)