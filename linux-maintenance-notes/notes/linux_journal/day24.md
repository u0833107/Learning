# Day 24：自動化部署（Bash 初體驗）

使用 Bash 腳本，一鍵部署一個簡易的 Flask API 應用，並設定 Nginx 作為反向代理。

---

## ✅ 任務目標

- 使用 Bash 腳本自動部署 Flask API + Nginx
- 設定防火牆開放 HTTP port
- 實際使用 curl 測試服務
- 解釋每行腳本的功能

---

## 📂 腳本內容 deploy_flask.sh

```bash
#!/bin/bash

echo "[1] 安裝必要套件..."
sudo apt update && sudo apt install -y python3-pip nginx ufw

echo "[2] 建立專案資料夾..."
mkdir -p /home/$USER/flask_project
cd /home/$USER/flask_project

echo "[3] 建立 Flask App"
cat << EOF > app.py
from flask import Flask
app = Flask(__name__)

@app.route("/api/order")
def order():
    return {"status": "success", "order_id": 12345}

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
EOF

echo "[4] 安裝 Flask..."
pip3 install flask

echo "[5] 啟動 Flask App"
nohup python3 app.py &

echo "[6] 設定 Nginx 反向代理..."
sudo tee /etc/nginx/sites-available/flaskapp <<EOF
server {
    listen 80;
    location /api/ {
        proxy_pass http://localhost:5000/;
    }
}
EOF

sudo ln -s /etc/nginx/sites-available/flaskapp /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl restart nginx

echo "[7] 設定防火牆..."
sudo ufw allow 80

echo "✅ 部署完成！你可以打開 http://localhost/api/order"
```

---

## 🔍 腳本解說

| 步驟 | 說明 |
|------|------|
| `[1]` | 安裝 Python3 套件、Nginx、UFW 防火牆 |
| `[2]` | 建立 Flask 專案目錄 |
| `[3]` | 自動生成 `app.py`，建立簡易 API |
| `[4]` | 用 `pip3` 安裝 Flask |
| `[5]` | 用 `nohup` 背景啟動 Flask，避免關閉 terminal 停止服務 |
| `[6]` | 建立 Nginx 設定檔並啟用反向代理 |
| `[7]` | 開啟防火牆 HTTP port（80）讓外部可連線 |

---

## ✅ 成果驗證

```bash
curl http://localhost/api/order
```

得到如下結果：
```json
{"status": "success", "order_id": 12345}
```

---

