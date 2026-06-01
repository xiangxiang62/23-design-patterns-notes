## 策略模式

**策略模式** 是一种行为型设计模式。它的核心思想就是：**定义一系列算法，把它们一个个封装起来，并且让它们可以互相替换。**

换句话说，我们可以把不同的处理逻辑抽象成不同的策略对象，在程序运行时，根据实际情况动态选择合适的策略进行处理，而不需要在代码里堆砌大量的 if...else 或 switch 判断。

拿生活中的场景来说，这事其实我们每天都在干。比如我们早上出门上班时，会根据天气和路况选择出行方式：天气好就骑车，下雨就打车，堵车就改坐地铁。不同的出行方式就是不同的策略，可以根据实际情况选择，背后的逻辑就非常符合策略模式的设计理念。

这种模式在项目里非常好用，尤其是当发现代码出现了很多重复的判断逻辑，或者某个行为有多个实现方式的时候，基本可以考虑使用策略模式重构一波。可以将变化隔离开，让程序更灵活、更可维护。

### 为什么要使用策略模式？

在实际开发中，当我们遇到需要根据不同条件选择不同行为的场景时，最直接的做法通常是堆一堆 if-else 或者 switch-case 判断。随着条件的增多，代码就会变得又长又乱，维护起来非常困难。更重要的是，这种做法严重违背了面向对象设计的开闭原则（对扩展开放，修改关闭）。

而使用策略模式，我们可以将每一种策略封装为单独的类，后续如果要新增、修改策略，只需要扩展新的策略类就行，原有代码不需要改动，系统的扩展性和可维护性就能大大提升。

为了让大家更好的感受到策略模式的使用，以电商折扣计算为例，我们需要实现一个灵活的促销系统，支持多种折扣方式，让我们来看看使用和不使用策略模式的区别：

![](https://i.imgs.ovh/2026/06/01/4ec61a966019ea912c72badb72151999.png)

通过对比可以看出，不使用策略模式的时候，所有的折扣计算逻辑都集中在 PriceCalculator 类中，使用 if 语句进行判断。这种方式导致代码耦合度高且维护困难，每当需要添加新的折扣方式时，都需要修改 PriceCalculator 类的代码，违反了开闭原则。

而使用策略模式后，我们将每种折扣方式封装成独立的策略类，通过接口统一管理。PriceCalculator 类再不关心具体的折扣计算逻辑，只需要调用策略对象的 calculate 方法即可。这种设计使得系统更加灵活，添加新的折扣方式只需要创建新的策略类，无需修改现有代码。同时，策略模式也使得代码结构更加清晰，每个策略类都专注于自己的职责，提高了代码的可维护性。

### 策略模式的应用场景

- 支付系统中支付方式选择：在电商平台中，支付系统通常支持多种支付方式（比如支付宝、微信、信用卡等）。通过策略模式，可以将每种支付方式抽象为不同的策略类，用户根据选择的支付方式动态切换对应的策略，简化支付流程的实现和扩展。
- 折扣计算策略：在电商平台或者购物网站中，商品的折扣计算可能根据不同的促销策略（如满减、打折、会员折扣等）有所不同。策略模式允许根据不同的促销活动或用户登记选择响应的折扣策略，并在需要的时候轻松切换或扩展折扣规则。
- 路线规划算法：在导航系统或地图应用中，用户可以选择不同的路线规划算法（如最短路径、最快路径、避免拥堵等）。策略模式可以将这些不同的路线规划策略封装为不同的算法策略，用户选择时可以动态切换算法，无需修改系统的核心逻辑。
- 日志记录方式选择：在应用中，日志的记录方式可能根据不同的环境或需求的变化而变化。例如，在开发环境中可以记录详细日志，而在生产环境中可能只输出关键日志。通过策略模式，可以在程序运行时选择不同的日志记录策略（如控制台输出、文件写入、远程日志服务等）。

### 策略模式的基本结构

1）策略接口：定义所有支持的算法（策略）必须实现的接口。

2）具体策略：实现策略接口，封装具体的算法实现。

3）上下文：持有一个策略接口的引用，负责在客户端选择并执行具体的策略。

### 策略模式的实现

下面就以 “电商折扣计算” 为例，我们用策略模式实现一个灵活的促销系统。

1）定义折扣策略接口：声明统一的折扣计算方法

```java
public interface DiscountStrategy {
    double calculate(double originalPrice);
}
```

这一步是策略模式的核心接口，抽象了 “折扣“ 行为。所有的促销方式只实现这个接口，就能被系统自由替换调用。

2）实现具体策略类：满减、打折、会员折扣等

```java
public class FullReductionStrategy implements DiscountStrategy {
    @Override
    public double calculate(double originalPrice) {
        if (originalPrice >= 100) {
            return originalPrice - 20; // 满减
        }
        return originalPrice;
    }
}


public class PercentageDiscountStrategy implements DiscountStrategy {
    @Override
    public double calculate(double originalPrice) {
        return originalPrice * 0.9; // 打九折
    }
}


public class VipDiscountStrategy implements DiscountStrategy {
    @Override
    public double calculate(double originalPrice) {
        return originalPrice * 0.8; // VIP 打八折
    }
}
```

这些类就是 “策略族” 的具体实现，分别封装了不同的折扣规则。每种规则彼此互相独立，互不影响。

3）创建上下文类：接口策略并执行计算的业务入口

```java
public class PriceCalculator {
    private DiscountStrategy strategy;

    public void setStrategy(DiscountStrategy strategy) {
        this.strategy = strategy;
    }

    public double calculatePrice(double originalPrice) {
        if (strategy == null) {
            throw new IllegalStateException("未设置折扣策略");
        }
        return strategy.calculate(originalPrice);
    }
}
```

上下文类 PriceCalculator 用于管理和执行折扣策略的调用逻辑。通过 setStrategy() 注入不同的策略对象，打到行为灵活切换的效果。

4）客户端调用示例

```java
public class Client {
    public static void main(String[] args) {
        PriceCalculator calculator = new PriceCalculator();
        double originalPrice = 120.0;

        // 使用满减策略
        calculator.setStrategy(new FullReductionStrategy());
        System.out.println("【满减策略】最终价格：" + calculator.calculatePrice(originalPrice));

        // 使用打折策略
        calculator.setStrategy(new PercentageDiscountStrategy());
        System.out.println("【打折策略】最终价格：" + calculator.calculatePrice(originalPrice));

        // 使用 VIP 策略
        calculator.setStrategy(new VipDiscountStrategy());
        System.out.println("【VIP策略】最终价格：" + calculator.calculatePrice(originalPrice));
    }
}
```

输出结果：

```
【满减策略】最终价格：100.0
【打折策略】最终价格：108.0
【VIP策略】最终价格：96.0
```

通过策略模式，PriceCalculator 并不关心如何计算，只关心调用策略。这让我们在新增 “新人专属满 200 减 50” 这类促销的时候，只需要实现一个新的策略类，而无需修改旧的逻辑。

### 策略模式的优缺点

#### 优点：

- **算法封装：**策略模式将不同的算法封装成独立的策略类，每个策略类都实现一个统一的接口。这样，当我们需要切换算法的时候，只需要改变策略对象，而不需要修改客户端代码，增强了代码的灵活性和可维护性。
- **符合开闭原则：** 当需要新增不同的策略时，我们只需要新增策略类，并实现相应的算法，不需要修改原有的代码。这样，系统在需求变化时具有更好的扩展性。
- **避免多重条件的判断：** 策略模式通过将负责的条件判断逻辑分散到各个策略类中，避免了在主程序中出现大量的 if else 或 switch-case 语句，使得代码更加清晰整洁，便于维护。

#### 缺点：

- **策略类过多：** 每增加一种策略，都需要创建一个新的策略类，随着策略数量的增加，系统中的类会变得比较多，可能导致代码冗长和难以维护。
- **客户端必须了解策略：** 虽然策略模式让算法封装的很好，但客户端仍然需要了解不同策略之间的区别，并选择合适的策略。这可能会导致客户端变得有点复杂。
- **增加了对象之间的协作：** 策略模式引入一个上下文类来管理策略的切换和使用，这意味着客户端不仅需要选择策略，还需要与上下文进行协作，可能会引入额外的复杂度。