## 访问者模式

### 什么是访问者模式？

访问者模式是一种行为型设计模式，它的核心思想是：**将数据结构和作用于数据结构**

说白了，就是原来的类我不想动它，但是我又想多做点事，这时候就请一个 “外部角色” 来干活。这个角色我们叫它 “访问者”，它会主动去拜访每个对象，挨个执行自己的操作。

比如我们有一个 “员工信息系统”。里面记录了所有员工的信息，人事部想计算绩效奖金，财务想算税，IT 想统计电脑配置。按正常思路，如果都把这些逻辑写在员工类里，那类就炸了，职责全堆在一起了。用访问者模式，我们就把这些操作分给不同的对象，让它们自己来处理员工信息。

每个员工类只需要提供一个 accept(Visitor v) 方法，访问者自己带着 “我要干的事” 过来，就和员工说：“我来算你这个月的工资”，员工高兴的点个头：“可以呀”，就结束了。

这个模式的最大好处就是：扩展性强，不破坏原结构，逻辑清晰，职责明确。如果后续新增一个 “安全部门” 来查岗，那只需要新增一个访问者类，不用动员工类半行代码。

### 为什么要使用访问者模式？

在实际开发中，有时候我们面对的数据结构是相对稳定的，但是需要不断的新增功能或者操作。如果我们直接在数据结构的类里加方法，类会不断膨胀，变得难以维护，而且维护了单一职责原则和开闭原则。

通过访问者模式，我们可以很优雅的把新增的功能封装成访问者类，数据结构本身不懂，只需要开放一个接受访问者的方法（accept 方法），就可以实现新功能的扩展，大大提高了系统的灵活性和可维护性。

### 访问者模式的应用场景

- 文档处理系统重的格式转换：对于一个文档处理系统，可能需要处理各种格式的文档（如 PDF、Word、Excel 等），而每种文档格式的解析与转换逻辑不同。访问者模式可以用来定义一个通用的操作接口，允许对不同类型的文档进行格式转换处理，扩展时无需修改文档类的代码，只需要增加新的转换访问者。
- 编译器设计：在编译器中，源代码通常会被解析为抽象语法树。不同类型的节点（如类、函数、表达式）会有不同的处理逻辑。使用访问者模式，编译器可以在不修改节点类的情况下，针对不同节点执行特定的操作，如语法检查、代码优化等。

### 访问者模式基本结构

1）抽象元素：这是所有元素的基类或者接口，声明了接受访问者的 accept() 方法。每个元素类都会实现该方法，用来将访问者传递给自己。

2）具体元素：每个具体元素都实现了抽象元素的 acept() 方法，通常这个方法会将自己传递给访问者，让访问者执行特定的操作。

3）抽象访问者：定义了针对每个具体元素类的操作，每个具体访问者都实现这个接口，并且为每个元素类提供不同的操作。

4）具体访问者：实现了抽象访问者接口，并为每个具体元素提供特定的操作。

5）对象结构：包含一组元素对象，提供一个 accept() 方法，允许访问者访问结构中的每个元素。

### 访问者模式的实现

下面就以 "文档格式转换" 为例，我们使用访问者模式实现一个简单的文档处理系统。

1）定义访问者接口：声明针对不同文档类型的转换方法。

```java
public interface DocumentVisitor {
    void visit(PDFDocument pdf);
    void visit(WordDocument word);
    void visit(ExcelDocument excel);
}
```

这一步定义了访问者的统一接口，每种文档类型（PDF、Word、Excel）对应一个访问方法，用于封装 “对文档进行格式转换” 的逻辑入口。

2）定义文档元素接口：提供接受访问者的方法。

```java
public interface Document {
    void accept(DocumentVisitor visitor);
}
```

这个文档类型实现这个接口，就可以 ”接受“ 访问者，调用相应的转换方法。通过双分派机制，把访问者逻辑交给访问者处理，文档本身无需关心。

3）实现具体文档类：如 PDF、Word、Excel

```java
public class PDFDocument implements Document {
    private String content;

    public PDFDocument(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }

    @Override
    public void accept(DocumentVisitor visitor) {
        visitor.visit(this);
    }
}

public class WordDocument implements Document {
    private String content;

    public WordDocument(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }

    @Override
    public void accept(DocumentVisitor visitor) {
        visitor.visit(this);
    }
}

public class ExcelDocument implements Document {
    private String[][] table;

    public ExcelDocument(String[][] table) {
        this.table = table;
    }

    public String[][] getTable() {
        return table;
    }

    @Override
    public void accept(DocumentVisitor visitor) {
        visitor.visit(this);
    }
}
```

每个文档类都实现 accept 方法，并在其中调用访问者对应方法，把自身 this 传过去。这样访问者就可以获取到文档数据并处理，而文档类不需要知道怎么处理。

4）实现具体访问者类：将文档转换为 HTML 格式

```java
public class HtmlExportVisitor implements DocumentVisitor {

    @Override
    public void visit(PDFDocument pdf) {
        System.out.println("<html><body><h1>PDF 内容</h1><p>" + pdf.getContent() + "</p></body></html>");
    }

    @Override
    public void visit(WordDocument word) {
        System.out.println("<html><body><h1>Word 内容</h1><p>" + word.getContent() + "</p></body></html>");
    }

    @Override
    public void visit(ExcelDocument excel) {
        System.out.println("<html><body><h1>Excel 内容</h1><table border='1'>");
        for (String[] row : excel.getTable()) {
            System.out.print("<tr>");
            for (String cell : row) {
                System.out.print("<td>" + cell + "</td>");
            }
            System.out.println("</tr>");
        }
        System.out.println("</table></body></html>");
    }
}
```

这个访问者类实现了将文档转换为 HTML 的具体逻辑。每种文档转换方式不同，但是他们共用一个访问者接口，使得扩展变得灵活又统一。

5）客户端调用示例

```java
public class Client {
    public static void main(String[] args) {
        Document pdf = new PDFDocument("这是 PDF 文件的内容");
        Document word = new WordDocument("这是 Word 文档的内容");
        Document excel = new ExcelDocument(new String[][] {
            {"姓名", "成绩"},
            {"鱼皮", "90"},
            {"Yes哥", "95"}
        });

        DocumentVisitor htmlExporter = new HtmlExportVisitor();

        pdf.accept(htmlExporter);
        word.accept(htmlExporter);
        excel.accept(htmlExporter);
    }
}
```

输出结果：

```
<html><body><h1>PDF 内容</h1><p>这是 PDF 文件的内容</p></body></html>
<html><body><h1>Word 内容</h1><p>这是 Word 文档的内容</p></body></html>
<html><body><h1>Excel 内容</h1><table border='1'><tr><td>姓名</td><td>成绩</td></tr><tr><td>鱼皮</td><td>90</td></tr><tr><td>Yes哥</td><td>95</td></tr></table></body></html>
```

客户端通过访问者 HtmlExportVisitor 完成了对多个文档类型的统一格式转换。访问者模式让新增新的转换方式（比如导出为 Markdown 或 HTML）变得非常容易，而无需修改原有文档类的任何代码。

### 访问者模式的优缺点

#### 优点

- 扩展性强：访问者模式可以让我们在不改变元素类（被访问者类）的情况下，增加新的操作。这样一来，功能扩展就变得非常简单，只需要在访问者中添加新功能即可，符合开闭原则。
- 集中操作：将操作集中在访问者类中，避免了在各个元素中重复实现相同的操作，使得系统的修改和维护更加集中和清晰。
- 灵活性高：访问者模式让你可以针对不同类型的元素执行不同的操作，灵活性也非常高，特别适用于需要对对线结构中不同的元素做不同操作的情况。

#### 缺点

- 增加了类的数量：访问这模式需要为每个不同的操作创建一个访问者类，这会导致系统中类的数量增加，进而使得系统变得复杂。
- 修改元素类困难：虽然增加新操作很方便，但一旦要修改元素类的结构，就会影响到所有的访问者，需要同步更新访问者中的相关代码，导致维护成本增加。
- 不适合频繁变化的对象结构：访问者模式适合结构比较稳定的场景，如果对象结构经常变化，添加新的元素时，就必须修改所有的访问者类，这样就违背了它的初衷，增加了维护的难度。























