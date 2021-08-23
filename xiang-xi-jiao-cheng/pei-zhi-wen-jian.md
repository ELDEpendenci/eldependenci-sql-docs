---
description: 本頁將集中講述配置文件。
---

# 配置文件

在 db.yml 中，你會發現 mysql 欄位不止出現了 host / port / database 名稱等基本資料，還有一些額外的設定。

```yaml
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

其實這些設定都是 [hikariCP](https://github.com/brettwooldridge/HikariCP) 中創建連接池所需要的設定，因此關於這些設定，你應該到 hikariCP 的文件設定中查看相關教學。

### hibernate.properties

顧名思義，這是 Hibernate 的設定文件。但由於插件已經內部填入了基本設定，因此你可以在不碰它的情況下使用本框架提供的 Hibernate 服務。如果你需要額外的設定，則可使用該配置文件進行設置。

有關詳細的教學可自行查詢 [Hibernate 的文件配置教學](https://www.javatpoint.com/hibernate-configuration)。

