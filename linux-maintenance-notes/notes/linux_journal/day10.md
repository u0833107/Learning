# Day10 備份自動化與排程管理

一份簡單的自動化排程備份腳本

```bash
#!/bin/bash

DB_NAME=”linux_practice”

BACKUP_DIR=”/home/milly/bakups”

TIMESTAMP=$(date +%F-%H%M)

BACKUP_FILE=”${BACKUP_DIR}/{DB_NAME}_backup_${TIMESTAMP}.sql”

LOG_FILE=”${BACKUP_DIR}/backup.log

mkdir -p “$BACKUP_DIR”

sudo mysqldump --databases “$DB_NAME” | sudo tee “$BACKUP_FILE

”

If [ $? -eq 0 ]; then

echo “[$(date)] database backup successful!”

else

echo “[$(date)] database backup failed”

fi

find “$BACKUP_DIR” -name “*.sql”-mtime +7 -exec rm{} \; >> “$LOG_FILE” 2>&1
```

`chmod -x /home/deployuser/mysql_backup.sh`   //給腳本執行權限

`crontab -e`

//加入以下指令，代表每天凌晨2:30執行這份腳本

30 2 * * * /home/deployuser/mysql_bakup_sh

`crontab -l`   //檢查排程是否加入

最後測試一下手動跑腳本是否正常

`sudo /home/deployuser/mysql_backup.sh`

補充：將.sql壓縮成.gz

在腳本內加入`gzip “$BACKUP_FILE”`即可

