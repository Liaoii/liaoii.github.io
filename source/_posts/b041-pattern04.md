---
title: "装饰者模式（Decorator）"
catalog: true
date: 2021-03-04 18:14:15
subtitle:
header-img:
tags:
- Design Patterns
---

### 抛出需求

有一个咖啡店需要设计它的订单系统，首先是各种种类的咖啡，且在购买咖啡时，可以根据需求加入各种调料。最后会根据咖啡的类型和所加入的调料收取不同的费用。

![咖啡](coffee.png)

### 第一种实现方式

![第一种实现方式](first.png)

在基类中通过Boolean类型的成员变量来表示是否加入了某种调料，cost()方法用来计算这些调料的价格，子类的cost()方法通过调用基类的cost()来获取调料的总价格，再加上自身的价格来得到总的价格。

```java
public class Beverage {
    public String description;
    private boolean milk;
    private boolean soy;
    private boolean whip;
    public double cost() {
        double total = 0;
        if (hasMilk()) {total += 1.1;}
        if (hasSoy()) {total += 1.2;}
        if (hasWhip()) {total += 1.3;}
        return total;
    }
    public String getDescription() {return description;}
    public boolean hasMilk() {return milk;}
    public void addMilk() {this.milk = true;}
    public boolean hasSoy() {return soy;}
    public void addSoy() {this.soy = true;}
    public boolean hasWhip() {return whip;}
    public void addWhip() {this.whip = true;}
}
```

```java
public class DarkRoast extends Beverage {
    public DarkRoast() {
        description = "DarkRoast";
    }
    public double cost() {
        double cost = super.cost();
        return cost + 10;
    }
}
```

```java
public class Order {
    public static void main(String[] args) {
        DarkRoast roast = new DarkRoast();
        roast.addMilk();
        roast.addSoy();
        roast.addWhip();
        System.out.println(roast.cost());
    }
}
```

**当前设计存在的问题**

- 如果调料的价格有变动，需要修改基类中的cost()方法
- 如果出现新的调料，需要在基类中添加新的方法，并且需要修改cost()方法
- 如果需要开发新的饮料，有些调料对这种新的饮料可能并不合适
- 如果顾客需要双倍的调料该怎么办

### 开放-关闭原则

类应该对扩展开放，对修改关闭

我们的目标是允许类容易扩展，在不修改现有代码的情况下，就可搭配新的行为。这样的设计具有弹性可以应对改变，可以接受新的功能来应对改变的需求。

### 定义装饰者模式

装饰者模式动态地将责任附加到对象上，若要扩展功能，装饰者提供了比继承更有弹性的替代方案。

![装饰者模式](decorator.png)

- 装饰者和被装饰对象有相同的超类型
- 可以用一个或多个装饰者包装一个对象
- 在任何需要原始对象的场合，可以用装饰过的对象替代它
- 装饰者可以在所委托被装饰者的行为之前与/或之后，加上自己的行为，以达到特定的目的
- 对象可以在任何时候被装饰，所以可以在运行时动态地、不限量地用你喜欢的装饰者来装饰对象

### 使用装饰者模式实现上面的需求

![装饰者模式](coffeeDecorator.png)

**具体实现**

```java
public abstract class Beverage {
    String description = "Unknown Beverage";
    public String getDescription() {
        return description;
    }
    public abstract double cost();
}
```

```java
public class Espresso extends Beverage{
    public Espresso() {
        description = "Espresso";
    }
    public double cost() {
        return 19.9;
    }
}
```

```java
public abstract class CondimentDecorator extends Beverage {
    public abstract String getDescription();
}
```

```java
public class Mocha extends CondimentDecorator {
    Beverage beverage;
    public Mocha(Beverage beverage) {
        this.beverage = beverage;
    }
    public String getDescription() {
        return beverage.getDescription() + ", Mocha";
    }
    public double cost() {
        return 5 + beverage.cost();
    }
}
```

```java
public class Soy extends CondimentDecorator {
    Beverage beverage;
    public Soy(Beverage beverage) {
        this.beverage = beverage;
    }
    public double cost() {
        return 4 + beverage.cost();
    }
    public String getDescription() {
        return beverage.getDescription() + ", Soy";
    }
}
```

```java
public class Whip extends CondimentDecorator {
    Beverage beverage;
    public Whip(Beverage beverage) {
       this.beverage = beverage;
    }
    public double cost() {
        return 6 + beverage.cost();
    }
    public String getDescription() {
        return beverage.getDescription() + ", Whip";
    }
}
```

**测试**

```java
public class Order {
    public static void main(String[] args) {
        Beverage espresso = new Espresso();
        espresso = new Mocha(espresso);
        espresso = new Soy(espresso);
        System.out.println(espresso.getDescription());
        System.out.println(espresso.cost());
    }
}
```