---
title: "抽象工厂模式（Abstract Factory）"
catalog: true
date: 2021-04-26 13:52:11
subtitle:
header-img:
tags:
- Design Patterns
---

### 依赖倒置原则

要依赖抽象、不要依赖具体类。

该原则与【针对接口编程，不针对实现编程】不同，依赖倒置原则更强调抽象，它的意思是说：不能让高层组件依赖低层组件，而且，不管高层或低层组件，两者都应该依赖于抽象。

### 举例

```java
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

PizzaStore是高层组件、而CheesePizza、GreekPizza、PepperoniPizza等是低层组件，PizzaStore在这里需要依赖这些低层组件。根据依赖倒置原则，需要重写PizzaStore类，使其依赖抽象类。

**使用工厂**

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

在应用了工厂方法之后，高层组件PizzaStore就只依赖Pizza抽象了，而CheesePizza、GreekPizza、PepperoniPizza等低层组件由于是从Pizza继承而来，所以也依赖Pizza抽象。

在未使用工厂方法之前，依赖是从高层到低层的。而应用了工厂方法之后，低层组件也开始依赖高层的抽象，高层组件也依赖相同的抽象。依赖关系被倒置了过来。

### 如何遵循依赖倒置原则

- 变量不可以持有具体类的引用
- 不要让类派生自具体类
- 不要覆盖基类中已实现的方法

### 定义抽象工厂模式

抽象工厂模式提供一个接口，用于创建相关或依赖对象的家族，而不需要明确指定具体类。

![抽象工厂](abstruactFactory.png)

抽象工厂允许客户使用抽象的接口来创建一组相关的产品，而不需要知道实际产出的具体产品是什么。这样一来，客户就从具体的产品中被解耦。

### 抽象工厂示例

```java
public interface PizzaIngredientFactory {
    Dough createDough();
    Sauce createSauce();
    Cheese createCheese();
}
```

```java
public class ChicagoPizzaIngredientFactory implements PizzaIngredientFactory {
    public Dough createDough() {
        return new ThlckCrustDough();
    }
    public Sauce createSauce() {
        return new PlumTomatoSauce();
    }
    public Cheese createCheese() {
        return new FreshCheese();
    }
}
```

```java
public class NYPizzaIngredientFactory implements PizzaIngredientFactory {
    public Dough createDough() {
        return new ThinCrustDough();
    }
    public Sauce createSauce() {
        return new MarinaraSauce();
    }
    public Cheese createCheese() {
        return new ReggianoCheese();
    }
}
```

```java
public abstract class Pizza {
    String name;
    Dough dough;
    Sauce sauce;
    Cheese cheese;
    abstract void prepare();
    public void bake() {
        System.out.println("烘焙中");
    }
    public void cut() {
        System.out.println("切片");
    }
    public void box() {
        System.out.println("打包");
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

```java
public class CheesePizza extends Pizza {
    PizzaIngredientFactory factory;
    public CheesePizza(PizzaIngredientFactory factory) {
        this.factory = factory;
    }
    void prepare() {
        System.out.println("Preparing " + name);
        dough = factory.createDough();
        sauce = factory.createSauce();
        cheese = factory.createCheese();
    }
}
```

```java
public abstract class PizzaStore {
    public Pizza orderPizza(String type) {
        Pizza pizza;
        pizza = createPizza(type);
        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        return pizza;
    }
    protected abstract Pizza createPizza(String type);
}
```

```java
public class NYPizzaStore extends PizzaStore {
    protected Pizza createPizza(String type) {
        Pizza pizza = null;
        PizzaIngredientFactory factory = new NYPizzaIngredientFactory();
        if (type.equals("cheese")) {
            pizza = new CheesePizza(factory);
            pizza.setName("New York Style Cheese Pizza");
        }
        return null;
    }
}
```