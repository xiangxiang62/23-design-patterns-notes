## 什么是工厂方法模式？

### 1、模式定义

**工厂方法模式是一种创建型设计模式，旨在定义一个用于创建对象的接口。**

将具体的实例化工作放到子类中完成。

通俗易懂的来说，**工厂方法模式让子类决定实例化哪个具体类**，从而实现对象创建的灵活性和可扩展性。

### 2、为什么要使用工厂方法模式？

优点：

1. 解耦代码：客户端无需知道具体的产品类，只需要依赖抽象的工厂接口，降低了代码之间的耦合度。
2. 提升可扩展性：如果以后需要增加新的产品类型，只需新增相应的工厂类，而无需修改现有代码，符合开闭原则。
3. 集中管理：产品的创建逻辑集中在工厂中，便于维护和管理。 

### 3、工厂方法模式的应用场景

1.  消息通知系统：系统支持短信、邮件、站内信等不同的通知方式，可通过工厂方法屏蔽创建细节，让调用方只负责发送消息。
2.  支付系统：用户可能选择支付宝、微信、银行卡等支付方式，工厂方法可以根据用户选择生成对应的支付通道对象，方便扩展新方式。
3.  文件解析模块：上传的文件可能是 Excel、CSV、JSON、XML 等不同格式，工厂方法可以根据文件类型创建对应的解析器实例，统一解析入口。
4.  业务规则引擎：当一个系统支持多个业务规则版本（如不同行业、不同客户），可以通过工厂方法创建对应的规则计算器，灵活支持定制化逻辑。

### 4、 工厂方法模式的基本结构

1. 抽象产品（Product）：定义产品的公共接口，是所有具体产品的父类。
2. 具体产品（ConcreteProduct）：实现了抽象产品的接口，表示某种具体的产品。
3. 抽象工厂（Factory）：定义了一个返回产品的对象的方法（一般是一个抽象方法）。
4. 具体工厂（ConcreteFactory）：实现了抽象工厂中的创建产品的方法，生成具体的产品实例。



![工厂方法模式](https://i.imgs.ovh/2026/05/07/95e3300160c8511518029e4fe27f1702.png)

### 5、 工厂方法模式的代码实现

以 “消息通知系统” 为例来实现抽象工厂模式，可以快速创建消息对象，整个流程如下：

1）定义抽象产品接口（比如通知接口）

```java
// 抽象产品接口：通知消息
public interface Message {
    void send(String to, String content);
}
```

这个接口是所有 “消息产品” 的统一标准，无论是短信、邮件还是站内信，都必须实现 send 方法。

2）定义多个具体产品，比如短信、邮件、站内信：

```java
public class SmsMessage implements Message {
    @Override
    public void send(String to, String content) {
        System.out.println("发送短信给 " + to + "，内容：" + content);
    }
}

public class EmailMessage implements Message {
    @Override
    public void send(String to, String content) {
        System.out.println("发送邮件给 " + to + "，内容：" + content);
    }
}

public class InAppMessage implements Message {
    @Override
    public void send(String to, String content) {
        System.out.println("发送站内信给 " + to + "，内容：" + content);
    }
}
```

每个类都实现了 Message 接口，表示各自的发送方式。

3）定义抽象工厂接口

```java
// 抽象工厂接口：负责生产消息对象
public interface MessageFactory {
    Message createMessage();
}
```

4）定义多个具体工厂（每种通知方式一个工厂）

```java
public class SmsMessageFactory implements MessageFactory {
    @Override
    public Message createMessage() {
        return new SmsMessage();
    }
}

public class EmailMessageFactory implements MessageFactory {
    @Override
    public Message createMessage() {
        return new EmailMessage();
    }
}

public class InAppMessageFactory implements MessageFactory {
    @Override
    public Message createMessage() {
        return new InAppMessage();
    }
}
```

5）客户端调用（用工厂创建消息，不直接 new）

```java
public class NotificationService {
    private final MessageFactory messageFactory;

    public NotificationService(MessageFactory factory) {
        this.messageFactory = factory;
    }

    public void notifyUser(String to, String content) {
        Message message = messageFactory.createMessage();
        message.send(to, content);
    }
}
```

客户端完全不需要关心到底是邮件、短信还是站内信，只需要注入一个 “消息工厂”，然后就可以调用。

6）测试使用

```java
public class Main {
    public static void main(String[] args) {
        MessageFactory factory = new SmsMessageFactory(); // 切换只需换这里
        NotificationService service = new NotificationService(factory);
        service.notifyUser("13812345678", "您的验证码是 123456");
    }
}
```

> 如果要切换成别的通知，只需要将工厂实现类进行替换即可，完全不需要改 NotificationService 中的任何代码。

### 6、 工厂方法模式的优缺点

##### 优点：

- **解耦了对象的创建和使用：** 调用方不再关心具体是怎么 new 出来的对象，只负责使用，这样一来，如果后续对象的创建逻辑变了，也不会影响到业务逻辑，代码更稳定、更灵活。
- **符合开闭原则，易于扩展：** 当需要新增一个产品时，只需要新增一个对应的工厂类就行，原来的代码不用动。这对于后期产品种类多、变化快的场景非常友好。
- **提高了代码的复用性和可维护性：** 创建逻辑集中在工厂中，避免了重复造轮子。有什么调整，只需要改一处地方，维护成本低，也更容易统一管理。

##### 缺点：

- **类的数量会增多：** 每新增一种产品，通常就要新增一个对应的工厂类。产品多了之后，工厂类也多，项目结构可能会显得臃肿。
- **增加了系统复杂度：** 相较于直接 new，一个工厂类、一个产品接口这样组合起来的结构，对于小项目或者逻辑比较简单的场景来说，可能会显得过于复杂。
- **有时候可能出现过度设计：** 如果产品结构本身很简单，用工厂方法模式反而会让代码变得啰嗦，不如直接用简单工厂来得搞笑直接。

### todo 工厂模式和抽象工厂模式有什么区别？
