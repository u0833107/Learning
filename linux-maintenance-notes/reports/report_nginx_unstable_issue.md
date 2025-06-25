# 系統維運報告：Nginx異常(網站無法連線)

### 發生時間

2025/06/16 15:21:15(UTC+8)

### 問題描述

使用者無法連線nginx網站，並且連線結果顯示Couldn’t connect to server。

### 處理過程

使用下列指令確認nginx服務狀態

`sudo systemctl status nginx`

發現nginx狀態顯示inactive

啟動nginx服務

`sudo systemctl start nginx`

出現錯誤Job for nginx.service failed because control process exited with error code

利用下列指令修正設定檔

`sudo nano /etc/nginx/nginx.conf`

將錯誤程式碼bad_directive_purpose刪除

再次啟動nginx服務

`sudo systemctl start nginx`

利用`nginx -t`驗證避免錯誤

測試連線

`curl http://localhost`

### 修復時間

2025/06/16 15:57:18(UTC+8)

### 預防改善

每次修改完設定檔利用nginx -t驗證，避免錯誤。

### 結論與回報方式

此問題為常見錯誤，可做SOP方便日後遇見相同問題時參考