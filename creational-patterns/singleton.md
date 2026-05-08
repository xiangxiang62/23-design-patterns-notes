## 什么是单例模式

单例模式是一种创建型设计模式，一个类在整个应用运行期间，只能有一个实例，而且这个实例对外一共一个全局访问点。

无论是从哪调用这个类，拿到的永远都是同一个对象。

像数据库连接池、线程池、缓存管理器这些东西，如果每次用都重新 new，一个项目跑不了几天内存就炸了。所以我们通常只让它们有一个唯一实例，整个系统共享，这样不仅节约资源，还可以统一管理。

## 为什么使用单例模式？

1. 节省资源：控制实例数量，避免在高频访问场景中反复创建销毁；
2. 统一管理：全局只有一个入口，方便统一初始化和清理，比如统一配置、日志级别控制；
3. 线程安全：好的单例实现可以天然保证多线程环境下只有一次创建，避免竞态条件。
4. 与其他模式结合：单例尝尝作为工厂、抽象工厂等模型的基础组件，为更复杂的结构性模式提供支持。

## 单例设计模式的

1. 私有化构造器：禁止外部直接 new，这样才能确保外部拿不到新对象；
2.  持有唯一实例的静态变量：通常写成 private static Singleton instance；程序启动或首次访问的时候再创建；
3. 全局访问点：提供一个 public static getInstance() 方法，外部就通过这个方法拿到唯一实例；
4. 线程安全：在多线程场景下，还得保证并发时也只有一份，常见做法有加锁、双检锁、静态内部类 或者直接用枚举；

## 单例模式的应用场景

- 全局唯一 ID 生成器：在电商、内容平台等高并发系统中，需要全局唯一的订单号、用户 ID、消息 ID 等，通常会实现一个雪花算法（Snowflake）或时间戳+随机数生成器，作为单例使用，保证线程安全和唯一性。
- 系统配置管理类：很多应用在启动时会加载一份配置（如系统参数、限流配置、第三方服务地址等），通过单例模式保证在内存中只加载一次，全局共享光，避免重复读取和资源浪费。
- 应用级缓存组件：例如一些基础数据（地区列表、标签分类、活动配置等）缓存到内存中，需要多个模块共享，通常封装成单例类，提供统一的读写接口，保证数据一致性。

## 单例模式的几种实现

### 饿汉式

饿汉式是在类加载阶段就完成实例化，保证从第一次访问该类型到程序结束，全局只有这一个实例。它依赖 JVM 的类加载机制来确保线程安全。

```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() { }
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

### 懒汉式

懒汉式在第一次调用 getInstance() 时才创建实例，通过对该方法加锁来保证线程安全，适合对启动性能有要求且实例不一定马上需要的场景。

```java
public class Singleton {
    private static Singleton instance;
    private Singleton() { }
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

### 双检锁

双检锁结合了懒汉式的延迟加载和饿汉式的高性能，首次创建时加锁，后续访问则跳过同步块，从而减少锁开销。

```java
public class Singleton {
    private static volatile Singleton instance;
    private Singleton() { }
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

### 静态内部类

利用 JVM 在加载外部类时并不立即加载内部类的特性，将实例的创建延迟到真正访问内部类时。既能延迟加载，又能借助类加载的线程安全特性。

```java
public class Singleton {
    private Singleton() { }
    private static class Holder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

### 枚举式

利用 Java 枚举类型的特性，枚举值在类加载时就创建，JVM 保证枚举实例的线程安全和唯一性。同时，枚举对序列化和反射攻击有天然的防护能力。

```jaVA
public enum Singleton {
    INSTANCE;
}
```

## 单例模式的优缺点

### 优点：

- 全局唯一，资源可控：单例模式可以保证某个类在系统中始终只有一个实例，这对一些需要全局共享资源的场景，比如配置管理器、线程池、缓存等，非常使用，既节省资源又避免冲突。
- 提供统一访问点：通过统一的 getInstance() 方法获取实例，不用每次都去 new，代码更加简洁，管理也更集中，有时候还能结合懒加载，提升性能。
- 便于扩展为多线程安全版本：只要设计得当，比如加锁、使用双检锁或静态内部类方式，都能很好的保证线程安全，让单例在多线程环境下也能稳定运行。

### 缺点：

- 不利于测试和扩展：单例一旦写死了，很难在单元测试时替换成 mock 对象，也不容易在运行时切换成别的实现，不利于灵活扩展和测试隔离；
- 可能引发隐藏的状态问题：由于单例在多个地方被共享使用，
- 生命周期不可控：单例通常在 JVM 生命周期内部存在，不容易销毁，容易导致资源无法及时释放。如果单例中持有了重资源，比如数据库链接或者文件句柄，可能造成内存泄漏。



## 问题：

#### 请解释什么是单例模式，并给出一个使用场景？

#### 单例模式有哪几种实现？如何保证线程安全？



