# Day8

安裝

`sudo apt update`

`sudo apt install mysql-server`

`sudo systemctl status mysql`   //檢查安裝是否成功，成功會看見active(running)

建立資料庫與資料表

`sudo mysql`   //進入mysql視窗

```bash
CREATE DATABASE practice_linux;   //創一個叫做linux_practice的資料庫

USE practice_linux;`


//創建一個叫user的資料表(欄位包含id、姓名和信箱)


CREATE TABLE users(

id INT AUTO_INCREMENT PRIMARY_KEY,

name VARCHAR(100),

email VARCHAR(100)

);

//新增資料

INSERT INTO users (name,email) VALUES

(‘Alice’,’alice@example.com’),

(‘Bob’,’bob@example.com’);

//查詢資料表

SELECT * FROM users;

EX1：查詢所有email包含example的使用者

SELECT * FROM users WHERE email LIKE ‘%example%’;

EX2：新增一位使用者

INSERT INTO users (name,email) VALUES (‘Charlie’,’charlie@example.com’);

EX3：刪除一位 email 是 bob@example.com 的使用者

DELETE FROM users WHERE email = ‘bob@example.com’;
```

補充：UPDATE

```bash
UPDATE 資料表名稱

SET 欄位名稱 = 新值

WHERE 條件;

EX1：把Alice的名字改成Alicia

UPDATE users

SET name = ‘Alicia’

WHERE email = ‘alice@example.com’;

EX2：把所有名字是Bob的使用者email改為bob@newmail.com

UPDATE users

SET email = ‘bob@newmail.com’

WHERE name = ‘Bob’;

EX3：讓name為Charlie的使用者名字改成大寫CHARLIE並順便更新email為charlie@linux.com

UPDATE users

SET name = ‘CHARLIE’,

email = ‘charlie@linux.com’

WHERE name = ‘Charlie’;
```

