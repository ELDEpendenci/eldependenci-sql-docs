---
description: 本頁將簡單介紹使用 Jpa Repository 的方式
---

# 使用 JPA Repository

{% hint style="info" %}
JPA Repository 方式是由 Spring Data JPA 基於 hibernate 建置的更方便進行 sql 操作的 api。因此，其註冊方式是跟 Hibernate 的註冊方式相同的。關於 Hibernate 的註冊方式請細閱上頁的 [使用Hibernate](../shi-yong-hibernate.md) 。
{% endhint %}

根據 Hibernate 所註冊的方式完成後，你需要先創建一個 interface , 並繼承  `JpaRepository<Entity, ID>`。

```java
public interface UserRepository extends JpaRepository<User, String> {
}
```

在上述的 generic 中， `Entity` 代表了 table class, 而 `ID` 則代表了那個 table 的 primary key 類型。

完成後，你需要到主類註冊你的 `Repository`。

```java
@ELDPlugin(
        registry = TesterRegistry.class,
        lifeCycle = TesterLifeCycle.class
)
public class ELDTester extends ELDBukkitPlugin {


    @Override
    protected void bindServices(ServiceCollection serviceCollection) {

        SQLInstallation sql = serviceCollection.getInstallation(SQLInstallation.class);

        sql.bindEntities(User.class); // 註冊 entities
        sql.bindJpaRepository(UserRepository.class); // 註冊 Repository

    }

    @Override
    protected void manageProvider(ManagerProvider provider) {

    }
}
```

## 使用服務

註冊完成後，只需要直接注入你的 Repository 即可。

```java
public class UserJpaService implements UserService {


    @Inject
    private UserRepository userRepository;

    @Override
    public void save(User user) {
        userRepository.save(user);
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

從上述代碼中，你可發現使用 JPA Repository 比使用 EntityManager 更簡潔，也更可讀。

{% hint style="info" %}
除了使用 JpaRepository 所提供的方法外，你也可以自建自己的獨特方法以執行SQL的操作。我們將在後頁講解。

除此以外，Spring Data JPA 還提供排序，分頁，過濾 等等的方法，這裏便不再一一敘述。

其他有關更多 Spring Data JPA 的詳細教學可參閱[這裏](https://docs.spring.io/spring-data/jpa/docs/2.5.3/reference/html/#jpa.query-methods)。
{% endhint %}



