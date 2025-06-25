# Day2

各數字代表的權限

|       數字    |     符號  |      權限   |
|:-------------:|:---------:|:-----------:|
|         0     |      ---  |     無權限  |
|         1     |       x   |      執行   |
|         2     |       w   |       寫    |
|         4     |       r   |       讀    |

EX：6 => 可讀可寫

建立使用者

`sudo adduser Bob`：新增一個叫bob的使用者

`sudo passwd Bob`：為Bob這個使用者設定密碼

`su - Bob`：切換使用者Bob

whoami：檢查使用者身分

修改權限

`chmod a+x hello.txt`：給(+)所有人(a)hello.txt這個檔案的執行權限(x)

`chmod 600 hello.txt`：讓使用者有讀寫(讀:4+寫:2)的權限

改變擁有者

`sudo chown Bob:Bob owner.txt`：將owner.txt的擁有者改成Bob

