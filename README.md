# 23-design-patterns-notes

<div align="center">

![设计模式](https://img.shields.io/badge/设计模式-23种经典模式-blue)
![文档](https://img.shields.io/badge/文档-学习笔记-green)
![进度](https://img.shields.io/badge/进度-持续更新中-orange)

</div>

## 📖 关于本仓库

**23-design-patterns-notes** 是一份全面、系统的设计模式学习笔记，基于 GoF（Gang of Four）《设计模式：可复用面向对象软件的基础》一书中的 23 种经典设计模式。

本仓库的目标是：
- 🎯 **化繁为简**：用通俗易懂的语言和图示解释每种模式
- 💡 **深入浅出**：提供贴近实际场景的代码示例
- 📊 **对比分析**：梳理相似模式的差异与适用场景
- 🔧 **即查即用**：作为日常开发的速查手册

> 持续更新中，欢迎 Star ⭐ 和提 Issue 讨论！

---

## 🗂️ 目录结构

```
23-design-patterns-notes/
├── README.md                      # 本文件
├── SUMMARY.md                     # 所有模式速查表
├── creational-patterns/           # 创建型模式 (5种)
│   ├── singleton.md               #   单例模式
│   ├── factory-method.md          #   工厂方法模式
│   ├── abstract-factory.md        #   抽象工厂模式
│   ├── builder.md                 #   建造者模式
│   └── prototype.md               #   原型模式
├── structural-patterns/           # 结构型模式 (7种)
│   ├── adapter.md                 #   适配器模式
│   ├── bridge.md                  #   桥接模式
│   ├── composite.md               #   组合模式
│   ├── decorator.md               #   装饰器模式
│   ├── facade.md                  #   外观模式
│   ├── flyweight.md               #   享元模式
│   └── proxy.md                   #   代理模式
├── behavioral-patterns/           # 行为型模式 (11种)
│   ├── chain-of-responsibility.md #   责任链模式
│   ├── command.md                 #   命令模式
│   ├── interpreter.md             #   解释器模式
│   ├── iterator.md                #   迭代器模式
│   ├── mediator.md                #   中介者模式
│   ├── memento.md                 #   备忘录模式
│   ├── observer.md                #   观察者模式
│   ├── state.md                   #   状态模式
│   ├── strategy.md                #   策略模式
│   ├── template-method.md         #   模板方法模式
│   └── visitor.md                 #   访问者模式
└── assets/                        # 图片资源
    └── diagrams/                  # UML 类图等
```

---

## 📚 设计模式分类总览

| 类型 | 模式名称 | 一句话描述 | 跳转链接 | 完成状态 |
|------|----------|------------|----------|----------|
| 创建型 | **单例 (Singleton)** | 保证一个类只有一个实例 | [查看笔记](./creational-patterns/singleton.md) | ✅️ |
| 创建型 | **工厂方法 (Factory Method)** | 将对象创建延迟到子类 | [查看笔记](./creational-patterns/factory-method.md) | ✅️ |
| 创建型 | **抽象工厂 (Abstract Factory)** | 创建一系列相关对象 | [查看笔记](./creational-patterns/abstract-factory.md) | ✅️ |
| 创建型 | **建造者 (Builder)** | 分步构建复杂对象 | [查看笔记](./creational-patterns/builder.md) | ✅️ |
| 创建型 | **原型 (Prototype)** | 通过克隆来创建对象 | [查看笔记](./creational-patterns/prototype.md) | ✅️ |
| 结构型 | **适配器 (Adapter)** | 转换接口以兼容协作 | [查看笔记](./structural-patterns/adapter.md) | ✅️ |
| 结构型 | **桥接 (Bridge)** | 分离抽象与实现 | [查看笔记](./structural-patterns/bridge.md) | ✅️ |
| 结构型 | **组合 (Composite)** | 树形结构统一处理 | [查看笔记](./structural-patterns/composite.md) | ✅️ |
| 结构型 | **装饰器 (Decorator)** | 动态添加额外职责 | [查看笔记](./structural-patterns/decorator.md) | ⏳ |
| 结构型 | **外观 (Facade)** | 提供统一高层接口 | [查看笔记](./structural-patterns/facade.md) | ⏳ |
| 结构型 | **享元 (Flyweight)** | 共享细粒度对象 | [查看笔记](./structural-patterns/flyweight.md) | ⏳ |
| 结构型 | **代理 (Proxy)** | 控制对象访问 | [查看笔记](./structural-patterns/proxy.md) | ✅️ |
| 行为型 | **责任链 (Chain of Responsibility)** | 请求沿链传递 | [查看笔记](./behavioral-patterns/chain-of-responsibility.md) | ⏳ |
| 行为型 | **命令 (Command)** | 请求封装为对象 | [查看笔记](./behavioral-patterns/command.md) | ⏳ |
| 行为型 | **解释器 (Interpreter)** | 定义文法并解释 | [查看笔记](./behavioral-patterns/interpreter.md) | ⏳ |
| 行为型 | **迭代器 (Iterator)** | 顺序访问集合元素 | [查看笔记](./behavioral-patterns/iterator.md) | ⏳ |
| 行为型 | **中介者 (Mediator)** | 协调对象间交互 | [查看笔记](./behavioral-patterns/mediator.md) | ⏳ |
| 行为型 | **备忘录 (Memento)** | 捕获并恢复状态 | [查看笔记](./behavioral-patterns/memento.md) | ⏳ |
| 行为型 | **观察者 (Observer)** | 一对多的依赖通知 | [查看笔记](./behavioral-patterns/observer.md) | ⏳ |
| 行为型 | **状态 (State)** | 状态驱动行为变化 | [查看笔记](./behavioral-patterns/state.md) | ⏳ |
| 行为型 | **策略 (Strategy)** | 算法族可互换 | [查看笔记](./behavioral-patterns/strategy.md) | ✅️ |
| 行为型 | **模板方法 (Template Method)** | 定义算法骨架 | [查看笔记](./behavioral-patterns/template-method.md) | ✅️ |
| 行为型 | **访问者 (Visitor)** | 数据和操作分离 | [查看笔记](./behavioral-patterns/visitor.md) | ⏳ |

---

## 🚀 快速开始

### 在线阅读
所有笔记均为 Markdown 格式，可直接在 GitHub 上浏览；

或者点击在线文档进行浏览：https://xiangxiang62.github.io/23-design-patterns-notes/。

### 本地阅读
```bash
# 克隆仓库
git clone https://github.com/xiangxiang62/23-design-patterns-notes.git

# 进入目录
cd 23-design-patterns-notes

# 使用任意 Markdown 阅读器打开
# 推荐：Typora / VS Code / Obsidian
```

---

## 📝 每篇笔记结构

每篇设计模式笔记均按照以下统一结构组织：

1. **模式定义** —— 一句话概括模式的核心思想
2. **问题场景** —— 什么时候需要用到这个模式
3. **UML 类图** —— 可视化展示结构关系
4. **代码示例** —— 贴近实战的代码实现
5. **关键角色** —— 模式中各个类的职责说明
6. **优缺点分析** —— 使用该模式的利弊权衡
7. **适用场景** —— 真实项目中的应用案例
8. **与其他模式对比** —— 相似模式的区分与组合

---

## 🔍 设计模式六大原则

本仓库中的所有模式均体现了以下六大设计原则：

| 原则 | 英文 | 核心思想 |
|------|------|----------|
| 单一职责原则 | SRP | 一个类只负责一件事 |
| 开闭原则 | OCP | 对扩展开放，对修改关闭 |
| 里氏替换原则 | LSP | 子类可以替换父类 |
| 接口隔离原则 | ISP | 接口应该小而专一 |
| 依赖倒置原则 | DIP | 依赖抽象而非具体实现 |
| 迪米特法则 | LoD | 最少知识原则 |

---

## 🤝 贡献指南

欢迎贡献！你可以通过以下方式参与：

1. **提 Issue**：发现错误或有任何建议，欢迎提 Issue 讨论
2. **提 PR**：修正笔误、补充代码示例、增加图解等
3. **分享交流**：Star 本项目并分享给更多开发者

### 贡献步骤
```bash
# Fork 本仓库
# 创建你的特性分支
git checkout -b feature/awesome-improvement

# 提交你的修改
git commit -m '添加了某某模式的真实案例'

# 推送到分支
git push origin feature/awesome-improvement

# 打开一个 Pull Request
```

如果这份笔记对你有帮助，请给一个 Star ⭐ 支持一下！

<div align="center">

**[☝️ 回到顶部](#23-design-patterns-notes)**

</div>