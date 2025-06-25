# Day3 Linux軟體與服務管理

`sudo apt install htop`：安裝htop安裝包

`sudo apt update`：從官方套件庫拉取最新版本資訊(更新套件資訊)

`sudo apt remove htop`：移除htop

查看目前執行中的服務

`systemctl list-units --type=service --state=running`：查看目前執行中的系統服務

操作服務

`sudo systemctl status cron`：查看cron服務的狀態

`sudo systemctl start cron`：啟動cron服務

`sudo systemctl stop cron`：關閉cron服務

`sudo systemctl restart cron`：重啟cron服務

設定服務開機自動啟動/取消

`sudo systemctl enable cron`：開啟開機自動啟動cron服務

`sudo systemctl disable cron`：關閉開機自動啟動cron服務

