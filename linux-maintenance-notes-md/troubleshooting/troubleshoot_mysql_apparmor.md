MySQL 無法啟動與 AppArmor 權限修復流程筆記
問題起始點
已經將 /var/lib/mysql 權限恢復為 mysql:mysql 並設為 750，但執行 `sudo systemctl restart mysql` 時仍顯示錯誤：
`Job for mysql.service failed because the control process exited with error code.

錯誤訊息排查
使用以下指令查看詳細錯誤：
 - `sudo systemctl status mysql.service`
 - `sudo journalctl -xeu mysql.service`
 - `sudo journalctl -u mysql.service -b | grep -i error`

AppArmor 狀態與處理
查看 AppArmor 啟用狀態：
 sudo aa-status

發現 `mysqld` profile 被 disable（因 /etc/apparmor.d/disable/ 中有連結）。需手動啟用：
 sudo rm /etc/apparmor.d/disable/usr.sbin.mysqld
 sudo apparmor\_parser -r /etc/apparmor.d/usr.sbin.mysqld
 ```
 
修改 AppArmor 設定檔
編輯 AppArmor 設定檔：
 sudo nano /etc/apparmor.d/usr.sbin.mysqld

插入以下區塊（建議加在 log 權限設定前）：

 # 允許 MySQL 存取資料庫目錄
 /var/lib/mysql/ r,
 /var/lib/mysql/** rwk,

 # 允許 MySQL 存取執行所需目錄
 /var/run/mysqld/ r,
 /var/run/mysqld/** rwk,

 # 若使用 socket 檔案，也需要加入
 /var/lib/mysql/mysql.sock rw,

 並確認開頭是 `{`，結尾是 `}`，不是舊語法 `[`。

最終確認
重啟 MySQL 並確認狀態：
 sudo systemctl restart mysql
 sudo systemctl status mysql

 確認 AppArmor 正常啟用：
 sudo aa-status


附錄：磁碟空間不足處理
在操作過程中若出現：
 `you don't have enough free space in /var/cache/apt/archives`
 可使用以下方式清理空間：
 sudo apt clean
 sudo du -h / --max-depth=2 2>/dev/null | sort -hr | head -n 20
 sudo df -h
 檢查與釋放 / 分割區空間後再嘗試安裝或重啟服務。
 