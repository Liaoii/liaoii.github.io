---
title: "观察者模式（Observer）"
catalog: true
date: 2021-04-25 19:40:56
subtitle:
header-img:
tags:
- Design Patterns
---

### 认识观察者模式

主题 + 观察者 = 观察者模式

![观察者](observer.png)

- 主题对象管理某些数据
- 当主题内的数据改变就会通知观察者
- 观察者已经订阅主题以便在主题数据改变时能够收到更新

### 观察者模式定义

观察者模式定义了对象之间的一对多依赖，这样一来，当一个对象改变状态时，它的所有依赖者都会收到通知并自动更新。

### 观察者模式类图

![观察者模式类图](observerUML.png)

- 在主题接口Subject中，对象可以使用此接口注册为观察者，或者把自己从观察者中删除
- 具体的主题需要实现主题接口，notifyObservers()方法在状态改变时更新所有观察者
- 所有观察者都必须实现观察者接口Observer，当主题状态改变时update()被调用
- 具体的观察者必须注册具体的主题，以便接收更新
- 每个主题可以有许多观察者

### 观察者模式分析

- 观察者模式提供了一种对象设计，让主题和观察者之间松耦合
- 主题只需要与Observer接口进行交互即可，不需要知道观察者的具体细节
- 新增、删除、替换观察者不会对主题造成任何影响
- 改变主题或观察者其中一方不会影响另一方，因为两者是松耦合的。也可以独立地复用主题或观察者。

### 设计原则

为了交互对象之间的松耦合设计而努力。

松耦合的设计之所以能让我们建立有弹性的OO系统，能够应对变化，是因为对象之间的互相依赖降到了最低。

### 具体应用-气象站

一旦气象站的数据有更新，就会通知WeatherData并调用其measurementsChanged()方法，此时需要做的就是通过该方法更新展示区的多个数据。在将来可能新增新的展示模块，所以需要支持可扩展。

![需求](require.png)

### 气象站类图

![气象站类图](weatherData.png)

### 具体实现

```java
public interface Subject {
    void registerObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers();
}
```

```java
public class WeatherData implements Subject {
    private ArrayList<Observer> observers;
    private float temperature;
    private float humidity;
    private float pressure;
    public WeatherData() {
        observers = new ArrayList();
    }
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }
    public void removeObserver(Observer observer) {
        if (observers.contains(observer)) {
            observers.remove(observer);
        }
    }
    public void notifyObservers() {
        for (Observer observer : observers)
            observer.update(temperature, humidity, pressure);
    }
    public void measurementsChanged() {
        notifyObservers();
    }
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }
}
```

```java
public interface Observer {
    void update(float temp, float humidity, float pressure);
}
```

```java
public interface DisplayElement {
    void display();
}
```

```java
public class CurrentConditionsDisplay implements Observer, DisplayElement {
    private float temperature;
    private float humidity;
    private Subject weatherData;
    public CurrentConditionsDisplay(Subject weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }
    public void display() {
        System.out.println("Current conditions: " + temperature + "F degrees and " + humidity + "% humidity");
    }
}
```

```java
public class StatisticsDisplay implements Observer, DisplayElement {
    private float temperature;
    private float humidity;
    private float pressure;
    private Subject weatherData;
    public StatisticsDisplay(Subject weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        display();
    }
    public void display() {
        System.out.println("Current conditions: " + temperature +
                "F degrees and " + humidity + "% humidity " +
                "and pressure is " + pressure);
    }
}
```

### 使观察者获得状态信息的方法

- 主题主动将所有状态信息推送给观察者

  劣势：

  这种方式存在的问题是主题必须一次性将所有的状态信息都推送给观察者，其中有很多状态信息可能是观察者并不需要的。

- 由观察者主动向主题拉取自身所需的数据

  劣势：

  这种方式存在的问题是观察者每次获取状态信息可能需要拉取多次

  优势：

  如果有观察者只需要一点点数据，不会被强迫收到一堆数据

  如果主题需要扩展功能，新增更多的状态，此时就不需要修改和更新每位观察者的调用。

### Java内置的观察者模式

**类图**

![JDK内置观察者模式](jdkObserverUML.png)

**实现**

```java
import java.util.Observable;
public class WeatherData extends Observable {
    private float temperature;
    private float humidity;
    private float pressure;
    public WeatherData() {}
    public void measurementsChanged() {
        setChanged();
        notifyObservers();
    }
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }
    public float getTemperature() {
        return temperature;
    }
    public float getHumidity() {
        return humidity;
    }
    public float getPressure() {
        return pressure;
    }
}
```

```java
public interface DisplayElement {
    void display();
}
```

```java
import java.util.Observable;
import java.util.Observer;
public class CurrentConditionsDisplay implements Observer, DisplayElement {
    Observable observable;
    private float temperature;
    private float humidity;
    public CurrentConditionsDisplay(Observable observable) {
        this.observable = observable;
        observable.addObserver(this);
    }
    public void display() {
        System.out.println("Current conditions: " + temperature + "F degrees and " + humidity + "% humidity");
    }
    public void update(Observable obs, Object arg) {
        if (obs instanceof WeatherData) {
            WeatherData weatherData = (WeatherData) obs;
            this.temperature = weatherData.getTemperature();
            this.humidity = weatherData.getHumidity();
            display();
        }
    }
}
```

**Observable存在的问题**

- 由于Observable是一个类，且Java不支持多重继承，所以这限制了Observable的复用能力。

- Observable中的setChanged()方法被指定为protected，所以只能继承Observable，无法使用组合来复用Observable。

  