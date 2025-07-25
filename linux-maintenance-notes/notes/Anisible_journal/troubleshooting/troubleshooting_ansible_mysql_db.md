# Troubleshooting：Ansible 建立 MySQL 資料庫失敗

## 問題描述
在執行 Ansible playbook `mysql_setup.yml` 建立 MySQL 資料庫時，發生以下錯誤：

```
unable to find /root/.my.cnf. Exception message: (1698, "Access denied for user 'root'@'localhost'")
```

## 原因分析
- 預設情況下，Ansible 使用 `root` 身份執行 `mysql_db` 模組，但沒有提供 MySQL root 密碼認證。
- 因此需要透過 `/root/.my.cnf` 檔案來提供密碼給 MySQL 指令使用。

## 處理步驟

### Step 1：手動設定 root 密碼（改用密碼登入）

```bash
sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'mypass123';
FLUSH PRIVILEGES;
```

### Step 2：建立 `/root/.my.cnf` 認證檔案

```bash
sudo nano /root/.my.cnf
```

加入以下內容：

```
[client]
user=root
password=mypass123
```

設定檔案權限：

```bash
sudo chmod 600 /root/.my.cnf
```

### Step 3：重新執行 Playbook

```bash
ansible-playbook -i inventory/hosts mysql_setup.yml -K
```

## 處理結果

資料庫 `ansible_practice` 成功建立

---

> 📌 備註：若未建立 `/root/.my.cnf` 或密碼設定錯誤，將無法順利操作 MySQL。
