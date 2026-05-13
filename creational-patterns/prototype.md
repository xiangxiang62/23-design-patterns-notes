## 什么是原型模式？

**原型模式** 是一种创建型设计模式。主要用于 **通过复制现有的对象来创建新对象**，而不是通过 new 关键字来直接实例化。简而言之，原型模式让我们能够在已有对象的基础上创建新对象。它依赖于 “克隆” 已有对象的状态，从而减少重复构建相同对象的成本。

举个例子，我们都打印过东西，我们把文件拿到打印店中，不需要再重新手写一遍，直接用打印机复印出一模一样的副本。在这个场景中，原件就是原型（Prototype），复印机就相当于是调用了原型的克隆功能，快速拷贝出很多个一模一样的对象。

有时候我们可能想在复印件上加点批注，画一些线。这就像是在克隆出来的对象再做一点小的修改，也就是原型模式的一个灵活用法。

所以原型模式就像是一台对象复印机，可以高效的复制出新的对象，尤其适用于构建过程复杂、创建成本高的场景。

## 为什么要使用原型模式？

1. **对象创建过程开销较大**：当对象创建的过程比较复杂或者需要消耗较多资源的时候，直接复制现有的对象可能会比重新构建对象更高效。

2. **需要大量相似的对象**：如果我们要创建大量结构相似、但又不完全相同的对象，那么通过克隆一个模板对象来创建新对象，会比每次都手动设置每个属性要简单的多。

3. **对象的状态是变化的**：当对象的状态不止一次构建就能完成，并且在多次使用后可能会有不同的变化时，使用原型模式可以帮助我们根据现有对象状态来创建新对象。

   

   ![](https://i.imgs.ovh/2026/05/13/92f6660b5638870fc2708879b17120cb.png)

通过对比可以看出，不使用原型模式时，需要手动实现复制方法，为每个属性创建新的实例，这导致了代码冗长、容易出错、维护困难。当类的属性发生变化时，需要同步修改复制方法，增加了开发成本。

使用原型模式后，实现通过 Cloneable 接口和实现自定义的 cloneDocument 方法，可以轻松创建对象的副本。原型模式将对象的复制过程封装在类内部，使得复制草错更加简单和可靠。这种实现方式不仅减少了代码量，还提高了代码的可维护性，使得床架你对象副本变得更加高效和优雅。

## 原型模式的应用场景

- **图形编辑软件中的形状复制**：在图形编辑系统（如绘图软件、CAD 系统）中，用户常常需要复制图形（圆形、矩形、多边形等）。每种图形可能有许多相似的属性，使用原型模式可能通过克隆一个现有的对象来快速创建一个新的对象，而不是重新初始化对象。
- **配置对象复制**：例如系统的初始化配置、模板文件、配置信息等，这些通常是对象的集合（如数据库连接池配置、缓存配置等），如果某个配置在程序中有多个实例，原型模式可以通过克隆来快速生成新的配置对象，避免重复创建和设置。
- **缓存对象克隆**：在缓存系统中，尤其是当缓存对象需要被更新并生成新对象时，使用原型模式可以避免重复创建对象，直接从缓存中克隆对象，直接从缓存中克隆出新对象并进行修改。
- **文档复制功能**：在内容管理系统、企业知识库、在线协作文档平台中，很多时候我们需要基于一份已有文档快速创建一个新文档，这个过程需要复制文档的标题、正文、作者信息、附件、目录结构、版本记录等内容。

## 原型模式的基本结构

1）原型接口：生命一个克隆自身的接口，所有具体原型类都需要实现这个接口。

2）具体原型类：实现原型接口，定义如何复制自身的对象。

3）客户端：通过调用圆形对象的克隆方法，来获取新的对象实例。

## 原型模式代码实现

以 “文档克隆” 为例，使用原型模式实现文档复制功能。

1）定义原型接口：声明克隆方法

```java
public interface DocumentPrototype extends Cloneable {
    DocumentPrototype cloneDocument();
}
```

这一步是原型模式的基础，定义一个统一的克隆方法 cloneDocument()，表示所有可复制的文档都应该实现这个接口，具备自我复制的能力。

2）实现具体原型类：定义文档结构并实现克隆逻辑

```java
public class WordDocument implements DocumentPrototype {
    private String title;
    private String content;
    private List<String> images;

    public WordDocument(String title, String content, List<String> images) {
        this.title = title;
        this.content = content;
        this.images = new ArrayList<>(images);
    }

    @Override
    public DocumentPrototype cloneDocument() {
        try {
            WordDocument copy = (WordDocument) super.clone();
            copy.images = new ArrayList<>(this.images); // 深拷贝，避免共享引用
            return copy;
        } catch (CloneNotSupportedException e) {
            throw new RuntimeException("Clone failed", e);
        }
    }

    public void show() {
        System.out.println("Title: " + title);
        System.out.println("Content: " + content);
        System.out.println("Images: " + images);
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public void setContent(String content) {
        this.content = content;
    }

    public void addImage(String image) {
        this.images.add(image);
    }
}
```

这段代码是原型模式的核心实现。通过 clone() 方法创建副本，同时对 images 列表做深拷贝，防止多个副本之间互相影响。

3）客户端调用示例：从模板克隆多个实例文档。

```java
public class Client {
    public static void main(String[] args) {
        List<String> images = new ArrayList<>();
        images.add("cover.png");

        WordDocument template = new WordDocument("招聘简历模板", "请填写个人信息", images);
        System.out.println("== 原始模板 ==");
        template.show();

        WordDocument doc1 = (WordDocument) template.cloneDocument();
        doc1.setTitle("锟斤拷的简历");
        doc1.setContent("锟斤拷，5年的全栈开发经验");
        doc1.addImage("kunjinkao_avatar.png");

        WordDocument doc2 = (WordDocument) template.cloneDocument();
        doc2.setTitle("香香的简历");
        doc2.setContent("香香，8年的Java开发经验");
        doc2.addImage("xiangxiang_avatar.png");

        System.out.println("\n== 克隆出的锟斤拷的简历 ==");
        doc1.show();

        System.out.println("\n== 克隆出的香香的简历 ==");
        doc2.show();
    }
}
```

输出结果：

```java
== 原始模板 ==
Title: 招聘简历模板
Content: 请填写个人信息
Images: [cover.png]

== 克隆出的锟斤拷的简历 ==
Title:锟斤拷的简历
Content锟斤拷，5年的全栈开发经验
Images: [cover.png, kunjinkao_avatar.png]

== 克隆出的香香的简历 ==
Title: 香香的简历
Content: 香香，8年的Java开发经验
Images: [cover.png, xiangxiang_avatar.png]
```

客户端通过调用 cloneDocument() 方法，快速创建多个互不干扰的文档副本，避免重复构造和字段复制，提升效率，这就是原型模式的精髓所在。

## 原型模式的优缺点

#### 优点：

- **对象创建效率高**：原型模式通过克隆已有的对象来创建新对象，省去了重新 new 的过程，尤其适合那种初始化过程比较复杂或者比较耗资源的对象。复制一份现成的，比从头构建要快的多。
- **简化对象的创建逻辑**：有时候一个对象的创建逻辑非常复杂，比如需要从数据库加载、远程拉数据、组合多个子对象等等，用原型模式就可以把这些一次性处理好，之后通过克隆就能直接复用。
- **便于动态创建新对象**：如果系统需要在运行时灵活生成对象，而这些对象的结构和内容不确定，原型模式已提供了很好的解决方案。通过复制已有对象，可以更方便的动态生成所需的新实例。

#### 缺点：

- **深拷贝比较麻烦**：如果对象内部还有嵌套引用，比如 List、Map 或其他复杂对象，浅拷贝就不够用了。这时候就得实现深拷贝，而深拷贝的代码写起来往往不太轻松，稍不注意就有可能出现问题。
- **对克隆的依赖性强**：原型模式依赖对象实现 clone() 方法或者自定义的复制逻辑，如果原型类设计的不合理，比如克隆出来的对象状态不一致，就可能带来隐形 bug，尤其是在多人协作开发的时候更容易出现问题。
- **可能违反封装性**：为了实现克隆，有时候需要暴露一些本来不应该开放的字段或方法，这可能会破坏类本很的封装性，降低系统的健壮性。