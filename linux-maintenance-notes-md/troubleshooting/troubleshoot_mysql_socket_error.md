MySQL Socket異常但服務仍啟動—排查流程筆記


情境描述


將 MySQL 的 socket 設定為不存在的路徑（如 /this/path/does/not/exist/mysql.sock）後：
- 使用 `sudo systemctl restart mysql`，服務仍顯示啟動成功
- 使用 `sudo mysql --protocol=socket` 則報錯無法連線

問題核心解析

MySQL 的啟動條件僅檢查主程式 mysqld 是否成功執行。

即使 socket 檔案無法建立，只要沒有 crash，systemd 還是會顯示「Active: running」。

但 client 端（如 mysql CLI）無法透過指定 socket 連線，就會報錯。

驗證方式
1. 確認服務是否啟動：
 `sudo systemctl status mysql`
2. 嘗試透過 socket 登入：
 `sudo mysql --protocol=socket`
3. 確認實際 socket 是否建立：
 `sudo lsof -nP | grep mysql | grep sock`

4. 查看啟動 log 是否有 socket 錯誤：
 `sudo journalctl -u mysql --since "5 minutes ago"`
小結與重點

維運上，這類問題常發生在「設定錯誤但服務看起來正常」的情境。
建議使用 systemctl + journalctl + socket 實體檢查一起排查。