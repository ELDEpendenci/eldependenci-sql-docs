---
description: 本頁將介紹要如何安裝外來的自定義數據源
---

# 安裝自定義的數據源

目前本插件有提供一定數量的數據源，包括但不限於 MySQL, SQLite 等等。這些數據源的設定都需要在 `db.yml` 中去設定。然而，有時候你仍然需要去創建一個本插件內沒有提供的數據源，又或者需要掛接到在團隊核心插件已經創建的數據源上。

在 `v0.0.6` 版本後，你將可以安裝自定義的數據源。首先需要到 `db.yml` 把數據源設置成 `custom`

```yaml
enable: true # 設置啟動 eld-sql
dataSource: "custom" # 設置為自定義

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

然後，在 `SQLInstallation` 中注入你所自定義的數據源。

```java
    @Override
    protected void bindServices(ServiceCollection collection) {
        SQLInstallation sqlInstallation = collection.getInstallation(SQLInstallation.class);
        DataSource dataSource = ... // 獲取你的數據源
        sqlInstallation.injectDataSource(dataSource, MySQL8Dialect.class);
    }
```