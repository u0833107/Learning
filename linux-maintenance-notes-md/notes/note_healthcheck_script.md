健康檢查與自動修復腳本 - 綜合維運實戰
 腳本說明

本腳本會檢查兩個常見服務（nginx 與 mysql）的狀態：
- 若服務異常，會自動重啟，並記錄重啟次數
- 若重啟超過 3 次，模擬發出警告通知
- 額外記錄系統的記憶體剩餘狀態與前五名 CPU 使用程序

腳本內容（health\_check.sh）

#!/bin/bash

LOG="/home/milly/health\_check.log"
TIMESTAMP=$(date "+%F %T")
NGINX\_RESTART\_FILE="/home/milly/nginx\_restart\_count.txt"
MYSQL\_RESTART\_FILE="/home/milly/mysql\_restart\_count.txt"

echo "[$TIMESTAMP] 健康檢查開始" >> $LOG

# 初始化記錄檔
[ ! -f "$NGINX\_RESTART\_FILE" ] && echo 0 > "$NGINX\_RESTART\_FILE"
[ ! -f "$MYSQL\_RESTART\_FILE" ] && echo 0 > "$MYSQL\_RESTART\_FILE"

# 檢查 nginx 狀態
if ! systemctl is-active --quiet nginx; then
 echo "[$TIMESTAMP] nginx 未啟動，將重新啟動" >> $LOG
 sudo systemctl restart nginx
 echo "[$TIMESTAMP] nginx 重啟完成" >> $LOG

 COUNT=$(cat "$NGINX\_RESTART\_FILE")
 COUNT=$((COUNT + 1))
 echo $COUNT > "$NGINX\_RESTART\_FILE"

 if [ "$COUNT" -gt 3 ]; then
 echo "[$TIMESTAMP] nginx 重啟超過 3 次，模擬發送通知 email" >> $LOG
 fi
else
 echo "[$TIMESTAMP] nginx 運作正常" >> $LOG
 echo 0 > "$NGINX\_RESTART\_FILE"
fi

# 檢查 mysql 狀態
if ! systemctl is-active --quiet mysql; then
 echo "[$TIMESTAMP] mysql 未啟動，將重新啟動" >> $LOG
 sudo systemctl restart mysql
 echo "[$TIMESTAMP] mysql 重啟完成" >> $LOG

 COUNT=$(cat "$MYSQL\_RESTART\_FILE")
 COUNT=$((COUNT + 1))
 echo $COUNT > "$MYSQL\_RESTART\_FILE"

 if [ "$COUNT" -gt 3 ]; then
 echo "[$TIMESTAMP] mysql 重啟超過 3 次，模擬發送通知 email" >> $LOG
 fi
else
 echo "[$TIMESTAMP] mysql 運作正常" >> $LOG
 echo 0 > "$MYSQL\_RESTART\_FILE"
fi

# 系統資源狀態
echo "[$TIMESTAMP] 系統資源狀況：" >> $LOG
echo " 記憶體使用情況：" >> $LOG
free -h >> $LOG

echo "" >> $LOG
echo " Top 5 CPU 使用程序：" >> $LOG
ps aux --sort=-%cpu | head -n 6 >> $LOG

echo "" >> $LOG
echo "[$TIMESTAMP] 健康檢查結束" >> $LOG
echo "" >> $LOG

建議排程方式
可透過 /etc/crontab 加入以下行，每 10 分鐘執行一次：
*/10 * * * * milly /home/milly/health\_check.sh
（需確保 cron 服務啟動且腳本有執行權限）