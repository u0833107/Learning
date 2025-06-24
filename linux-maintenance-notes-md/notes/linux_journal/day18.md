# Day18

Step1：模擬錯誤情境(停掉nginx)

sudo sysyemctl stop nginx

Step2：嘗試 curl 測試網站連線

curl

出現錯誤訊息：Failed to connect to localhost port 80 after 0 ms：Couldn’t connect to server

Step3：進行錯誤排查

利用以下問題進行判斷：

systemctl status nginx 有沒有顯示 active？

sudo ufw status numbered 有封掉 port 嗎？

sudo ss -tulnp 中 nginx 的 port 80 有在 listen 嗎？

journalctl -u nginx --since "10 minutes ago" 有沒有報錯？

結果：發現systemckt status nginx nginx沒有顯示active且sudo ss -tulnp中nginx的port 80也沒有listen

Step4：修復錯誤

利用sudo systemctl start nginx將服務啟動再使用sudo ss -tulnp確定port 80有listen，最後重新測試網站連線

報告版：

補充：修改 nginx port → 8080 並設定防火牆

Step 1：使用編輯器開啟設定檔修改設定

sudo nano /etc/nginx/sites-enabled/default

找到以下內容：

listen 80 default_server;

listen [::]:80 default_server;

改成：

listen 8080 default_server;

listen [::]:8080 default_server;

儲存並離開(Ctrl+O、Enter、Ctrl+X)

Step 2：重新啟動 nginx

sudo nano restart nginx

sudo ss -tulnp | grep nginx   #確認nginx是否監聽8080

Step 3：設定防火牆

sudo ufw deny 80   #封鎖原本的80 port

sudo ufw allow 8080   #允許新的8080 port

sudo ufw status numbered   #查看目前防火牆規則

Step 4：測試是否只有 8080 可連

curl    #測試新的port有沒有通

正常會顯示nginx的畫面

curl    #測試舊的port是否已封鎖

正常會出現connection refused

※若要對外開放服務測試參考C:\Users\milly\OneDrive\桌面\Port_Forwarding_成功紀錄.docx

