# Day4 Linux檔案搜尋與日誌分析

找出檔案或資料夾

`find . -name hello.txt`：找出目前目錄下所有叫hello.txt的檔案

`sudo find / -name nginx.conf`：在整個系統下尋找nginx.conf

`find . -type f -mtime -3`：找出三天內修改過的檔案

關鍵字過濾

`sudo less /var/log/nginx/error.log`

`grep “404”/var/log/nginx/access.log`：在access.log中找出所有包含404的行

`grep “nginx”/etc/passwd`：搜尋passwd中有nginx的行

`grep -n “nginx” /var/log/nginx/access.log`：找出包含nginx的檔案與行號

常用的選項：

-n：顯示行號

-i：忽略大小寫

-r：遞迴搜尋(整個資料夾)

-v：排除(不包含關鍵字)

系統事件日誌

`journalctl`：查看全部日誌

`journalctl -u nginx`：查看nginx相關日誌（前提是 nginx 是 systemd 管控的服務）

`journalctl -b`：查看最近的開機日誌

`journalctl -p err`：只看錯誤

`journalctl --since “10 minutes ago”`：查看特定時間範圍的日誌

