# Day25：使用 systemd 管理 Flask API

在 Day23 中，我們已經完成了使用 `systemd` 建立 Flask API 為服務的實作。Day25 我們將補充實務部署中常見的管理技巧與改善建議。

---

## ✅ 已完成的 systemd 設定

```ini
[Unit]
Description=Flask Application
After=network.target

[Service]
User=milly
WorkingDirectory=/home/milly/myapp
ExecStart=/usr/bin/python3 app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## 🔧 建議補充項目

| 項目 | 說明 |
|------|------|
| `ExecStart` | 建議改為使用 venv 的 python 執行路徑 |
| `Restart=always` | 確保 Flask 當機會自動重啟 |
| `StandardOutput=journal` | 建議加上標準輸出與錯誤輸出記錄 |
| `Environment=` | 可加入 FLASK_ENV 等環境變數 |
| `enable` | 請確認服務已設為開機自啟動 |
| `journalctl` | 使用 `journalctl -u flask-app` 查看 log |

---

## 🧪 常用 systemd 操作指令

```bash
# 啟用服務
sudo systemctl start flask-app

# 設為開機自動啟動
sudo systemctl enable flask-app

# 檢查服務狀態
sudo systemctl status flask-app

# 停止服務
sudo systemctl stop flask-app

# 查看 log
sudo journalctl -u flask-app -n 50
```

---

## 🧰 觀察 Log 與排錯建議

```bash
# 即時查看 flask log
sudo journalctl -u flask-app -f

# 檢查是否有設定錯誤
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
```

---

## ✅ 結論

Day25 是實務部署的重要環節，讓 Flask API 在系統層級受控，搭配 log 與開機啟動設定，有助於打造穩定的交易或監控服務。
