# MySQL 3306 未監聽問題 - 完整排查與修正報告


### 問題描述

在 Ubuntu 虛擬機中，MySQL 雖然服務狀態為 active，但使用 ss/netstat 等工具確認時，發現埠 3306 並未監聽，導致無法從本機或其他主機連線。


### 初步確認項目

- MySQL 為 active (sudo systemctl status mysql)

- 設定檔中已包含 bind-address = 0.0.0.0 以及 port = 3306

- journalctl 未顯示明顯錯誤訊息

- ss -tulnp 未顯示 3306 被監聽


### 排查與修正步驟

1. 編輯 /etc/mysql/mysql.conf.d/mysqld.cnf

2. 確認 [mysqld] 區塊中包含以下設定：

 bind-address = 0.0.0.0

 port = 3306

 skip-networking = 0

3. 儲存後重啟 MySQL：`sudo systemctl restart mysql`

4. 使用 `sudo ss -tulnp | grep 3306` 驗證監聽狀態


### 修正結果

修正後再次執行 ss 顯示：0.0.0.0:3306 被 mysqld 正常監聽，表示 MySQL 已成功開啟 TCP 連線功能，允許內網主機遠端連線。

建議的安全性補強（UFW）

- 僅允許內網存取：`sudo ufw allow from 10.0.2.0/24 to any port 3306`

- 阻擋其他來源：`sudo ufw deny 3306`

（本報告可作為未來遇到類似問題的參考案例）