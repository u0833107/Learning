# Linux 維運實作作品集

這是我在三周系統維運學習過程中所整理的筆記與實作報告，涵蓋 Linux / Windows / MySQL 的常見問題排查、系統報告撰寫與腳本自動化任務，適合作為維運工程師職務的實作參考。

---

## 📁 目錄結構

```
linux-maintenance-notes/
├── notes/ # 學習筆記與工具指令整理
│ ├── note_crontab_debug_summary.md
│ ├── note_healthcheck_script.md
│ ├── note_mysql_grant_reference.md
│ ├── note_port_forwarding.md
│ └── linux_journal/ # Linux 每日學習實作紀錄（Day 01～Day 22）
│ ├── day01.md
│ ├── day02.md
│ └── ...
├── reports/ # 系統事件模擬報告（含圖文說明）
│ ├── images/ # 報告內使用的截圖資源
│ ├── report_cron_task_missed.md
│ ├── report_mysql_connection_failed.md
│ ├── report_nginx_failed_to_start.md
│ └── report_nginx_unstable_issue.md
├── troubleshooting/ # 額外補充與問題排查筆記
├── windows-journal/ # Windows 每日學習實作紀錄
│ ├── README.md
│ ├── day01.md
│ └── day02.md
└── README.md # 主專案介紹與資料導覽
```

---

## 📝 Reports - 系統維運事件報告

| 主題 | 說明 |
|------|------|
| [`系統維運事件報告_nginx無法啟動`](linux-maintenance-notes/reports/report_nginx_failed_to_start.md) | 模擬 nginx 配置錯誤導致服務失敗，排查與修復流程 |
| [`系統維運事件報告_nginx異常`](linux-maintenance-notes/reports/report_nginx_unstable_issue.md) | 模擬 nginx 網站無法連線問題處理流程與修復紀錄 |
| [`系統維運事件報告_Cron任務未執行`](linux-maintenance-notes/reports/report_cron_missed.md) | crontab 沒有正確執行排程的完整排查與處理紀錄 |
| [`系統維運事件報告_MySQL無法連線`](linux-maintenance-notes/reports/report_mysql_connection_failed.md) | MySQL 遠端連線問題處理流程與授權修復紀錄 |

---

## 🛠 Troubleshooting - 問題排查記錄

| 主題 | 說明 |
|------|------|
| [`MySQL_Socket_異常排查筆記`](linux-maintenance-notes/troubeshooting/troubleshoot_mysql_socket_error.md) | 連線錯誤時出現 socket 找不到問題的排查流程 |
| [`MySQL無法啟動與AppArmor權限修復流程筆記`](linux-maintenance-notes/troubleshooting/troubleshoot_mysql_apparmor.md) | 虛擬機環境中 AppArmor 阻擋導致無法啟動 MySQL |
| [`MySQL 3306 未監聽問題`](linux-maintenance-notes/troubleshooting/troubleshoot_mysql_3306_bind.md) | MySQL 3306 未監聽問題排查修正 |
| [`MySQL_遠端連線_10060_錯誤排查筆記`](linux-maintenance-notes/troubleshooting/troubleshoot_mysql_remote_10060.md) | MySQL 遠端連線錯誤排查 |
| [`nginx_網路問題排查筆記`](linux-maintenance-notes/troubleshooting/troubleshoot_nginx_network.md) | 使用 ss / curl / ufw 解決無法連接 nginx 的問題 |
| [`Log重複誤判排查筆記`](linux-maintenance-notes/troubleshooting/troubleshoot_log_duplicate.md) | Log資料重複誤判排查

---

## 📚 Notes - 技術筆記與整理

| 主題 | 說明 |
|------|------|
| [`MySQL權限範圍對照表筆記`](linux-maintenance-notes/notes/note_mysql_grant_reference.md) | 對 MySQL 權限（READ / WRITE / ALL）分別整理與對照說明 |
| [`crontab除錯總整理`](linux-maintenance-notes/notes/note_crontab_debug_summary.md) | 如何排查排程不執行？包含權限、格式與 sudo 問題 |
| [`修改Port_Forwarding設定筆記`](notes/note_port_forwarding.md) | VirtualBox 如何設定 port 轉發供外部連入 VM |
| [`綜合維運實戰`](linux-maintenance-notes/notes/note_healthcheck_script.md) | 健康檢查與自動修復腳本 |

---

## 📘 每日學習實作記錄 - Learning Journals

| 系統類別 | 說明 |
|--------|------|
| [`Linux Journal`](linux-maintenance-notes/notes/linux_journal/README.md) | Day01～Day22 系統維運主題循序實作（包含備份、排程、資安、資源監控等） |
| [`Windows Journal`](linux-maintenance-notes/windows-journal/README.md) | Windows 排程、PowerShell 自動化與事件記錄分析 |


## 💡 說明

所有紀錄與報告皆為學習期間之模擬實作內容，為求真實完整，保留排查過程與錯誤紀錄，作為日後維運經驗回顧與面試作品呈現依據。
