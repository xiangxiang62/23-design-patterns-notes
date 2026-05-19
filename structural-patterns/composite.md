## 组合模式

组合模式是一种结构型设计模式，它是一种让我们可以用统一的方式来处理 **单个对象** 和 **一组对象** 的设计思路。它的核心思想就是：无论是一个元素，还是一组元素，我都用同一套接口、一致的方式去操作，不用每次都写一堆 if 判断去区分 “你到底是个体还是整体”。

在开发中，我们经常会碰到 “部分-整体” 的结构，比如文件系统：一个文件夹里可以放文件，也可以放文件夹，后者又可以继续嵌套文件......这时候如果每种情况都单独处理，会导致代码不够优雅。组合模式就让你像处理一个单独文件那样处理整个文件夹，递归也轻松搞定。

举个例子，大家都搬过家把，需要整理一大堆打包好的箱子，每个箱子里可能直接装着物品，比如一本书、一个杯子。也可能是一个更小的箱子，里面再装别的东西。

最后你看着这些大箱子，小箱子，其实根本不用关心 “它到底是单个物品，还是一堆东西的集合”，因为你对他们的处理方式是一样的，拿起来、贴上标签、装上车。整个过程中，对 “单个” 和 “组合” 的操作方式是完全一致的。

通俗来说，它就是帮我们把 “单个” 和 “多个” 的操作方式统一起来，让代码更简洁、更优雅、扩展也方便。

## 为什么要使用组合模式？

系统开发中，随着功能的不断扩展，对象之间的关系往往也变得复杂，有时候单个对象也能完成事情，有时候又需要一组对象配合才能完成。如果我们在代码中频繁去判断是单个对象还是一组对象，不仅逻辑繁琐，还容易出错。

组合模式通过统一接口，把这两种情况统一起来，大大简化了客户端代码，让整体结构更加清晰，扩展起来也更加自然顺手。尤其是在涉及树形结构、递归处理的场景下，组合模式能帮我们优雅的管理这些复杂的对象关系。

## 组合模式的应用场景：

- 文件系统管理：在一个文件管理系统中，文件和文件夹可以构成树形结构，文件夹可以包含多个文件或其他文件夹。组合模式可以让文件和文件夹抽象成统一的接口，使得在操作文件夹和文件时，可以使用相同的方法。比如，用户想删除一个文件夹，无论文件夹中包含的是文件还是子文件夹，操作方式都是一样。
- 企业组织结构：在处理企业的组织结构时，可以将公司、部门、员工等抽象为组件。公司是一个组合对象，包含多个部门；部门可以包含多个员工。组合模式允许我们将公司、部门和员工看作一个统一的接口，方便在便利和操作整个组织结构时使用相同的方式，无论操作的是公司、部门还是单个员工。
- 菜单管理系统：在一个菜单管理系统中，菜单项可以是简单的单项（如一个按钮或链接）或者是复杂的菜单（如包含多个子菜单项的下拉菜单）。组合模式可以将菜单项和菜单容器统一处理，使得无论是普通菜单项还是包含子菜单的符合菜单，都能以相同的方式进行渲染、更新或删除。这样在实现菜单功能时，可以灵活的添加或修改菜单层级结构。

## 组合模式的基本结构

1）抽象组件：定义了所有组件（包括基本组件和容器组件）必须遵循的接口，比如公共的方法，比如添加、删除、获取子组件等。

2）叶子节点：代表最基本的、不能再分的对象，比如普通员工。叶子节点实现了抽象组件定义的接口，但不会再包含子节点。

3）容器节点：代表可以包含子组件的对象，比如部门经理。容器节点同样实现了抽象组合接口，同时内部维护者子节点集合，并实现对子节点的添加、删除等操作。

## 组合模式的实现

下面就以 “文件管理系统” 为例，我们用组合模式实现一个简单的文件结构系统。

1）定义统一的文件组件接口：声明操作方法，比如显示信息。

```java
public interface FileComponent {
    void display(String indent);
}
```

这一步定义了组合模式的 “抽象组件”，统一所有文件系统节点的操作接口，比如 display() 方法用于展示目录结构。

2）实现叶子节点类：表示文件

```java
public class FileLeaf implements FileComponent {
    private String name;

    public FileLeaf(String name) {
        this.name = name;
    }

    public void display(String indent) {
        System.out.println(indent + "- 文件：" + name);
    }
}
```

FileLeaf 是叶子节点类，代表文件，不包含其他子节点，只负责输出自己的名字，是组合结构中最底层的节点。

3）实现组合节点类：表示文件夹

```java
import java.util.ArrayList;
import java.util.List;

public class FolderComposite implements FileComponent {
    private String name;
    private List<FileComponent> children = new ArrayList<>();

    public FolderComposite(String name) {
        this.name = name;
    }

    public void add(FileComponent component) {
        children.add(component);
    }

    public void remove(FileComponent component) {
        children.remove(component);
    }

    public void display(String indent) {
        System.out.println(indent + "+ 文件夹：" + name);
        for (FileComponent child : children) {
            child.display(indent + "  ");
        }
    }
}
```

FolderComposite是组合节点，代表文件夹，可以包含任意数量的子文件或子文件夹，实现递归并展示结构，是组合模式的核心。

4）客户端调用示例：构建并展示一个文件树结构

```java
public class FileSystemClient {
    public static void main(String[] args) {
        // 创建文件
        FileLeaf file1 = new FileLeaf("readme.txt");
        FileLeaf file2 = new FileLeaf("logo.png");
        FileLeaf file3 = new FileLeaf("data.csv");

        // 创建文件夹
        FolderComposite root = new FolderComposite("根目录");
        FolderComposite docs = new FolderComposite("文档");
        FolderComposite images = new FolderComposite("图片");

        // 构建层级关系
        docs.add(file1);
        images.add(file2);

        root.add(docs);
        root.add(images);
        root.add(file3);

        // 显示结构
        root.display("");
    }
}
```

客户端不需要关心节点到底是文件还是文件夹，只调用 display() 方法即可，组合模式让 “整体” 和 “部分” 拥有一致的操作方式，实现类对复杂结构的透明访问。

## 组合模式的优缺点

#### 优点：

- 树形结构的表示：组合模式能够将对象组织成树形结构，适用于表示 “部分-整体” 的层次结构，如文件系统、组织结构等。
- 统一性：客户端可以统一处理单个对象和对象集合，简化了代码的复杂性。在操作时无需区分符合对象与叶子节点，可以统一使用相同的接口进行操作。
- 扩展性：通过递归方式，容易为树形结构添加新的操作或新类型的对象，无需修改已有的代码即可实现新功能。
- 灵活性：组合模式使得对象的组合和解耦组合更加灵活，组合结构可以按需变化。

#### 缺点：

- 过度泛化：组合模式可能会导致设计过于泛化，叶子节点和符合节点使用统一接口，可能会引起不必要的复杂性。如果不加区分，复合节点和叶子节点的功能可能会被过度简化，导致某些操作不符合实际需求。
- 不适合复杂对象：如果对象层次过于复杂，组合模式可能会导致大量的类和接口，增加系统的复杂性，降低维护性。
- 难以控制部分操作：当组合对象非常复杂时，处理某些特定的部分操作可能会变得困难，因为无法简单区分叶子节点和复合节点的行为。



---



## AI 扩展

### 组合模式（Composite Pattern）

组合模式（Composite Pattern）是一种结构型设计模式，用于将对象组合成树形结构，以表示“部分-整体”的层次关系。
它使得客户端可以统一地处理单个对象和组合对象。

### 一、核心思想

> “让叶子节点和容器节点拥有一致的接口。”

比如：

- 文件 和 文件夹
- 员工 和 部门
- 菜单项 和 菜单
- 图形元素 和 图形组

它们都可以被“统一操作”。

### 二、适用场景

组合模式特别适用于：

| 场景                           | 示例            |
| ------------------------------ | --------------- |
| 树形结构                       | 文件系统、DOM树 |
| 部分-整体关系                  | 公司组织架构    |
| 递归结构                       | UI组件          |
| 希望统一处理单个对象和组合对象 | 菜单系统        |

### 三、模式结构

```text
        Component
        /      \
     Leaf    Composite
```

### 四、角色组成

#### 1. Component（抽象组件）

定义所有对象共有的接口。

```java
interface Component {
    void operation();
}
```

#### 2. Leaf（叶子节点）

最小单元，没有子节点。

```java
class Leaf implements Component {
    public void operation() {
        System.out.println("叶子节点");
    }
}
```

#### 3. Composite（组合节点）

内部可以包含子节点。

```java
class Composite implements Component {

    private List<Component> children = new ArrayList<>();

    public void add(Component c) {
        children.add(c);
    }

    public void operation() {
        for (Component child : children) {
            child.operation();
        }
    }
}
```

### 五、完整示例（文件系统）

#### 场景

模拟：

```text
root
 ├── file1.txt
 ├── file2.txt
 └── folder
      └── file3.txt
```

#### 代码实现

##### 抽象组件

```java
interface FileSystemNode {
    void show();
}
```

##### 文件（叶子节点）

```java
class FileNode implements FileSystemNode {

    private String name;

    public FileNode(String name) {
        this.name = name;
    }

    @Override
    public void show() {
        System.out.println("文件: " + name);
    }
}
```

##### 文件夹（组合节点）

```java
import java.util.ArrayList;
import java.util.List;

class FolderNode implements FileSystemNode {

    private String name;

    private List<FileSystemNode> children = new ArrayList<>();

    public FolderNode(String name) {
        this.name = name;
    }

    public void add(FileSystemNode node) {
        children.add(node);
    }

    @Override
    public void show() {
        System.out.println("目录: " + name);

        for (FileSystemNode node : children) {
            node.show();
        }
    }
}
```

##### 测试代码

```java
public class Main {

    public static void main(String[] args) {

        FolderNode root = new FolderNode("root");

        root.add(new FileNode("file1.txt"));
        root.add(new FileNode("file2.txt"));

        FolderNode subFolder = new FolderNode("folder");
        subFolder.add(new FileNode("file3.txt"));

        root.add(subFolder);

        root.show();
    }
}
```

##### 输出结果

```text
目录: root
文件: file1.txt
文件: file2.txt
目录: folder
文件: file3.txt
```

### 六、组合模式的优点

#### 1. 统一操作

客户端无需区分：

- 单个对象
- 对象集合

例如：

```java
node.show();
```

即可统一处理。

#### 2. 易于扩展

新增节点类型时：

- 不需要修改客户端
- 符合开闭原则

#### 3. 天然适合递归

树结构操作非常优雅：

```java
for (Component child : children)
```

递归即可完成整棵树遍历。

### 七、缺点

#### 1. 设计会变得抽象

系统复杂度提高。

#### 2. 不容易限制节点类型

比如：

- 文件夹能加文件
- 但文件不能加文件

统一接口后可能不好约束。

### 八、透明组合模式 vs 安全组合模式

#### 1. 透明组合模式（Transparent）

所有方法放到 Component。

```java
interface Component {
    void add(Component c);
    void remove(Component c);
}
```

##### 优点

统一性强。

##### 缺点

Leaf 不需要 add/remove。

#### 2. 安全组合模式（Safe）

只有 Composite 才有 add/remove。

##### 优点

更安全。

##### 缺点

不够统一。

### 九、实际应用

#### 1. 文件系统

- 文件
- 文件夹

#### 2. Java GUI

如：

- JPanel
- JButton

容器和组件统一处理。

#### 3. XML / HTML DOM

```html
<div>
    <span>Hello</span>
</div>
```

DOM 本质就是组合模式。

#### 4. 公司组织架构

```text
公司
 ├── 技术部
 │     ├── 张三
 │     └── 李四
 └── 财务部
```

### 十、与其他模式区别

| 模式       | 核心目的     |
| ---------- | ------------ |
| 组合模式   | 组织树形结构 |
| 装饰器模式 | 动态增强对象 |
| 代理模式   | 控制对象访问 |
| 适配器模式 | 接口兼容     |

### 十一、组合模式 + 递归的本质

组合模式的真正核心：

```text
对象中包含对象自身类型
```

例如：

```java
List<Component> children;
```

因此可以形成：

- 树
- 递归
- 层级结构

### 十二、一句话总结

> 组合模式通过“树形结构 + 统一接口”，让客户端可以一致地处理单个对象与对象集合。