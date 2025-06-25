# Day7

查閱系統日誌

`journalctl -n 50`：顯示最後50行的記錄

`journalctl | grep “error”`：篩選出帶有error的記錄

觀察系統負載狀況(監控指令)

|          指令         |                 功能                |             功能說明           |
|:---------------------:|:-----------------------------------:|:------------------------------:|
|           top         |     實時CPU/RAM資源使用             |          建立新的壓縮檔        |
|         uptime        |     查看系統執行時間與平均負載      |     使用gzip壓縮(產生.gz檔)    |
|          df -h        |     查看磁碟空間(人類可讀的格式)    |           顯示詳細過程         |
|         free -h       |     查看記憶體使用狀況              |           指定檔案名稱         |
|      du -sh /資料夾   |     檢查某個資料夾的空間用量        |              解壓縮            |
|             t         |     list                            |      顯示壓縮檔中有那些檔案    |

`top`

|       欄位     |               說明              |
|:--------------:|:-------------------------------:|
|       PID      |              程序ID             |
|       USER     |     該程序的擁有者(誰執行的)    |
|        PR      |         優先權(Priority)        |
|        NI      |       nice值(影響優先順序)      |
|       %CPU     |       該程序佔用的CPU百分比     |
|       %MEM     |         使用記憶體的比例        |
|      TIME+     |          累積CPU使用時間        |
|     COMMAND    |        程序名稱或執行指令       |

`uptime`

02:56:25 up 13 minutes, 1 user, load average:0.07,0.13,0.14

02:56:25：系統目前時間

up 13 minutes：系統已連續開機多久

1 user：有幾個使用者帳號目前登入系統

load average:0.07,0.13,0.14：系統平均負載，分別是過去1分鐘；過去5分鐘；過去15分鐘

`df -h`

df：disk filesystem

-h：human readable(把KB/MB/GB顯示成易讀的單位)

|        欄位     |                        意義                       |
|:---------------:|:-------------------------------------------------:|
|     FileSystem  |     哪一個磁碟裝置或分割區（例如   /dev/sda1）    |
|        Size     |     該分割區的總容量                              |
|        Used     |     已使用的空間                                  |
|        Avail    |     可使用的空間                                  |
|        Use%     |     使用百分比                                    |
|     Mounted on  |     該磁碟掛載在哪個目錄                          |

`free -h`

free：顯示記憶體空間與Swap狀況

|        欄位     |                    說明                  |
|:---------------:|:----------------------------------------:|
|        total    |     總記憶體(物理RAM或swap)空間          |
|        used     |     已使用的記憶體(程式佔用+系統暫存)    |
|         fee     |     完全沒被使用的記憶體(真的閒置)       |
|       shared    |     多個程式共享的記憶體(如tmpfs)        |
|     buff/cache  |     系統用來加速得快取或buffer空間       |
|      available  |     真正可供程式使用的空間               |

補充1：swap是虛擬記憶體，當RAM不夠用時，會使用硬碟空間暫存資料

補充2：建立一個2G的swap檔案

`sudo fallocate -l 2G /swapfile`   //先建立一個2G的swap檔案

`sudo chmod 600 /swapfile`   //設定權限只有root可讀寫

`sudo mkswap /swapfile`   //建立swap區塊

`sudo swapon /swapfile`  //啟用swap

`free -h`   //最後用free -h確定有建成功

打以下指令讓系統開機自動啟用swap

`echo ‘/swapfile none swap sw 0 0’ | sudo tee -a /etsc/fstab`

`du -sh`

du：顯示disk usage磁碟用量

-s：summary，只列出總計，不列出每個子資料夾

