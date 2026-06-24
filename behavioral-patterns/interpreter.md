## 解释器模式

### 什么是解释器模式？

解释器模式是一种行为型设计模式，它的主要作用是**为了特定类型的语言提供解释执行的方法**。

简单来说，解释器模式就像是 “**给机器提供一个规则，让它能够理解我们输入的内容并根据规则来执行。”**

比如家里的遥控器，按一下 “音量+” 就是发出一个简单的命令，这时候电视机要能理解 “音量+” 是要加音量而不是换台，就得有一套规则去解释这个指令。再比如你看到 ✅️ 就知道是 “同意”，❌ 就是 “不同意”，人脑能理解，但如果让程序去处理这些表情符号，就得教它这套规则，也就是写一个解释器。

### 为什么要使用解释器模式？

在开发过程中，有时候我们的系统需要处理一些用户自定义的、结构清晰的表达式或者指令。如果用硬编码的方式去处理各种情况，不仅代码臃肿，而且非常难维护。每增加一种新的语法，就要修改一堆 if-else，非常不利于扩展。

而解释器模式把语法规则和执行逻辑封装到各个解释器对象中，既符合开闭原则，也让系统更容易维护和扩展。如果将来要有新的规则，我们只需要新的解释器就可以了，不需要大改原有逻辑。

### 解释器模式的应用场景

- 自定义规则引擎：在风控、营销、权限等系统中，业务规则经常需要用类似 “age > 18 && score < 600” 这样的表达式进行配置。使用解释器可以将这些规则表达式解析成语法树，动态执行并得出结果，避免频繁修改代码。
- DSL（领域特定语言）解析：一些项目中需要用户定义自己的业务流程、命令、查询语法（如自定义查询语言、自动化脚本语言）。解释器模式适合将这些语法结构翻译为可执行的命令或操作。
- 表达式解析：在实现计算器、权限表达式（如 hasRole('admin') && isAuthenticated()）、公式引擎等场景中，经常需要解析并计算一类语法表达式。解释器模式通过构建抽象语法树（AST），递归地解释每个节点，从而支持灵活的表达式解析与扩展，便于后续添加新运算符或函数，保持系统的可维护性与可扩展性。

### 解释器模式基本结构

解释器模式具有的角色和职责：

1）抽象表达式：定义所有解释器的通用接口，通常包含一个 interpret() 方法，负责解释和计算表达式。

2）终结符表达式：实现 interpret() 方法的具体类，表达具体的语法元素。这些元素是无法再分解的最小单位。

3）非终结符表达式：与终结符表达式不同，它们用于表示语言中的组合元素，可以包含其他表达式作为其子表达式。

4）上下文：包含了解释过程中所需要的数据，例如变量的值或当前和环境。

5）客户端：客户端负责构建并初始化解释器中的表达式，给定输入后，启动解释过程。

 ### 解释器模式的实现

下面就以 “自定义规则引擎” 为例，我们用解释器模式实现一个简单的规则引擎，能够动态解析并执行类型 age > 18 && score < 500 这样的规则表达式。

1）定义表达式接口：声明规则表达式的执行方法

```java
interface Expression {
    boolean interpret(Context context);  // 解释表达式，返回布尔值
}
```

先定义一个 Expression 接口，其中 interpret() 方法用来解释规则表达式并返回布尔值结果。interpret() 接受 Context 作为参数，Context 中保存了规则的上下文信息（如 age 和 score）。

2）定义上下文类：存储规则所需要的参数

```java
class Context {
    private int age;
    private int score;

    public Context(int age, int score) {
        this.age = age;
        this.score = score;
    }

    public int getAge() {
        return age;
    }

    public int getScore() {
        return score;
    }
}
```

Context 类用于保存规则所需的上下文信息，像 age 和 score 这样的变量可以动态传递给规则引擎。

3）实现具体的表达式类：分别解析具体的规则

```java
class GreaterThanExpression implements Expression {
    private String field;
    private int value;

    public GreaterThanExpression(String field, int value) {
        this.field = field;
        this.value = value;
    }

    @Override
    public boolean interpret(Context context) {
        if ("age".equals(field)) {
            return context.getAge() > value;
        } else if ("score".equals(field)) {
            return context.getScore() > value;
        }
        return false;
    }
}

class LessThanExpression implements Expression {
    private String field;
    private int value;

    public LessThanExpression(String field, int value) {
        this.field = field;
        this.value = value;
    }

    @Override
    public boolean interpret(Context context) {
        if ("age".equals(field)) {
            return context.getAge() < value;
        } else if ("score".equals(field)) {
            return context.getScore() < value;
        }
        return false;
    }
}

class AndExpression implements Expression {
    private Expression expr1;
    private Expression expr2;

    public AndExpression(Expression expr1, Expression expr2) {
        this.expr1 = expr1;
        this.expr2 = expr2;
    }

    @Override
    public boolean interpret(Context context) {
        return expr1.interpret(context) && expr2.interpret(context);
    }
}
```

- GreaterThanExpression 和 LessthanExpression 类是具体的规则表达式，分别判断某个字段（如 age 或 score）是否大于或小于某个值。
- AndExpression 用来组合两个表达式，表示逻辑 “与” 操作。

4）解析动态输入的规则表达式：实现灵活的规则解析

```java
class RuleParser {
    public static Expression parse(String rule) {
        String[] tokens = rule.split("&&");  // 按“&&”分割表达式
        Expression left = null;
        Expression right = null;

        for (String token : tokens) {
            token = token.trim();
            if (token.contains(">")) {
                String[] parts = token.split(">");
                String field = parts[0].trim();
                int value = Integer.parseInt(parts[1].trim());
                left = new GreaterThanExpression(field, value);
            } else if (token.contains("<")) {
                String[] parts = token.split("<");
                String field = parts[0].trim();
                int value = Integer.parseInt(parts[1].trim());
                right = new LessThanExpression(field, value);
            }
        }
        return new AndExpression(left, right);  // 合并左右表达式
    }
}
```

RuleParser 类实现了规则字符串的解析。它能够灵活地解析动态输入的规则表达式（例如 “age” > 10 && score < 600），通过拆分符号来确定每个表达式的类型（如大于、小于等）。

5）客户端调用示例：解析并执行动态规则表达式。

```java
public class Client {
    public static void main(String[] args) {
        // 用户输入的动态规则
        String rule = "age > 18 && score < 500";

        // 创建上下文对象
        Context context = new Context(20, 450);  // 假设 age = 20, score = 450

        // 解析规则表达式
        Expression expression = RuleParser.parse(rule);

        // 判断规则是否成立
        boolean result = expression.interpret(context);
        System.out.println("表达式结果: " + result);  // 输出结果
    }
}
```

输出结果：

```
表达式结果：true
```

使用解释器模式，我们可以动态解析并执行业务规则表达式。用户输入的规则（如 age > 18 && score < 500）会被解析为一颗语法树，规则引擎会逐个判断每个表达式，最终得出一个布尔结果。

### 解释器模式的优缺点

#### 优点：

- 容易扩展：解释器模式通过使用递归结构，能够方便地扩展新的语法规则。只要有新的语法需求，就可以在原有的基础上添加新的解释器类，保持系统的扩展性。
- 清晰的结构：这种模式将不同的语法规则和解释逻辑分开，使得整个解析过程非常清晰易懂。每种规则、有对应的解释器，职责明确，便于维护和修改。
- 灵活性高：解释器模式使得对于复杂表达式的处理变得更加灵活，可以根据需要添加不同的解释规则来适应不同的场景。

#### 缺点：

- 类的数量过多：如果语法规则非常复杂，需要为每个规则编写一个对应的解释器类，这可能导致系统中类的数量答复增加，增加了代码的复杂度。
- 性能问题：解释器模式通过递归调用进行解析，对于大量的数据或复杂的表达式，可能会引发性能问题。尤其是在处理大规模输入的时候，递归调用的栈深度可能影响系统的执行效率。
- 不适合简单场景：如果语法规则非常简单，使用解释器模式可能就显得有些过于复杂，反而增加了不必要的开销。在这种情况下，采用其他模式可能会更加简单和高效。