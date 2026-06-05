## 外观模式

### 什么是外观（门面）模式？

**外观模式** 是一种结构型设计模式，它的核心思想是：**为复杂的子系统提供一个统一的对外接口**，通过这是个接口，我们就能更方便、更高效的访问这些子系统，而不需要关心他们的内部复杂逻辑。

比如去银行办业务，准备办理一笔贷款，结果银行里面有一堆系统：一个管审核资料，一个管征信查询，一个管资金调度。好在前台小姐姐一句话：您只需要把身份证交给我就可以了。突然就感觉轻松了很多。

这个场景就是外观模式的魅力。它帮我们屏蔽了内部系统的复杂性，只保留一个干净的、面向用户的接口，让调用方用起来更省心。你不需要知道每个子系统是怎么工作的，只需要和前台打交道就行。

在开发中，我们也经常封装一些 “外观类”，比如整合多个工具类，SDK，业务模块。对外统一暴露一个xxxFacade，简化调用流程。这样不仅降低了耦合度，还提升了代码的可读性和维护性。

### 为什么要使用外观模式？

开发中我们经常会遇到一种情况：系统内部有很多模块，比如数据库、缓存、第三方接口、日志系统等，而外部调用者（比如前端、接口调用者）根本没必要也不想知道这些模块是怎么运作的。我们如果直接暴露所有子系统的接口，不仅使用麻烦，而且耦合度高，系统也不易维护。

这时候我们通过引入外观模式，定义一个对外统一的访问入口，这个入口来协调和管理各个子系统的调用流程，这样，外部调用者只需要跟这个 “入口类” 打交道，调用起来很方便，系统的接口也更加清晰，解耦。

### 外观模式的应用场景

- 多模块复杂系统整合：在一些大型的业务系统中，往往会有多个子模块（订单、支付、物流等）。通过外观模式，可以为外部接口提供一个简单的接口，让外部系统通过外观类与各个模块进行交互，避免直接调用复杂的子系统接口，简化了系统的操作。例如，在电商系统中，创建一个 “订单管理” 外观类，将订单、支付、库存、配送等模块整合为一个简单接口，提供给前端调用。
- 数据库操作封装：对于一些复杂的数据库操作，可能需要多个步骤才能完成（如数据查询、转换、保存等）。使用外观模式可以为数据库操作提供一个简单的外观类接口，隐藏复杂的查询细节和操作等，减少开发人员的认知负担。例如，设计一个数据访问外观类，它封装了常见的 CRUD 操作，让其他业务层无需关心数据库的具体实现。
- 复杂的第三方服务集成：在项目中，如果需要集成多个第三方服务（如支付服务、短信服务、邮件服务等），使用外观模式可以统一对外提供一个简洁的接口，避免各个业务层与第三方服务的直接交互。例如在一个电商系统中，可以创建一个 “支付外观类”，统一处理支付宝、微信等支付方式的调用，使回复逻辑更加简单易用。

### 外观模式的基本结构

1）外观类：对外提供统一的接口，封装子系统的调用；

2）子系统类：负责自身的业务逻辑处理，通常不会对外暴露；

3）客户端：通过外观类来使用子系统的功能。

### 外观模式的实现

下面就以 “数据库操作封装” 为例，我们用外观模式实现一个简化的用户数据访问系统。

1）定义实体类：表示用户的数据结构

```java
public class User {
    private int id;
    private String name;

    public User(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```



2）定义底层 DAO 接口和实现：封装原始数据库操作（模拟实现）

```java
public interface UserDAO {
    void insert(User user);
    User findById(int id);
    void update(User user);
    void delete(int id);
}
```

```java
import java.util.HashMap;
import java.util.Map;

public class UserDAOImpl implements UserDAO {
    private Map<Integer, User> database = new HashMap<>();

    public void insert(User user) {
        database.put(user.getId(), user);
        System.out.println("插入用户：" + user.getName());
    }

    public User findById(int id) {
        System.out.println("根据 ID 查询用户：" + id);
        return database.get(id);
    }

    public void update(User user) {
        database.put(user.getId(), user);
        System.out.println("更新用户：" + user.getName());
    }

    public void delete(int id) {
        User removed = database.remove(id);
        System.out.println("删除用户：" + (removed != null ? removed.getName() : "未知"));
    }
}
```

这一步实现了基本的数据库操作，但接口比较底层，业务层如果直接调用，还是比较繁琐，不利于复用和维护。

3）定义外观类：封装 CRUD 操作，简化调用方式。

```java
public class UserServiceFacade {
    private UserDAO userDAO;

    public UserServiceFacade() {
        this.userDAO = new UserDAOImpl(); // 实际开发中可注入
    }

    public void createUser(int id, String name) {
        User user = new User(id, name);
        userDAO.insert(user);
    }

    public void viewUser(int id) {
        User user = userDAO.findById(id);
        if (user != null) {
            System.out.println("用户信息：ID=" + user.getId() + "，姓名=" + user.getName());
        } else {
            System.out.println("未找到对应的用户");
        }
    }

    public void renameUser(int id, String newName) {
        User user = userDAO.findById(id);
        if (user != null) {
            User updatedUser = new User(id, newName);
            userDAO.update(updatedUser);
        } else {
            System.out.println("无法重命名，用户不存在");
        }
    }

    public void removeUser(int id) {
        userDAO.delete(id);
    }
}
```

这一步封装了对 UserDAO 的所有操作，把复杂的数据库调用包装成对业务开发更友好的接口，降低调用门槛。

4）客户端调用示例：通过外观类完成数据库操作

```java
public class Client {
    public static void main(String[] args) {
        UserServiceFacade userService = new UserServiceFacade();

        userService.createUser(1, "香香");
        userService.viewUser(1);
        userService.renameUser(1, "琨斤拷");
        userService.viewUser(1);
        userService.removeUser(1);
        userService.viewUser(1);
    }
}
```

输出结果

````
插入用户：香香
根据 ID 查询用户：1
用户信息：ID=1，姓名=香香
根据 ID 查询用户：1
更新用户：琨斤拷
根据 ID 查询用户：1
用户信息：ID=1，姓名=琨斤拷
删除用户：琨斤拷
根据 ID 查询用户：1
未找到对应的用户
````

通过 UserServiceFacade，我们把增删改查封装成简单的函数调用，业务层不需要再和底层 DAO 打交道，开发效率和代码可维护性大大提升了。

### 外观模式的优缺点

#### 优点：

- 简化调用逻辑：外观模式最大的作用就是把系统的复杂度挡在门外，对于使用者来说，只需要跟一个外观类打交道，不用去理会底层到底有多少子系统，具体是怎么实现的，调用起来简单又清爽。
- 降低耦合度：外观类起到一个中间人的作用，让调用者和内部子系统之间的联系变得松散。换句话说，你要是将来改了底层实现，只要外观接口不动，上层代码基本不用动，维护成本低。
- 有利于分层架构设计：在一些分层接口的系统中，比如三层架构，外观类可以作为门卫，帮助管理每一层之间的依赖关系，让层与层之间的解耦变得更加干净，结构也更加清晰。

#### 缺点：

- 功能扩展时不够灵活：一旦客户端习惯了只跟外观类打交道，那要想用到子系统里一些比较细节的功能，就要回头修改外观类，或者绕过它。这个时候，外观反而成了一种限制。
- 可能会变 “万能类”：如果设计的不太合理，外观类很容易膨胀，什么事情都往它身上堆，最后可能会变成一个大杂烩，职责太多，反而不利于维护。
- 隐藏了部分系统细节：虽然这本身是优点，但有时候也是一把双刃剑，如果所有东西都被外观包装了起来，开发者可能会对系统内部的真实结构缺乏了解，排错的效率也会受到影响。