虛擬機 Port Forwarding 實戰紀錄
本紀錄說明如何在 VirtualBox 中使用 Port Forwarding，讓本機可以透過 http://localhost:8080 存取虛擬機內的 nginx 服務（port 8080）。

Step 1：修改 nginx 的 port
在虛擬機內執行：
sudo nano /etc/nginx/sites-enabled/default

# 修改以下兩行：
listen 80 → 改為 listen 8080;
listen [::]:80 → 改為 listen [::]:8080;

儲存後重啟 nginx：
sudo systemctl restart nginx

Step 2：設定防火牆
開放 8080、封鎖 80：

sudo ufw allow 8080
sudo ufw deny 80

Step 3：設定 VirtualBox Port Forwarding
1. 關閉虛擬機
2. 開啟 VirtualBox → 選取該 VM → 設定 → 網路 → NAT → 進階 → 埠轉發
3. 新增規則如下：

|     名稱    |    協定    |    主機 IP    |    主機 Port    |    客體 IP    |    客體 Port    |
|:-----------:|:----------:|:-------------:|:---------------:|:-------------:|:---------------:|
|    nginx    |     TCP    |               |       8080      |               |       8080      |

Step 4：測試結果
在本機瀏覽器輸入：http://localhost:8080
畫面正確顯示 nginx 頁面，代表埠轉發設定成功，服務對外可用！