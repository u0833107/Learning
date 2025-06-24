# Day9

備份 MySQL 資料庫

`sudo mysqldump linux_practice > home/deployuser/linux_practice_bakup.sql`

mysqldump：MySQL的備份工具

linux_practice：要備份的資料庫名稱

>：把備份輸出導向一個.sql檔

檢查備份檔案

輸入以下兩個指令

`ls -lh /home/deployuser/linux_practice_bakup.sql`

`head /home/deployuser/linux_practice_bakup.sql`

會看到以下以下內容

-- MySQL dump 10.13  Distrib ...

CREATE DATABASE ...

INSERT INTO ...

補充：

問題：路徑對，檔名對，為什麼用head看不到CREATE DATABASE…這些內容，改用cat就可以？

原因：因為head只能看到前10行，cat可以顯示整份檔案，而.sql前面通常是一堆註解和設定內容，真正的CREATE DATABASE和INSERT INTO等內容通常在20行後。

還原.sql檔案(模擬災害復原)

```bash
DROP DATABASE linux_practice;`   //刪除資料庫

EXIT`
```

回到shell模式

//確認還原是否成功

`sudo mysql < /home/deployuser/linux_practice_bakup.sql`

補充：

Q：輸入sudo mysql < /home/deployuser/linux_practice_bakup.sql出現ERROR 1046 (3D000): No database selected

發生原因：通常發生在備份檔內沒有包含CREATE DATABESE或USE語句的情況下，MySQL會不知道要把備份資料匯到那個資料庫

解決方式：

解法1：先手動建立資料庫在執行還原

`sudo mysql`   //進入mysql模式

```bash
CREATE DATABESE linux_practice;

EXIT;
```

`sudo mysql linux_practice < /home/deployuser/linux_practice_bakup.sql`

解法2：確認.sql裡有沒有CREATE DATABASE和USE語句

`grep -i “create database”/home/deployuser/linux_practice_bakup.sql`

如果以上指令輸入後沒出現CREATE DATABASE和USE語句代表備份十枚包含資料庫建立語句。

若要讓mysqldump包含CREATE DATABESE和USE，可以在備份時加上—databases參數，如下：

`sudo mysqldump --databases linux_practice > linux_practice_bakup.sql`

這樣在還原時就不用先建立資料庫，直接使用以下指令：

`sudo mysql < linux_practice_bakup.sql`

