# Day20

檢查與強化帳號設定

`sudo mysql`

`SELECT USER, Host,plugin FROM mysql.user;`   #檢查所有帳號

建議設定：

1. 沒有空密碼帳號

2. 沒有 '%' 通用授權（除非真的有防火牆管控）

3. plugin 最好是 mysql_native_password（不是 auth_socket）

若需要修改用下列指令：

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密碼';`

啟用遠端連線

`sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf`

找到bind-address = 127.0.0.1改成0.0.0.0

#儲存後重啟

`sudo systemctl restart mysql`

新增允許遠端登入的帳號（限內網 IP）

`CREATE USER ‘remotedb’@’10.0.2.%’ IDENTIFIED BY ‘remote123’;`(假設內網是10.0.2.%)

`GRANT SELECT ON linux_practice.* TO ‘remotedb’@’10.0.2.%’;`

加強 UFW 防火牆控制

#開啟內網才能存取MySQL

`sudo ufw allow from 10.0.2.0/24 to any port 3306`

#拒絕其他來源連入MySQL

```bash
sudo ufw deny 3306

sudo ufw reload
```

可用以下指令查詢目前規則：

`sudo ufw status numbered`

在本機進行測試

打開powerdshell輸入下列指令：

`mysql -u remotedb -h 10.0.2.15 -p`

成功會看到Welcome to the MySQL monitor…..

若出現Can't connect to MySQL server on '10.0.2.15' (10060)看troubleshoot_mysql_3306_bind

若出現Access denied for user 'remotedb'@'10.0.2.%' to database '10.0.2.15'看troubleshoot_mysql_remote_10060

