# Day6

建立壓縮備份與解壓縮

`tar -czvf /home/deployuser/app_bakup.tar.gz` /home/deployuser/app：

app_bakup.tar.gz：備份檔的名字

/home/deployuser/app：要打包的路徑

`makdir /home/deployuser/app_restore`：先創一個app_restore

`tar -xzvf /home/deployuser/app_bakup.tar.gz -C /home/deployuser/app_restore`：這樣壓縮檔就會還原在app_restore裡

`tar -tzvf /home/deployuser/app_backup.tar.gz`：列出壓縮檔內容不解壓縮

補充：-C代表切換目錄，代表在解壓縮前先進入指定的資料夾再把內容解開

|     參數 |       意義   |             功能說明           |
|:--------:|:------------:|:------------------------------:|
|      c   |      create  |          建立新的壓縮檔        |
|      z   |       gzip   |     使用gzip壓縮(產生.gz檔)    |
|      v   |     verbose  |           顯示詳細過程         |
|      f   |       file   |           指定檔案名稱         |
|      x   |     extract  |              解壓縮            |
|      t   |       list   |      顯示壓縮檔中有那些檔案    |

`tar -czvf /home/deployuser/app_bakup$(date +%F-%H$M).tar.gz /home/deployuser/app`

$(date +%F-%H$M)：shell指令替換，會執行date指令，並把結果插進檔名

補充：date +%F-%H$M是格式化檔名，%F->YYYY-MM-DD(EX：2025-05-21)；%H%M->小時分鐘(EX：1530會變成15：30)

資料同步

基本用法：`rsync -av /來源/目錄/ /目標/目錄/`

|     參數  |                     說明                   |             功能說明           |
|:---------:|:------------------------------------------:|:------------------------------:|
|      -a   |     archive模式，會保留檔案權限、時間等    |          建立新的壓縮檔        |
|      -v   |     verbose，會顯示詳細過程                |     使用gzip壓縮(產生.gz檔)    |
|      -z   |     傳輸時壓縮資料(通常遠端才會用)         |           顯示詳細過程         |
|      -r   |     遞迴複製(可選，-a內已包含)             |           指定檔案名稱         |
|       x   |     extract                                |              解壓縮            |
|       t   |     list                                   |      顯示壓縮檔中有那些檔案    |

EX：

`mkdir -p ~/original_data`  //創來源資料夾，及file1和file2兩個檔案做測試

`echo “Test1” > ~original_data/file1.txt`

`echo “Test2” > ~original_data/file2.txt`

`mkdir -p ~/bakup_data`   //創目標資料夾

`reync -av ~/original_data/ ~/bakup_data/`    //同步

`ls -l ~/bakup_data`   //確認同步結果

EX2：

//更新來源資料夾內其中一個檔案的內容

`echo “Update Content” > ~/original_data/file1.txt`

`reync -av ~/original_data/ > ~/bakup_data/`   //同步

`cat ~/bakup_data/file1.txt`    //查看同步後檔案內容是否更新

補充1：來源資料夾後的斜線很關鍵

有 / 表示「同步裡面的內容」

沒 / 表示「連同這個資料夾本身一起同步」

補充2：將本機資料夾同步到遠端主機上

`rsync -av ~/original_data/ username@remote_host:/home/username/bakup_data`

username@remote_host：遠端主機的登入使用者與位址

home/username/bakup_data：遠端主機上目的地的資料夾位址

！以上指令會要求使用者輸入username@remote_host 的SSH密碼！

自動化排程

基本用法：* * * * * 要執行的指令

第一個*代表分鐘(0~59)

第二個*代表小時(0~23)

第三個*代表日期(1~31)

第四個*代表月份(1~12)

第五個*代表星期幾(0~7，0和7代表星期日)

EX：

`nano /home/deployuser/bakup.sh`  //先建立備份腳本

輸入以下內容

#!/bin/bash

tar -czvf /home/deployuser/app_bakup_$(date +%F-%H%M).tar.gz /home/deployuser/app

儲存退出(Ctri+O -> Enter ->Ctrl+X)

記得給執行權限

`chmod +x /home/deployuser/bakup.sh`

`crontab -e`   //編輯crontab

！如果是第一次會問要選哪種編輯器，建議選nano(簡單好懂)！

*/10 * * * * /home/deployuser/bakup.sh  //代表每10分鐘備份一次

`crontab -l`   //查看目前的排程清單

