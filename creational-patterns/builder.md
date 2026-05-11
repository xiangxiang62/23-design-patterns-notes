## 什么是建造者模式

建造者模式是一种创建型设计模式，核心思想为：把一个复杂的对象创建过程拆解成多个小步骤，然后一步一步构建出来。在这个过程中，客户端只需要告诉系统：”我想要什么？“ 而不需要去关注 ”怎么创造出来的“。

比如组装一台电脑，只需要告诉装机师傅，我要高性能的游戏主机。然后他就会帮你一步步装好，你只管玩游戏，不需要关心零件怎么配、线怎么插。

## 为什么要使用建造者模式

1）**对象构建过程复杂：** 如果需要创建的对象有很多步骤或者很多参数。直接在构造函数或者其他方法中传递这些信息会变得复杂难懂。而建造者模式能把这些步骤分开处理，使得对象的构建过程更加清晰、

2）**需要灵活的对象构建方式：** 当我们需要根据不同的需求构建相似的对象时，建造者模式允许我们通过改变建造过程中的某些步骤来创建不同的对象，而不需要重写整个对象构造代码。

3）**构建过程和表示分离：** 建造者模式可以把构建过程和对象的表示（结果）分离开来，使得同一个构建过程能够创建出不同的表示对象。

## 建造者模式的应用场景

- **复杂的表单或请求对象的构建：** 如创建一个包含多个可选字段的用户注册请求、订单提交请求、报表筛选条件等，可以用建造者模式灵活拼接参数，避免构造方法过长或参数顺序过乱。
- **导出文件内容构建：** 例如生成复杂的 PDF 报告、Word 文件或 Excel 报表，往往需要一步步构建文档结构（比如页眉、页脚、表格、段落等），使用建造者模式可以分步骤构造并保证内容完整性。
- **业务流水记录对象创建：** 例如在支付系统或交易系统中，流水日志往往由多个字段拼成（请求来源、时间戳、交易码、响应内容、错误栈等），用建造者模式可以按需构建，避免创建臃肿的构造器或 set 方法杂乱调用。
- **消息推送内容构造器：** 推送系统中，不同渠道（短信、微信、邮件）消息格式差异，有标题、正文、按钮、跳转链接等字段信息，通过建造者模式统一构造内容，保持代码清晰灵活。

## 建造者模式的基本结构

1）**产品类：** 要构建的复杂对象，包含多个组成部分；

2）**抽象建造者类：** 定义构建产品各个部分的抽象方法，以及返回最终产品的方法；

3）**具体建造者类：** 实现 Builder 接口，具体负责各个部分的构建细节，并最终组装出完整产品；

4）**指挥者类：** 统一指挥建造者按照一定步骤来构建产品，屏蔽了构建过程的细节；

5）**客户端类：** 发起建造请求，选择具体的建造者并使用指挥者来完成产品的创建；

## 建造者模式的实现

我们来使用 “电脑组装” 的案例来实践一下建造者模式的使用：

1）定义产品类 Computer，表示最终要构建的复杂对象

```java
public class Computer {
    private String cpu;
    private String memory;
    private String hardDisk;
    private String gpu;

    public void setCpu(String cpu) {
        this.cpu = cpu;
    }

    public void setMemory(String memory) {
        this.memory = memory;
    }

    public void setHardDisk(String hardDisk) {
        this.hardDisk = hardDisk;
    }

    public void setGpu(String gpu) {
        this.gpu = gpu;
    }

    @Override
    public String toString() {
        return "Computer{" +
               "cpu='" + cpu + '\'' +
               ", memory='" + memory + '\'' +
               ", hardDisk='" + hardDisk + '\'' +
               ", gpu='" + gpu + '\'' +
               '}';
    }
}
```

这段代码定义了我们最终要构建的对象，也就是电脑、包含 CPU、内存、硬盘、显卡等几个配置项。



2）定义抽象建造者 ComputerBuilder，约定构建流程

```java
public abstract class ComputerBuilder {
    protected Computer computer = new Computer();

    public abstract void buildCpu();
    public abstract void buildMemory();
    public abstract void buildHardDisk();
    public abstract void buildGpu();

    public Computer getResult() {
        return computer;
    }
}
```

这段代码定义了 “装机流程” 的抽象说明，具体怎么装，由子类去完成实现。



3）定义具体建造者 GamingComputerBuilder，实现具体组装逻辑

```java
public class GamingComputerBuilder extends ComputerBuilder {
    @Override
    public void buildCpu() {
        computer.setCpu("Intel i9");
    }

    @Override
    public void buildMemory() {
        computer.setMemory("32GB DDR5");
    }

    @Override
    public void buildHardDisk() {
        computer.setHardDisk("1TB NVMe SSD");
    }

    @Override
    public void buildGpu() {
        computer.setGpu("NVIDIA RTX 4090");
    }
}
```

这里我们实现了一个 “高性能游戏电脑” 的装机过程。每个组件都用了比较高端的配置。



4）定义指挥者 Director，组织装机顺序

```java
public class Director {
    private ComputerBuilder builder;

    public Director(ComputerBuilder builder) {
        this.builder = builder;
    }

    public Computer construct() {
        builder.buildCpu();
        builder.buildMemory();
        builder.buildHardDisk();
        builder.buildGpu();
        return builder.getResult();
    }
}
```

这个类负责控制装机顺序，不管你换那个建造者，实现顺序都可以在这里安排。



5）客户端调用代码，组装电脑

```java
public class Main {
    public static void main(String[] args) {
        ComputerBuilder builder = new GamingComputerBuilder();
        Director director = new Director(builder);
        Computer gamingPc = director.construct();
        System.out.println(gamingPc);
    }
}
```

最终在主程序中，我们选用了一个具体的建造者 Builder，把电脑一步步的创建出来，最后构建了一台完整的高性能游戏电脑。

## 建造者模式的优缺点

#### 优点：

- **结构清晰、过程可控：** 建造者模式把复杂的对象构建过程拆成一个个明确的步骤，让整个流程变得清晰、稳定，而且每一步都可以单独控制，方便管理和调整。
- **便于构建 “不同版本” 的对象：** 通过不同的建造者，可以轻松的创建出结构类似但配置不同的对象，特别适合那种 ”定制化“ 很强的业务场景，比如生成不同类型的报表、构造不同风格的 UI 等。
- **代码更易维护和扩展：** 把构建逻辑从产品本身抽离出来，符合单一职责原则，既减少了耦合，也让代码更容易维护。如果后续要新增构建步骤或者替代某个细节，实现起来也很自然。

#### 缺点：

- **增加了类的数量：** 为了实现建造者模式，需要引入额外的建造者类和指挥者类，类的数量一下子就上来了。如果对象结构本身并不复杂，这种拆分反而会让代码变的繁琐。
- **不适合构建过程差异太大的对象：** 建造者模式适合使用在 “有共同构建步骤” 的对象上。如果对象之间的构建逻辑完全不一样，硬套建造者模式反而会让代码变得臃肿、不灵活。