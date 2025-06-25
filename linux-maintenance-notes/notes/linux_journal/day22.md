# Day22 系統維護流程與硬體診斷工具

使用 dmesg 檢查核心錯誤

`dmesg -T | grep -iE “error|fail|usb|disk”`

-i：不區分大小寫

-E：啟用「延伸正規表達式」功能

使用 smartctl 測試硬碟健康

`sudo apt install smartmontools`

`sudo smartctl -a /dev/sda`

重點欄位：

Reallocated_Sector_Ct：不應過高

Power_On_Hours：大於 20000 表示老化

SMART overall-health test result：PASSED 才正常

撰寫硬體健康狀態腳本

```bash
#!/bin/bash

TIMESTAMP=$(date +”%F %T”)

LOG=”/home/milly/hardware_health.log”

echo “[$TIMESTAMP]===硬體健康狀態檢查開始===”>>$LOG

#磁碟使用狀況

echo “===磁碟使用狀態(df -h)===”>> $LOG

df -h >> $LOG

echo “”>> $LOG

#記憶體狀態(free -h)

echo “===記憶體狀態(free -h)===” >> $LOG

free -h >> $LOG

echo “” >> $LOG

#開機時長與平均負載

echo “===開機時長與平均負載(uptime)===” >> $LOG

uptime >> $LOG

echo “” >> $LOG

#感應器資訊

echo “===感應器資訊(sensor)===” >> $LOG

sensor >> $LOG 2>/dev/null || echo “(感應器無法讀取或未安裝 lm-sensors)”>> $LOG

echo “” >> $LOG

echo “===磁碟檢查(smartctl)===” >> $LOG

sudo smartctl -a T permissive >> $LOG 2>/dev/sda || echo “(磁碟不支援 SMART 或無法存取)”>> $LOG

echo “”>> $LOG

echo “[$TIMESTAMP]===磁碟檢查結束===”>> $LOG

echo “” >> $LOG
```
