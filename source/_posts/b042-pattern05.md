---
title: "简单工厂（Simple Factory）"
catalog: true
date: 2021-03-05 21:16:21
subtitle:
header-img:
tags:
- Design Patterns
---

### 使用new创建对象可能存在的问题

以制作披萨为例：

```java
//实例化单个类
public class PizzaStore {
    Pizza orderPizza() {
        Pizza pizza = new CheesePizza();
        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        return pizza;
    }
}
//在运行时动态实例化
public class PizzaStore {
    Pizza orderPizza(String type) {
        Pizza pizza;
        if(type.equals("cheese")) {
            pizza = new CheesePizza();
        } else if(type.equals("greek")) {
            pizza = new GreekPizza();
        } else if(type.equals("pepperoni")) {
            pizza = new PepperoniPizza();
        }

        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        return pizza;
    }
}
```

- 当使用new实例化一个具体类时，是在针对实现编程，而不是针对接口编程。这会使程序的可维护性和可扩展性降低。
- 如果披萨的种类有变化，就必须重新打开这段代码进行检查和修改。应该遵循开闭原则。

### 解决问题

此时就需要找出变化的方面，把它们从不变的部分分离出来。但是要如何将实例化具体类的代码从应用中抽离出来呢？

**使用简单工厂**

```java
public class SimplePizzaFactory {
    public Pizza createPizza(String type) {
        Pizza pizza = null;
        if (type.equals("cheese")) {
            pizza = new CheesePizza();
        } else if (type.equals("greek")) {
            pizza = new GreekPizza();
        } else if (type.equals("pepperoni")) {
            pizza = new PepperoniPizza();
        }
        return pizza;
    }
}
```

```java
public class PizzaStore {
    SimplePizzaFactory factory;
    public PizzaStore(SimplePizzaFactory factory) {
        this.factory = factory;
    }
    Pizza orderPizza(String type) {
        Pizza pizza = factory.createPizza(type);
        if (pizza != null) {
            pizza.prepare();
            pizza.bake();
            pizza.cut();
            pizza.box();
        }
        return pizza;
    }
}
```

将创建对象的代码从PizzaStore中移到SimplePizzaFactory中，创建对象的工作就转移到了SimplePizzaFactory中。但是在SimplePizzaFactory中与具体实现类耦合的问题依然存在，这是无可避免的。

使用SimplePizzaFactory的好处在于如果有多个客户需要使用SimplePizzaFactory来创建具体对象，SimplePizzaFactory可以进行统一维护。

### 简单工厂

简单工厂用来处理创建对象的细节。但简单工厂并不是一个设计模式，而是一种编程习惯。

**静态工厂**

将工厂定义成一个静态方法叫做静态工厂，使用静态工厂可以不用实例化对象便可以使用工厂。但是这样便不能通过继承来改变工厂的行为了。