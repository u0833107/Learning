## Day 4部署 Flask API（自動佈署）

### 任務目標
- 安裝 Python3、pip、flask
- 將 app.py 複製到目標目錄
- 建立 flask-app.service 的 systemd 檔案
- 啟動 Flask 並設為開機自動執行

### 建立 app.py 程式碼
```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/api/order")
def order():
return jsonify({"status": "success", "order_id": 12345})

if __name__ == "__main__":
app.run(host="0.0.0.0", port=5000)
```

### 撰寫 flask.yml Playbook

```yml
- name: Deploy Flask APP
hosts: all
become: true

vars:
flask_app_dir: /opt/myflask
flask_service: flask-app

tasks:
- name: Install required apt packages
apt:
name:
- python3
- python3-venv
- python3-flask
- curl
state: present
update_cache: yes

- name: Create Flask APP Directory
file:
path: "{{ flask_app_dir }}"
state: directory
mode: '0755'

- name: Copy app.py
copy:
src: ./app.py
dest: "{{ flask_app_dir }}/app.py"
mode: '0755'

- name: Create systemd service file for flask
copy:
dest: "/etc/systemd/system/{{ flask_service }}.service"
content: |
[Unit]
Description=Flask App
After=network.target

[Service]
WorkingDirectory={{ flask_app_dir }}
ExecStart=/usr/bin/python3 app.py
Restart=always
User=root

[Install]
WantedBy=multi-user.target

- name: Reload systemd
systemd:
daemon_reload: true

- name: Start and enable Flask app service
systemd:
name: "{{ flask_service }}"
state: started
enabled: true
```

利用下列指令執行

`ansible-playbook -i /inventory/hosts flask.yml -K`

### 驗證部署結果

`sudo systemctl status flask-app`

顯示active代表成功