# Day7

查閱系統日誌

journalctl -n 50：顯示最後50行的記錄

journalctl | grep “error”：篩選出帶有error的記錄

觀察系統負載狀況(監控指令)

top

uptime

02:56:25 up 13 minutes, 1 user, load average:0.07,0.13,0.14

02:56:25：系統目前時間

up 13 minutes：系統已連續開機多久

1 user：有幾個使用者帳號目前登入系統

load average:0.07,0.13,0.14：系統平均負載，分別是過去1分鐘；過去5分鐘；過去15分鐘

df -h

df：disk filesystem

-h：human readable(把KB/MB/GB顯示成易讀的單位)

free -h

free：顯示記憶體空間與Swap狀況

補充1：swap是虛擬記憶體，當RAM不夠用時，會使用硬碟空間暫存資料

補充2：建立一個2G的swap檔案

sudo fallocate -l 2G /swapfile   //先建立一個2G的swap檔案

sudo chmod 600 /swapfile   //設定權限只有root可讀寫

sudo mkswap /swapfile   //建立swap區塊

sudo swapon /swapfile   //啟用swap

free -h   //最後用free -h確定有建成功

打以下指令讓系統開機自動啟用swap

echo ‘/swapfile none swap sw 0 0’ | sudo tee -a /etsc/fstab

du -shf

du：顯示disk usage磁碟用量

-s：summary，只列出總計，不列出每個子資料夾

