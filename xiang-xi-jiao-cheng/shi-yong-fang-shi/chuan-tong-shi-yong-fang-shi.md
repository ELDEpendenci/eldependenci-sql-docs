---
description: 本頁將簡單介紹傳統使用方式。
---

# 傳統使用方式

傳統方式，就是透過 SQL 服務，提取連接\(Connection\)，然後執行SQL語法\(Statement\)。此方式需要使用者掌握各種 SQL 語法。

```java
public class SQLManager {


    @Inject
    private SQLService sqlService;

    public void doSQLShit(){
        try (Connection c = sqlService.getConnection(); PreparedStatement s = c.prepareStatement("select * from USer")){
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

正如在快速開始的例子一樣，此方法提取了 User 資料表所有的行數並返回到 ResultSet, 最後打印出每行的 username 欄。

有關使用 jdbc sql 的教學，你可參考[這裏](http://tw.gitbook.net/jdbc/jdbc-statements.html)。

