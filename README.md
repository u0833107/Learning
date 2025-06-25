# Linux 維運實作作品集

這是我在三週系統維運學習過程中所整理的筆記與實作報告，涵蓋 Linux / Windows / MySQL 的常見問題排查、系統報告撰寫與腳本自動化任務，適合作為維運工程師職務的實作參考。

---

## 📁 目錄結構

```
linux-maintenance-notes/
├── notes/ # 學習筆記與工具指令整理
│ ├── note_xxx.md
│ └── linux_journal/ # Linux 每日學習實作紀錄（Day 1～22）
├── reports/ # 系統事件模擬報告（含圖文說明）
│ ├── images/ # 報告內使用的截圖資源
│ └── report_xxx.md
├── troubleshooting/ # 額外補充與問題排查筆記
└── README.md # 專案介紹與資料索引
```

---

## 📝 Reports - 系統維運事件報告

| 檔案 | 說明 |
|------|------|
| `系統維運事件報告_nginx無法啟動.md` | 模擬 nginx 配置錯誤導致服務失敗，排查與修復流程 |
| `系統維運事件報告_Cron任務未執行.md` | crontab 沒有正確執行排程的完整排查與處理紀錄 |
| `系統維運事件報告_MySQL無法連線.md` | MySQL 遠端連線問題處理流程與授權修復紀錄 |

---

## 🛠 Troubleshooting - 問題排查記錄

| 檔案 | 說明 |
|------|------|
| `MySQL_Socket_異常排查筆記.md` | 連線錯誤時出現 socket 找不到問題的排查流程 |
| `MySQL無法啟動與AppArmor權限修復流程筆記.md` | 虛擬機環境中 AppArmor 阻擋導致無法啟動 MySQL |
| `nginx_網路問題排查筆記.md` | 使用 ss / curl / ufw 解決無法連接 nginx 的問題 |

---

## 📚 Notes - 技術筆記與整理

| 檔案 | 說明 |
|------|------|
| `MySQL權限範圍對照表筆記.md` | 對 MySQL 權限（READ / WRITE / ALL）分別整理與對照說明 |
| `crontab除錯總整理.md` | 如何排查排程不執行？包含權限、格式與 sudo 問題 |
| `修改Port_Forwarding設定筆記.md` | VirtualBox 如何設定 port 轉發供外部連入 VM |

---

## 💡 說明

所有紀錄與報告皆為學習期間之模擬實作內容，為求真實完整，保留排查過程與錯誤紀錄，作為日後維運經驗回顧與面試作品呈現依據。
