Day 11：排程實戰 & 除錯總整理筆記
三種 crontab 使用場景比較

 systemctl 在 crontab 中常見錯誤
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
！推薦使用/etc/crontab執行高權限維運任務（如 systemctl、mysqldump、rsync 等）！