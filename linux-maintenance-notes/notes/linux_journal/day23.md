# 📦 Day 23：模擬交易系統部署與維運挑戰

## 🎯 今日目標
- 建立一個模擬交易系統（API 服務）
- 配置 nginx 作為反向代理
- 撰寫監控腳本與故障自動修復
- 撰寫系統維運報告

---

## ✅ 任務一：建立簡易 Flask 模擬交易 API

```bash
sudo apt update
sudo apt install python3-pip -y
pip install flask
```

建立專案資料夾並撰寫 `app.py`：

```python
from flask import Flask, jsonify
app = Flask(__name__)

@app.route("/api/order")
def order():
    return jsonify({"status": "success", "order_id": 12345})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

執行 API：

```bash
python3 app.py
```

驗證 API：
```bash
curl http://localhost:5000/api/order
```

---

## ✅ 任務二：nginx 反向代理設定

編輯 `/etc/nginx/sites-enabled/default`：

```nginx
location /api/ {
    proxy_pass http://localhost:5000/;
}
```

重啟 nginx：

```bash
sudo systemctl restart nginx
```

驗證是否能透過 `http://localhost/api/order` 訪問

---

## ✅ 任務三：撰寫監控腳本 `check_trade_api.sh`

```bash
#!/bin/bash

URL="http://localhost/api/order"
LOG="/home/milly/falsk_project/api_health.log"
TIME=$(date "+%F %T")

if curl -s $URL | grep -q "success"; then
    echo "[$TIME] API is normal operation" >> $LOG
else
    echo "[$TIME] API is error, please check nginx or flask" >> $LOG
    sudo systemctl restart nginx
    sudo systemctl restart flask-app
fi

echo "" >> $LOG
```

設為可執行：
```bash
chmod +x check_trade_api.sh
```

加入 `/etc/crontab` 排程：

```
*/5 * * * * root /home/milly/falsk_project/check_trade_api.sh
```

---

## ✅ 任務四：模擬 API 異常並觀察紀錄

1. 手動停止 flask 程式（`Ctrl+C`）
2. 執行：`curl http://localhost/api/order`
3. 觀察：
   - `/var/log/nginx/error.log`
   - `api_health.log`

---

## ✅ 任務五：撰寫系統維運報告

- 問題描述：API 無法連線，返回 502
- 原因分析：Flask 應用未啟動
- 處理步驟：手動或腳本重啟 flask
- 改善建議：監控頻率調整、自動修復、log 留存

📎 可參考報告：[api_issue_report.md](./api_issue_report.md)
