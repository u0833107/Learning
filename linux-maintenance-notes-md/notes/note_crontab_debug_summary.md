Day 11：排程實戰 & 除錯總整理筆記
三種 crontab 使用場景比較
|        類型        |      位置/編輯方式     |     執行者設定    |         適用場景         |                         優缺點                         |
|:------------------:|:----------------------:|:-----------------:|:------------------------:|:------------------------------------------------------:|
| 使用者個人 crontab | crontab -e（無 sudo）  | 自動以目前使用者執行 | 輕量自動化任務，如同步資料、寄信 | 優：簡單設定<br>缺：無法用 systemctl 等需高權限指令 |
| root 個人 crontab  | sudo crontab -e        | 以 root 執行       | 高權限備份、維運任務      | 優：具 root 權限<br>缺：某些版本對 systemctl 支援不穩定 |
| 系統 crontab       | /etc/crontab           | 必須明確指定使用者欄位 | 最穩定的系統級排程，如自動啟動服務 | 優：100% 支援高權限操作、明確角色控制<br>缺：格式需小心，多一欄 user 欄位 |


systemctl 在 crontab 中常見錯誤
|            問題現象           |                       常見原因                     |                          解法建議                         |   |   |
|:-----------------------------:|:--------------------------------------------------:|:---------------------------------------------------------:|:-:|:-:|
|      指令沒被執行、無反應     |     指令使用相對路徑，cron   環境缺少 PATH         |     使用完整路徑`/usr/bin/systemctl`                      |   |   |
|      sudo: no tty present     |     在user crontab 中使用'sudo'且未配置sudoers     |     避免用sudo，改用root crontab或 `/etc/crontab`         |   |   |
|     nginx未啟動，log無紀錄    |     crontab被寫進錯帳號/未儲存/cron沒啟動          |     使用`sudo crontab -l`和`systemctl status cron`確認    |   |   |
|      crontab已設定但cron沒跑  |     cron daemon未啟動/寫入時間與時區不符           |     用`date`+`timedatectl`確認時區，啟動`cron`服務        |   |   |
|        log沒顯示執行紀錄      |     Ubuntu 24.04預設cron log不進/var/log/syslog    |     使用journalctl -u cron`查實際執行紀錄                 |   |   |

journalctl log 解讀技巧
使用指令：sudo journalctl -u cron --since "10 minutes ago"
常見 log 類型說明：
session opened for user root – 排程準備執行（已啟動）
CMD (...) – 實際執行的指令內容（成功排入）
session closed – 指令執行結束
❗️若只看到 session 沒看到 CMD ➜ 排程內容沒正確執行！

/etc/crontab 範本與說明
範例：
30 2 * * * root /usr/bin/systemctl restart nginx
欄位意義：
|          欄位         |                 意義               |
|:---------------------:|:----------------------------------:|
|      m(第一個*號)     |             分鐘（0–59）           |
|      h(第二個*號)     |             小時（0–23）           |
|     dom(第三個*號)    |               每月幾號             |
|     mon(第四個*號)    |                 月份               |
|     dow(第五個*號)    |           星期幾（0=Sun）          |
|          user         |     要用誰的身分執行（如 root）    |
|         command       |             要執行的指令           |
！推薦使用/etc/crontab執行高權限維運任務（如 systemctl、mysqldump、rsync 等）！