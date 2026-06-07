## 责任链模式

### 什么是责任链模式？

责任链模式是一种行为型设计模式，它的核心思想就是：**将多个处理请求的对象链接成一条链，每个对象在链上依次处理请求，直到有对象处理完请求为止。**换句话说，当一个请求到来时，它会沿着链上的对象逐个传递，每个对象有机会去处理这个请求。如果当前对象无法处理，它就将请求传递给链上的下一个对象。

就像我们平时遇到问题时，可能会先找一个人问，然后如果他不懂的话，就转交给下一个人，直到问题得到解决。每个人都承担自己可以处理的部分，任务逐级分配，直到问题得到处理。

比如我们在公司请假的时候，流程其实很像责任链的：请两天假，组长就批了，请五天假，组长批不了，就要往上递交给部门经理，如果我想在请个长假出去玩一个月，那就让总监出面了。这种逐级审批的流程，本质上就是一条责任链。只要发起请假申请，不管谁批的，反正最后有人能拍板就行。

写代码的时候也会遇到这种类似的情况，比如做一个请求拦截器，先做权限校验，再做参数校验，最后可能还有日志校验，每个环节负责自己那块，不用知道前后是谁，只管干自己的事情，整体逻辑非常清晰，灵活，后续扩展也方便。

### 为什么要使用责任链模式？

实际开发中，我们经常会遇到某个请求需要经过多个处理环节。例如，一个订单系统可能需要经过库存检查，支付验证，配送安排等多个步骤。如果这些步骤放在一个类里处理，逻辑会非常混乱，并且修改起来也会非常麻烦，而责任链模式通过将这些环节分成多个对象，每个对象只处理自己负责的部门，并将处理结果传递给下一个环节，清晰明了，方便扩展和维护，这样我们不仅可以避免一个类中堆砌过多的业务逻辑，还能动态的调整责任链的顺序和处理方式。

### 责任链的应用场景

- 用户请求的权限校验流程：在企业权限系统中，一个请求可能需要经过多个层级的权限校验，如登录校验，角色校验，操作权限校验等。使用责任链模式可以将每个校验步骤封装成独立处理器，按照顺序处理请求，便于扩展和维护。
- 电商系统的订单优惠处理：下单时可能会存在多种优惠（满减、折扣、优惠券、会员折扣等），这些优惠可能需要按照顺序叠加或互斥。通过责任链模式，将每种优惠逻辑封装成一个处理节点，逐个执行判断和计算。
- 内容审核系统中的多级规则过滤：在社区、短视频、评论系统中，内容发布前可能需要经过 “敏感词过滤 →图片检测→风险用户识别→自定义规则”等多个审核步骤。每个过滤器作为链中的一环，统一处理内容审核流程。

### 责任链模式基本结构

1）抽象处理者：声明一个处理请求的接口，通常包括一个方法 handleRequest()，并且持有对下一个处理者的引用。

2）具体处理者：实现抽象处理器的处理方法，如果当前对象能够处理请求，就处理它；否则，将请求转发给链上的下一个对象。

3）客户端：客户端负责构造责任链并发送请求，它不需要关心请求如何被处理，只需要向责任链传递请求即可。

### 责任链模式的实现

下面就以 “订单优惠处理” 为例，我们用责任链模式实现一个订单优惠系统。

1）定义优惠处理接口：声明各类优惠的处理方法

```java
interface DiscountHandler {
    void setNextHandler(DiscountHandler handler);  // 设置下一个处理器
    void applyDiscount(Order order); // 应用优惠策略
}
```

在这段代码中，我们定义了一个 DiscountHandler 接口，表示优惠处理的抽象类。每个优惠处理器需要实现 applyDiscount() 方法来处理订单优惠，同时通过 setNextHandler() 方法设置责任链中的下一个处理节点。

2）实际具体的优惠处理类：每个优惠作为一个处理器

```java
class FullReductionDiscountHandler implements DiscountHandler {
    private DiscountHandler nextHandler;  // 下一个处理器

    @Override
    public void setNextHandler(DiscountHandler handler) {
        this.nextHandler = handler;
    }

    @Override
    public void applyDiscount(Order order) {
        if (order.getAmount() > 200) {
            System.out.println("满减折扣: -50");
            order.setAmount(order.getAmount() - 50);
        }
        if (nextHandler != null) {
            nextHandler.applyDiscount(order);  // 继续传递给下一个处理器
        }
    }
}

class CouponDiscountHandler implements DiscountHandler {
    private DiscountHandler nextHandler;

    @Override
    public void setNextHandler(DiscountHandler handler) {
        this.nextHandler = handler;
    }

    @Override
    public void applyDiscount(Order order) {
        if (order.hasCoupon()) {
            System.out.println("优惠券折扣: -30");
            order.setAmount(order.getAmount() - 30);
        }
        if (nextHandler != null) {
            nextHandler.applyDiscount(order);
        }
    }
}

class MemberDiscountHandler implements DiscountHandler {
    private DiscountHandler nextHandler;

    @Override
    public void setNextHandler(DiscountHandler handler) {
        this.nextHandler = handler;
    }

    @Override
    public void applyDiscount(Order order) {
        if (order.isMember()) {
            System.out.println("会员折扣: -20");
            order.setAmount(order.getAmount() - 20);
        }
        if (nextHandler != null) {
            nextHandler.applyDiscount(order);
        }
    }
}
```

在这段代码中，我们实现了三个具体的优惠处理类：FullReductionDIscountHandler（满减优惠）、CouponDiscountHandler（优惠券优惠）和MemberDiscountHandler（会员优惠）。每个类都有 applyDiscount() 方法执行相应的优惠逻辑，并通过责任链将处理过程传递下去。

3）定义订单类：包含订单金额和优惠逻辑

```java
class Order {
    private double amount;  // 订单金额
    private boolean hasCoupon;  // 是否使用了优惠券
    private boolean isMember;  // 是否为会员

    public Order(double amount, boolean hasCoupon, boolean isMember) {
        this.amount = amount;
        this.hasCoupon = hasCoupon;
        this.isMember = isMember;
    }

    public double getAmount() {
        return amount;
    }

    public void setAmount(double amount) {
        this.amount = amount;
    }

    public boolean hasCoupon() {
        return hasCoupon;
    }

    public boolean isMember() {
        return isMember;
    }
}
```

在这段代码中，我们定义了一个 Order 类，包含订单的金额，是否使用了优惠券以及是否是会员。通过这些字段，优惠处理类可以判断是否应用相应的优惠。

4）客户端调用示例

```java
public class Client {
    public static void main(String[] args) {
        Order order = new Order(250, true, true);  // 创建订单

        // 创建各个优惠处理器
        DiscountHandler fullReductionHandler = new FullReductionDiscountHandler();
        DiscountHandler couponHandler = new CouponDiscountHandler();
        DiscountHandler memberHandler = new MemberDiscountHandler();

        // 设置责任链：优惠处理器依次连接
        fullReductionHandler.setNextHandler(couponHandler);
        couponHandler.setNextHandler(memberHandler);

        // 开始应用优惠
        fullReductionHandler.applyDiscount(order);  // 从满减开始处理
        System.out.println("最终订单金额: " + order.getAmount());
    }
}
```

输出结果：

```
满减折扣: -50
优惠券折扣: -30
会员折扣: -20
最终订单金额: 150.0
```

在客户端测试中，首先创建一个订单对象，并依次创建了满减、优惠券、会员优惠处理器。通过 setNextHandler() 方法将它们链接成一个责任链。最后调用 applyDiscount() 方法开始依次处理订单的各项优惠，最终得到处理后的订单金额。

### 责任链模式的优缺点

#### 优点：

- 减少了耦合性：责任链模式通过将请求的处理分配给多个对象，每个对象只负责自己能处理的部分，避免了请求者和处理者之间的直接耦合。请求者无需知道请求最终由哪个对象处理。
- 灵活的请求处理方式：责任链中的处理者可以根据需求动态调整，灵活的控制请求的流转路径。每个处理者决定是否处理请求，或者将请求传递给下一个处理者，确保了请求的灵活处理。
- 可扩展性强：通过增加新的处理者，可以轻松的扩展责任链。当新增的处理逻辑不影响其他部分时，系统能够非常平滑的扩展，符合开闭原则。

#### 缺点：

- 不容易测试：由于请求在链中的多个对象之间传递，可能会导致调试的时候不容易追踪请求的处理路径。如果链条比较长或者比较复杂，调试过程中可能会比较麻烦。
- 可能造成性能问题：如果责任链中处理器较多且么个处理者都需要做一些复杂的操作，可能会导致请求的处理变慢，影响系统性能。
- 责任链的管理复杂性：虽然责任链模式能够使系统的扩展变得简单，但当责任链较长的时候，管理和维护责任链中的各个处理器可能会变得复杂。如果链条太长或者处理者数量过多，整个责任链的维护成本也会增加。