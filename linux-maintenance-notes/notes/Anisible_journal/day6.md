## Day 6：安排定期備份任務

任務目標
- 每天凌晨 2 點自動備份 MySQL 的 ansible_practice 資料庫
- 備份檔案自動儲存在 /home/milly/backups/（可改路徑）
- 備份檔名包含日期，EX：ansible_practice_2025-07-22.sql.gz

### 建立備份腳本 mysql_backup.sh

```bash
#!/bin/bash
DB_NAME="ansible_practice"
BACKUP_DIR="/home/milly/backups"
DATE=$(date +%F)
mysqldump -u root "$DB_NAME" | gzip > "$BACKUP_DIR/${DB_NAME}_$DATE.sql.gz"
```
### 建立 backup.yml Playbook
```yml
- name: Setup MySQL Backup Cron Job
hosts: all
become: true

tasks:
- name: Ensure backup directory exists
file:
path: /home/milly/backups
state: directory
mode: '0755'

- name: Copy backup script
copy:
src: files/mysql_backup.sh
dest: /usr/local/bin/mysql_backup.sh
mode: '0755'

- name: Add cron job for daily MySQL backup at 2 AM
ansible.builtin.cron:
name: "Daily MySQL Backup"
minute: "0"
hour: "2"
job: "/usr/local/bin/mysql_backup.sh"
user: milly
```

### 執行並驗證Playbook是否成功

`ansible-playbook -i inventory/hosts backup.yml -K`

### 驗證

```bash
crontab -l -u milly  #確定是否加入排程
/usr/local/bin/mysql_backup.sh #手動執行
ls /home/milly/backups/ #確定有產生檔案
```
