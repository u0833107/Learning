# Day11

Part I以nginx當機為例

sudo systemctl status nginx   //查詢nginx目前的狀態

sudo journalctl -u nginx –since “10 minutes ago”   //查詢10分鐘前的log

!也可以用no-page查看所有的紀錄

sudo journalctl -u nginx --no-page | tail -n 30   //查詢所有nginx的log紀錄並顯示最後的(最新的)30行

模擬nginx當機

sudo systemctl stop nginx   //停止nginx服務

curl    //嘗試訪問網站

會出現Connection Refused

查詢錯誤記錄

sudo journalctl -u nginx –since “5 minutes ago”

也可以使用以下指令看錯誤日誌

sudo less /var/log/nginx/error.log

修復&重啟

sudo systemctl restart nginx   //重啟nginx服務

curl    //再訪問一次，有跑出內容代表成功

補充：停掉nginx，再用 crontab安排5分鐘後自動systemctl start nginx

sudo systemctl stop nginx

sudo nano /bin/crontab

加入以下排程

5 * * * *   root   /usr/bin/systemctl start nginx

等五分鐘後

curl    //訪問後有出現內容代表成功

詳細除錯內容看

模擬nginx.conf寫錯，測試systemctl restart 是否報錯再修正

sudo nano /etc/nginx/nginx.conf   //進入nginx.conf加入一段無效語法

例如：

在http區塊中加入bad_directive_on_purpose;

sudo systemctl restart nginx   //嘗試重啟nginx

會看到以下內容：

Job for nginx.service failed because the control process exited with error code.

See "systemctl status nginx.service" and "journalctl -xeu nginx.service" for details.

sudo nginx -t   //驗證設定錯誤細節

有錯誤會輸出以下內容

nginx: [emerg] unknown directive "bad_directive_on_purpose" in /etc/nginx/nginx.conf:35

nginx: configuration file /etc/nginx/nginx.conf test failed

sudo journalctl -u nginx –since “5 minutes ago”

或 sudo less /var/log/nginx/error.log

//修正錯誤

sudo nano /etc/nginx/nginx.conf

將bad_directive_on_purpose刪除或註解

sudo nginx -t   //再測試一次

sudo systemctl restart nginx   //重啟nginx服務

curl    //若看到Welcome to nginx就代表正確

補充：sudo nginx -t

功能用途：

解析 nginx 所有設定檔

檢查語法正不正確

檢查引用的檔案路徑是否存在

不會實際啟動 nginx，只會顯示驗證結果

Part II以MySQL當機為例

模擬MySQL設定檔語法錯誤(my.cnf)

sudo nano etc/mysql/my.cnf   //編輯mysql主設定檔

故意加上一行錯誤語法，例如：this_is_invaild_directive

儲存後執行以下指令：

sudo systemctl restart mysql

會出現錯誤訊息：Job for mysql.service is failed…

//驗證錯誤訊息

sudo systemctl status mysql

sudo journalctl -u mysql --since “10 minutes ago”

//修正錯誤

sudo nano /etc/mysql/my.cnf   //再次打開mysql主設定檔

將錯誤語法this_is_invaild_directiv刪除

儲存後用以下指令：

sudo systemctl restart mysql

可以看到服務正常啟動了

模擬MySQL權限或socket問題

sudo systemctl stop mysql   //停止mysql服務

sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf   //暫時改錯socket檔案位置

找到以下(通常會在mysql區塊裡)

socket  =  /var/run/mysqld/mysqld.sock

將路徑改成錯誤的路徑，例如：/this/path/does/not/exist/mysql.sock

儲存後重啟mysql服務

sudo systemctl restart mysql

這時會發現服務還是可以正常重啟並沒有跳出錯誤訊息

利用以下指令才會產生報錯

sudo mysql --protocol=socket

ERROR 2002 (HY000): Can't connect to local MySQL server through socket ...

sudo journalctl -u mysql --since "5 minutes ago"   //查Log

原因：MySQL啟動成功≠可連線

詳細部分看

將路徑改回正確後可以用mysql -u root測試連線是否正常

補充模擬情境：讓MySQL資料夾權限錯誤

sudo chmod 000 /var/lib/mysql

sudo systemctl restart mysql

這時會跳出錯誤訊息如下：

Job for mysql.service failed because the control process exited with error code.

See "systenctl status mysql.service" and "journalctl -xeu mysql.service" for details.

sudo journalctl -u mysql --since “10 minutes ago”//查看錯誤紀錄

會看到Failed to start mysql.service-MySQL Community Server.

sudo chmod 755 /var/lib/mysql   //恢復權限

sudo chown -R mysql:mysql /var/lib/mysql

sudo systemctl restart mysql   //重啟服務

若遇上已經恢復權限但mysql服務可能原因為AppArmor限制了MySQL對/var/lib/mysql的讀寫即使檔案權限正確也一樣無法進入。

解法看

