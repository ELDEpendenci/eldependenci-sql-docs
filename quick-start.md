# 快速開始

假若你使用 Maven，則添加以下的依賴:

```markup
<dependency>
     <groupId>org.eldependenci</groupId>
     <artifactId>eldependenci-sql</artifactId>
     <version>{{版本}}</version>
     <scope>provided</scope>
</dependency>
```

{% hint style="info" %}
注意別忘了先掛接 ELDependenci 的依賴！
{% endhint %}

完成後，即可注入 SQLService 的服務。

```java
public class SQLManager {


    @Inject
    private SQLService sqlService;

    public void doSQLShit(){
        try (Connection c = sqlService.getConnection(); PreparedStatement s = c.prepareStatement("select * from User")){
            ResultSet set = s.executeQuery();
            while (set.next()){
                String username = set.getString("username");
                System.out.println(username);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }
}
```

在 `plugin.yml` 新增 depend 之後，編譯即可完成你的第一個掛接eld-sql的插件。

## 安裝

首先，下載本插件，然後讓伺服器進行加載。加載後，你將看到如下的文件被加載:

* db.yml
* hibernate.properties

進入 db.yml 並填入你需要啟動的 資料庫 類型和基本資料:

```yaml
# 啟用 eld-sql, 不啟用無法掛接服務
enable: true
# 選擇你的資料庫類型，目前選擇 sqlite
dataSource: "sqlite"

sqlite:
  file: "central.db"

mysql:
  host: 127.0.0.1
  port: 3306
  database: database
  username: username
  password: password
  useSSL: false
  pool:
    name: SQL-Pool
    minSize: 5
    maxSize: 10
    connectionTimeout: 30000
    idleTimeout: 60000
    maxLifeTime: 600000
```

修改完畢後，放入你的插件並重啟伺服器，便能完成掛接使用了。

