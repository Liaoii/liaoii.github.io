---
title: "命令模式（Command）"
catalog: true
date: 2021-03-09 08:20:07
subtitle:
header-img:
tags:
- Design Patterns
---

### 定义命令模式

命令模式将请求封装成对象，以便使用不同的请求、队列或者日志来参数化其他对象。命令模式也支持可撤销的操作。

![命令模式](command.png)

- Client负责创建一个ConcreteCommand，并设置其接收者Recelver
- ConcreteCommand定义了动作和接收者之间的绑定关系。调用者只要调用execute()方法就可以发出请求，然后由ConcreteCommand调用接收者的一个或多个动作。
- Command为所有命令声明了一个接口，调用命令对象的execute()方法，就可以让接收者进行相关的动作。这个接口也具备一个undo()方法。
- Invoker持有一个命令对象，并在某个时间点调用命令对象的execute()方法，将请求付诸行动。

### 示例

用一个遥控器来控制家里的所有家电，其中每一个家电的厂商都提供了一些接口来控制家电。

**Command**

```java
public interface Command {
    void execute();
}
```

**Recelver**

```java
public class Light {
    public void on() {
        System.out.println("打开电灯");
    }
    public void off() {
        System.out.println("关闭电灯");
    }
}
```

**ConcreteCommand**

```java
public class LightOnCommand implements Command{
    Light light;
    public LightOnCommand(Light light) {
        this.light = light;
    }
    public void execute() {
        light.on();
    }
}
```

```java
public class LightOffCommand implements Command {
    Light light;
    public LightOffCommand(Light light) {
        this.light = light;
    }
    public void execute() {
        light.off();
    }
}
```

**Client**

```java
public class SimpleRemoteControl {
    Command slot;
    public SimpleRemoteControl() {}
    public void setCommand(Command command) {
        slot = command;
    }
    public void buttonWasPressed() {
        slot.execute();
    }
}
```