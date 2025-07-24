# Day 05：用 Ansible 完成實務級部署 Flask API

## 📌 任務目標
使用 Ansible 自動部署 Flask API，並透過 systemd 管理服務與 Nginx 反向代理轉發，達成接近實務的部署環境。

---

## 🧱 環境設定步驟

### 1. 安裝 Python3 與 Flask
透過 Ansible 安裝 Python3 與 Flask 並建立 app.py。

```yaml
- name: 安裝 Python 與 Flask
  apt:
    name:
      - python3
      - python3-pip
    state: present
    update_cache: yes

- name: 安裝 Flask
  command: python3 -m pip install flask
```

---

### 2. 建立 Flask 應用程式

**app.py**
```python
from flask import Flask, jsonify
app = Flask(__name__)

@app.route("/api/order")
def order():
    return jsonify({"status": "success", "order_id": 12345})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5050)
```

---

### 3. 建立 systemd 服務檔

```ini
# /etc/systemd/system/flask-app.service
[Unit]
Description=Flask App
After=network.target

[Service]
WorkingDirectory=/opt/myflask
ExecStart=/usr/bin/python3 /opt/myflask/app.py
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reexec
sudo systemctl enable flask-app
sudo systemctl start flask-app
```

---

### 4. 安裝 Nginx 並設定反向代理

```nginx
# /etc/nginx/sites-enabled/default
location /api/ {
    proxy_pass http://localhost:5050/;
}
```

```bash
sudo systemctl reload nginx
```

---

## ✅ 測試結果

```bash
curl http://localhost/api/order
# 返回：{"status": "success", "order_id": 12345}
```

---

## 🐞 常見錯誤與排查

| 錯誤訊息 | 解法 |
|----------|------|
| `Welcome to nginx` | 忘記設定 `/api/` 路由轉發 |
| `Failed to lock apt` | 系統正在跑其他 apt 任務，可重啟機器或清除 lock 檔 |
| `No module named flask` | Flask 未安裝，或 Python 執行環境錯誤 |
| `port 5000 already in use` | 改用未佔用的 port（如 5050）|

---

## 📘 小結

此任務完整實作了：

- Ansible Playbook 自動部署
- 使用 `systemd` 啟動 Flask 應用
- 設定 Nginx 轉發實作 API
- 成功測試 `/api/order` 接口
