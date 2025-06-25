# Day5 使用者管理與SSH遠端登入基礎

使用者管理

`groups testuser`：檢查使用者是否屬於特定群組

`sudo usermod -aG sudo testuser`：把使用者加入sudo群組(給管理權限)

`cat -d: -f1 /etc/passwd`：查詢目前系統有哪些帳號

SSH 遠端登入

`ssh your-name@localhost`：自己連自己

`ssh username@server-ip`：使用者在其他電腦上要登入Linux主機

