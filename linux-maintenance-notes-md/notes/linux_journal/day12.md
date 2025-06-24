# Day12

一個簡單的資源監控自動化腳本

nano ~/resource_monitor.sh

#!/bin/bash

LOG=”/home/milly/resource_monitor.log”

echo “======$(date)======”>> $LOG

echo “[UPDATE]”>> $LOG

uptime >> $LOG

echo “[DISK USAGE]”>> $LOG

df -h >> $LOG

echo “[TOP 5 CPU]”>> $LOG

ps aux –sort=-%cpu | head -n 6 >> $LOG   //紀錄cpu佔最多的5個行程

echo >> $LOG

chmod +x /home/milly/resource_monitor.sh   //給腳本執行權限

//加入排程

crontab -e

*/10 * * * * /home/milly/resource_monitor.sh

crontab -l   //確認排程生效

//驗證

tail -n 50 /home/milly/resource_monitor.log

補充1：ps aux --sort=-%cpu | head -n 6 >> $LOG

ps aux：列出目前所有行程，a代表所有使用者的行程，u代表所有使用者與CPU/記憶體占用比例，x代表包含沒有連接終端的行程

--sort=-%cpu：依照%CPU占用比例遞減排序，-代表由大到小，%cpu是欄位名稱

head -n 6：列出輸出前6行(第一行是標題列)，所以會顯示CPU使用率最高的前五個行程

>> $LOG：把結果用追加寫入的方式寫到$LOG指定的log檔中

補充2：Log重複誤判

看

