Day 2：撰寫第一支 Playbook，自動安裝 nginx
任務目標
使用 Ansible 撰寫一支 Playbook，完成以下動作：
安裝 nginx
啟動 nginx
確認 nginx 有啟用（使用 systemctl）
建立 playbook 檔案
cd ~/ansible_project   #進入專案資料夾
nano nginx_setup.yaml   #新增檔案 nginx_setup.yaml
檔案內容如下：
- name: Install and start nginx
hosts: local
become: true
tasks:
- name: Ensure nginx is installed
apt:
name: nginx
state: present
update_cache: yes

- name: Ensure nginx is running
service:
name: nginx
state: started
enabled: true
說明：
become: true：讓 Ansible 以 sudo 執行
apt:：使用 apt 模組安裝 nginx
service:：啟動 nginx 並設為開機啟動
執行 Playbook
ansible-playbook -i inventory/hosts nginx_setup.yaml
輸出結果如下：
TASK [Ensure nginx is installed] 變更為 OK or changed
TASK [Ensure nginx is running] 啟動成功
驗證 nginx 是否成功啟動
sudo systemctl status nginx
Day 3 使用 Ansible 安裝並設定 MySQL
任務目標：
使用 ansible.builtin.apt 安裝 mysql-server
使用 become: true 提升權限執行
撰寫 Playbook 自動：
安裝 MySQL
設定 root 密碼
建立資料庫 linux_practice
建立 Playbook mysql_setup.yml
建立檔案
nano mysql_setup.yml
檔案內容如下(最基本版本)：
- name: Install and configure MySQL
hosts: all
become: true

vars:
mysql_root_password: "mypass123"

tasks:
- name: Install MySQL server
apt:
name: mysql-server
state: present
update_cache: true

- name: Set MySQL root password (Ubuntu 預設用 socket 登入，不設定密碼)
debug:
msg: "若你使用 mysql_secure_installation 請手動設定 root 密碼"

- name: Create database linux_practice
mysql_db:
name: linux_practice
state: present
become: true
vars:
ansible_python_interpreter: /usr/bin/python3
mysql_db 模組需要目標機器有安裝 Python 的 PyMySQL 套件。
安裝 PyMySQL（遠端）
建立install_pymysql.yml
nano install_pymysql.yml
檔案內容如下：
- name: Install PyMySQL via apt
hosts: all
become: true

tasks:
- name: Update apt cache
apt:
update_cache: yes

- name: Install PyMySQL
apt:
name: python3-pymysql
state: present
#執行install_pymysql.yml
ansible-playbook -i inventory/hosts mysql_setup.yml -K
執行 Playbook 自動建立資料庫
#執行mysql_setup.yml
ansible-playbook -i inventory/hosts mysql_setup.yml -K
#確認是否成功建立資料庫
sudo mysql -e “SHOW DATABASES;”
Day 4部署 Flask API（自動佈署）
任務目標
安裝 Python3、pip、flask
將 app.py 複製到目標目錄
建立 flask-app.service 的 systemd 檔案
啟動 Flask 並設為開機自動執行
準備 app.py 程式碼
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/api/order")
def order():
return jsonify({"status": "success", "order_id": 12345})

if __name__ == "__main__":
app.run(host="0.0.0.0", port=5000)
撰寫 flask.yml Playbook
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
利用下列指令執行
ansible-playbook -i /inventory/hosts flask.yml -K
驗證部署結果
sudo systemctl status flask-app
顯示active代表成功
