---
description: 本頁將簡單講述 Hibernate 的方式。
---

# 使用 Hibernate

由於 Hibernate 採用 ORM 的方式，所以在注入 SQLService 之前，你需要先定義一個映射資料表的 `Class`。

```java
@Entity
public class User {

    @Id
    public String username;

    public String firstName;
    public String lastName;
    public String password;

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

所有 Table Class 都要標記為 `@Entity`，並根據 Table 的欄位定義 property，且使用 `@Id` 標記該欄位為 Primary Key。

{% hint style="info" %}
更多 Hibernate 的標註教學可到[這裏](https://www.cnblogs.com/softidea/p/6216722.html)查看。
{% endhint %}

完成後，到主類獲取本插件的安裝器 `SQLInstallation.class` 並註冊你的 table class。

```java
    @Override
    public void bindServices(ServiceCollection serviceCollection) {
        // 獲取安裝器
        SQLInstallation sql = serviceCollection.getInstallation(SQLInstallation.class);
        sql.bindEntities(User.class);
    }
```

## 開始使用服務 <a href="#starttouse" id="starttouse"></a>

與傳統方式不同的是，這次將注入 `Provider<EntityManager>`  以使用 Hibernate ORM。

{% hint style="info" %}
這邊強烈建議使用 `Provider<EntityManager>` 注入而非直接注入 `EntityManager` 以確保每次改寫能使用新的 `EntityManager`。
{% endhint %}

```java
public class UserEMService implements UserService {


    @Inject
    private Provider<EntityManager> entityManagerProvider;


    @Override
    public void save(User user) {
        EntityManager entityManager = entityManagerProvider.get();
        entityManager.getTransaction().begin(); // 打開連接
        entityManager.persist(user); // 儲存方法
        entityManager.getTransaction().commit(); // 關閉連接並提交
    }

    @Override
    public Optional<User> findByUsername(String username) {
        EntityManager entityManager = entityManagerProvider.get();
        return Optional.ofNullable(entityManager.find(User.class, username)); // 使用 primary key 搜索
    }


    @Override
    public Iterable<User> findAll() {
        EntityManager entityManager = entityManagerProvider.get();
        return entityManager.createQuery("select u from User u", User.class).getResultList();
    }

    @Override
    public void deleteById(String username) {
        EntityManager entityManager = entityManagerProvider.get();
        entityManager.getTransaction().begin(); // 打開連接
        entityManager.remove(entityManager.getReference(User.class, username));
        entityManager.getTransaction().commit(); // 關閉連接並提交
    }


}
```

{% hint style="info" %}
修改 Table 時，你需要使用 getTransaction 的 begin 和 commit 方法進行才能成功改寫。
{% endhint %}

關於 EntityManager 的更多詳細教學可參考[這裏](https://openhome.cc/Gossip/EJB3Gossip/EntityManager.html)。
