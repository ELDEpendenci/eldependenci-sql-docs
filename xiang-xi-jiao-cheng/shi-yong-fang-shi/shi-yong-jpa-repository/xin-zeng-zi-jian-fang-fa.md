---
description: 本頁將講解在 JpaRepository 的自建方法。
---

# 新增自建方法

Spring Data JPA 的自建方法有兩種，第一種是解析 method name 去執行 SQL Query，範例如下:

```java
public interface UserRepository extends JpaRepository<User, String> {

    // exists -> 檢查是否存在 (返回 boolean)
    // User -> table User
    // ByUsername -> 根據 username 欄位
    // 參數 username -> 查詢參數
    boolean existsUserByUsername(String username);

}
```

更多的格式和寫法其實在 Spring Data JPA documentation 中已有提供，本頁便不多敘述。

{% hint style="info" %}
[Spring Data JPA 文件](https://docs.spring.io/spring-data/jpa/docs/2.5.3/reference/html/#jpa.query-methods)中第 6.3.2 部分擁有查詢的格式寫法列表。 
{% endhint %}

## 自建擴展實作

本篇主要集中講述第二種，也就是自建擴展實作。

首先，你需要創建一個 Interface 並創建實作它的 class 。這邊例子將以自建兩個擴展實作:

```java
public interface CustomARepo {

    void doSpecialThing(User user);

}
```

```java
public class CustomARepoImpl implements CustomARepo {

    @Inject
    private Provider<EntityManager> entityManagerProvider; // 在自建擴展實作中，可以使用 Inject

    @Override
    public void doSpecialThing(User user) {
        EntityManager em = entityManagerProvider.get();
        // 使用 EntityManager 操作...
    }
}
```

```java
public interface CustomBRepo {

    void doSomeThingCool(User user);

}
```

```java
public class CustomBRepoImpl implements CustomBRepo {


    @Inject
    private ELDTester eldTester;

    @Override
    public void doSomeThingCool(User user) {
        eldTester.getLogger().info(user.toString());
    }
}
```

在上述範例中，我們創建了兩個 interface 並實作了它們。

然後，我們將在之前的例子 `UserRepository` 中，繼承它們。

```java
public interface UserRepository extends JpaRepository<User, String>, CustomARepo, CustomBRepo {

}
```

最後，到主類註冊。這次註冊將會添加 自建實作擴展的 class。

```java
@ELDPlugin(
        registry = TesterRegistry.class,
        lifeCycle = TesterLifeCycle.class
)
public class ELDTester extends ELDBukkitPlugin {


    @Override
    protected void bindServices(ServiceCollection serviceCollection) {

        SQLInstallation sql = serviceCollection.getInstallation(SQLInstallation.class);

        sql.bindEntities(User.class);
        // 第一個參數是 repository, 第二個參數開始就是 自建擴展實作
        sql.bindJpaRepository(UserRepository.class, CustomARepoImpl.class, CustomBRepoImpl.class);

    }

    @Override
    protected void manageProvider(ManagerProvider provider) {

    }
}
```

### 使用

註冊後，你便可以透過注入 `UserRepository` 的方式使用自建的方法。

```java
public class UserJpaService implements UserService {


    @Inject
    private UserRepository userRepository;

    @Override
    public void save(User user) {
        userRepository.save(user);

        // 自建方法
        userRepository.doSpecialThing(user);
        userRepository.doSomeThingCool(user);
    }

    @Override
    public Optional<User> findByUsername(String username) {
        return userRepository.findById(username);
    }

    @Override
    public Iterable<User> findAll() {
        return userRepository.findAll();
    }

    @Override
    public void deleteById(String username) {
        userRepository.deleteById(username);
    }


}
```

{% hint style="info" %}
你可創建無限多個的擴展實作。
{% endhint %}

