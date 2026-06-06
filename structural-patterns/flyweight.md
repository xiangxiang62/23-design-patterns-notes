## 享元模式

### 什么是享元模式？

**享元模式** 是一种结构型设计模式，它的核心思想是：通过共享对象，减少系统中对象的数量，从而节省内存，提高性能。在很多应用场景中，我们经常会遇到需要大量创建相似对象的情况，而这些对象之前其实有很多内容是可以共享的。享元模式就是专门针对这种情况提出的：把可以共享的部门提取出来，放在一个公共对象中，只保留那些不可共享的部门，减少重复的创建，从而节省资源开销。

我们之前在学校上课的时候，每个人都会发一本教科书。想一想，其实每个人的课本内容是一样的，只是使用者不同。如果每个人都印一本新的，那资源就浪费了。我们完全可以共用一本电子教材，每个人阅读的时候，传递进去自己的用户信息，这样既然节省了纸张，也节省了印刷成本。这就和享元模式非常类似：共用相同的部分，把变化的部分单独管理。

所以享元模式很适合那种重复多、变化少的场景，帮我们有效减少内存开销，提升系统性能。

### 为什么要使用享元模式？

在系统开发中，有些对象的数量非常庞大，比如地图上的每一颗树、游戏中每一颗子弹、文档编辑器里的每一个字符。如果为每个元素都创建独立的对象，内存开销是巨大的，系统的响应速度也会下降。使用享元模式，我们可以把那些相同的数据抽取出来，做成可以共享的对象，多个使用场景共用同一份数据，只针对变化的部门做单独处理。这样既节省了内存，又能提升整体系统的性能。

### 享元模式的应用场景

- 文本编辑器中的字符缓存：在文本编辑器中，可能会有大量相同的字符（如重复出现的字母、符号等）。而每个字符的显示样式（如字体、颜色、大小）可能是相同的。通过享元模式，可以将共享的字符属性（如字形、字体）提取出来作为享元对象，只保存那些需要变化的属性（如位置、颜色），减少重复存储，提升系统效率。
- 网页中的图标缓存：在一个网页应用中，可能需要显示大量图标，很多图标都是重复使用的（比如社交媒体的图标、按钮图标等）。通过享元模式，可以将这些共享图标的显示细节（如图形、颜色、大小等）抽象为享元对象，而将特定位置、状态等动态变化的部分保存在其他地方，而节省内存，提高响应速度。

### 享元模式的基本结构

1）抽象享元：定义享元对象的接口，规定外部状态传入的标准方法。

2）具体享元：实现抽象享元接口，内部保存可以共享的状态。

3）享元工厂：负责创建和管理享元对象，确保相同的享元对象只被创建一次，并进行共享。

4）客户端：通过享元工厂获取享元对象，同时传入外部状态进行使用。

### 享元模式的实现

1）定义享元接口：声明共享对象的标准方法

```java
public interface Glyph {
    void render(int x, int y, String color);
}
```

这一步的目的是定义所有可共享字符的统一操作接口，比如字符的 render()

2）实现具体享元类：封装可以被共享的内部状态

```java
public class CharacterGlyph implements Glyph {
    private final char symbol; // 内部状态，不随使用变化
    private final String font; // 内部状态：字体

    public CharacterGlyph(char symbol, String font) {
        this.symbol = symbol;
        this.font = font;
    }

    @Override
    public void render(int x, int y, String color) {
        System.out.println("渲染字符 '" + symbol + "'，字体：" + font + "，颜色：" + color + "，位置：(" + x + "," + y + ")");
    }
}
```

这一步是享元模式的核心：把所有字符都标准化，只保留不变的信息，比如字符本身和字体，这些都是可以被多个对象共享。

3）创建享元工厂：用于管理和复用享元对象

```java
import java.util.HashMap;
import java.util.Map;

public class GlyphFactory {
    private Map<String, Glyph> glyphPool = new HashMap<>();

    public Glyph getGlyph(char symbol, String font) {
        String key = symbol + ":" + font;
        if (!glyphPool.containsKey(key)) {
            glyphPool.put(key, new CharacterGlyph(symbol, font));
        }
        return glyphPool.get(key);
    }
}
```

这一步提供了享元对象的几种管理。无论创建多少次相同的字符，只要字体一样，就只会创建一个共享实例。

4）定义包含外部状态的字符视图类：包括位置、颜色等变化信息

```java
public class CharacterView {
    private final Glyph glyph;
    private final int x;
    private final int y;
    private final String color;

    public CharacterView(Glyph glyph, int x, int y, String color) {
        this.glyph = glyph;
        this.x = x;
        this.y = y;
        this.color = color;
    }

    public void draw() {
        glyph.render(x, y, color);
    }
}
```

这一步把变化的外部状态提取出来，像位置、颜色都不参与共享，这样不同位置的字符可以使用同一个 Glyph 对象。

5）客户端调用：

```java
public class Client {
    public static void main(String[] args) {
        GlyphFactory factory = new GlyphFactory();

        CharacterView[] page = new CharacterView[] {
            new CharacterView(factory.getGlyph('a', "Arial"), 10, 10, "black"),
            new CharacterView(factory.getGlyph('b', "Arial"), 20, 10, "black"),
            new CharacterView(factory.getGlyph('a', "Arial"), 30, 10, "red"),
            new CharacterView(factory.getGlyph('a', "Arial"), 40, 10, "blue"),
            new CharacterView(factory.getGlyph('b', "Arial"), 50, 10, "black"),
        };

        for (CharacterView cv : page) {
            cv.draw();
        }
    }
}
```

输出结构：

```
渲染字符 'a'，字体：Arial，颜色：black，位置：(10,10)
渲染字符 'b'，字体：Arial，颜色：black，位置：(20,10)
渲染字符 'a'，字体：Arial，颜色：red，位置：(30,10)
渲染字符 'a'，字体：Arial，颜色：blue，位置：(40,10)
渲染字符 'b'，字体：Arial，颜色：black，位置：(50,10)
```

通过这种方式，多个相同字符共享了同一个 CharacterGlyph 实例，只在需要时添加变化的位置信息，大大减少内存开销。

### 享元模式的优缺点

#### 优点：

- 节省内存：享元模式通过共享对象来减少内存的使用。当多个对象拥有相同的状态的时候，可以通过共享这些对象来避免重复创建，尤其是需要大量相似对象的时候，节省了大量内存。
- 提升性能：通过对象的共享，减少对象的创建和销毁，尤其是在大量对象需要频繁创建的场景中，能够显著提高系统性能，减少系统开销。
- 支持大规模对象管理：享元模式特别适用于需要管理大量对象的场景，如图像编辑、文字排版。它能够通过共享内存，提高对象的使用效率，适应大规模的对象管理需求。

#### 缺点：

- 增加了复杂性：享元模式需要管理享元池（对象池）和分离内外部状态，设计和实现上可能比直接创建对象要更加复杂。需要仔细处理对象共享和状态管理，避免引入不必要的复杂性。
- 对象状态管理困难：享元模式的共享对象往往是不可变的。若对象的状态是可变的，需要将内部状态和外部状态分开管理，这可能会增加代码的复杂度和维护难度。
- 可能导致资源竞争：当多个对象共享同一个实例的时候，如果共享的对象没有正确同步，可能会导致资源竞争或线程安全问题，尤其是在多线程的环境下，必须格外小心。