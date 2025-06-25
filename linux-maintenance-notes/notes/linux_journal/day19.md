# Day19 MySQL權限與使用者管理

建立新使用者
`CREATE USER ‘appuser’@’localhost’IDENTIFIED ‘password123’;`

賦予權限

語法：`GRANT 權限 ON 資料庫.資料表 TO '使用者'@'主機';`

`GRANT SELECT ON linux_practice.users TO ‘appuser’@’localhost’;`

驗證權限

`EXIT;`   #先登出

`mysql -u appuser -p`   #用appuser帳號登入

```bash
#選擇資料庫並查詢

USE linux_practice;

SELSCT * FROM users;

查詢成功

INSERT INTO users (name,email) VALUES (‘Hacker’,hacker@example.com);   #嘗試插入一筆新的資料

正常會顯示權限不足
```

```bash
查看與撤除權限

SHOW GRANTS FOR ‘appuser’@’localhost’;   #查看目前權限

REVOKE SELECTION ON linux_practice.users FROM ‘appuser’@’localhost’;   #撤銷查詢權限
```

再用appuser登入一次並嘗試選擇資料庫並查詢會被拒絕

補充1

EX1：建一個readonly帳號，只能讀取資料庫，不能寫入

```bash
CREATE USER ‘readonly’@’localhost’ IDENTIFIED BY ‘readonly123’;

GRANT SELECT ON linux_practice.users TO ‘readonly’@’localhost’;
```

EX2：建立devuser，可以對 linux_practice 資料庫做全部操作

```bash
CREATE USER ‘devuser’@’localhost’ IDENTIFIED BY ‘devuser123’

GRANT ALL PRIVILEGES ON linux_practice.* TO ‘devuser’@’localhost’;

ALL PRIVILEGES 包含 SELECT、INSERT、UPDATE、DELETE、CREATE、DROP 等等
```

補充2

查詢目前所有使用者（含 Host）

`SELECT USER, HOST FROM mysql.user;`

補充3

權限對照參考C:\Users\milly\OneDrive\桌面\MySQL權限範圍對照表.docx

