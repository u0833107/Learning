## Day 3 使用 Ansible 安裝並設定 MySQL

### 任務目標：

- 使用 ansible.builtin.apt 安裝 mysql-server
- 使用 become: true 提升權限執行

### 撰寫 Playbook 自動：

- 安裝 MySQL
- 設定 root 密碼
- 建立資料庫 linux_practice
- 建立 Playbook mysql_setup.yml

建立檔案
`nano mysql_setup.yml`

檔案內容如下(最基本版本)
```yml
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
```

※ mysql_db 模組需要目標機器有安裝 Python 的 PyMySQL 套件。

### 安裝 PyMySQL（遠端）

建立install_pymysql.yml
`nano install_pymysql.yml`

檔案內容如下：
```yml
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
```

### 執行install_pymysql.yml

`ansible-playbook -i inventory/hosts mysql_setup.yml -K`

### 執行 Playbook 自動建立資料庫

```bash
#執行mysql_setup.yml

`ansible-playbook -i inventory/hosts mysql_setup.yml -K`

#確認是否成功建立資料庫

`sudo mysql -e “SHOW DATABASES;”`
```