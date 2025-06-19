Windows學習日記
Day1 Windows系統基礎維運
使用事件檢視器查看系統錯誤
//開啟事件檢視器
Eventvwr.msc
系統：EX服務啟動錯誤、磁碟異常
應用程式：EX應用程式當機、錯誤模組
找出「錯誤」級別的記錄，並記錄以下：
時間
來源（如：Service Control Manager）
事件ID
錯誤訊息摘要
參考C:\Users\milly\OneDrive\桌面\錯誤事件記錄範例.txt
使用Powershell查看服務狀態
Get-Service //查看所有服務狀態

以spooler服務為例：
Stop-Service -Name spooler //停止spooler服務
Start-Service -Name spooler //啟動spooler服務
確認磁碟使用情況與格式
diskmgmt.msc //開啟磁碟管理
查看以下：
總硬碟容量
各分割區格式（NTFS？FAT32？）
是否有分割未配置空間
建立PowerShell 腳本
//輸出C:/Users下所有檔案名稱至桌面文字檔
Get-ChildItem -Path “C:/Users”-Recurse | Out-File “C:/Users/milly/OneDrive/桌面/user\_list.txt”
Get-ChildItem：取得資料夾清單
Out\_File：把資料寫到一個純文字檔中
-Recurse：表示要「遞迴」列出子資料夾裡的所有內容（不只看表層）
列出EventLog中System事件最近的10筆Log
Get-EventLog -LogName System -Newest 10
Get-EventLog：讀取Windows事件記錄
-LogName System：指令要查看的Log類型為System(其他還有Security、Application等)
-Newest 10：只列出最近的10筆記錄
補充：
事件ID可透過網路查詢微軟對應說明，有時可快速釐清設備或驅動程式錯誤來源。
Get-Help可以查詢指令說明。
不熟悉的服務名稱可用 sc qc 服務名稱 查詳細資訊。
Day2 PowerShell任務自動化
一個簡單的自動備份腳本
撰寫腳本用code backup.ps1(推薦，要先裝vs code)
$timestamp = Get-Date -Formate “yyyy-MM-dd-HHmm”
$logDir = “C:\Backup\logs
#若資料夾不存在則建立
If(!(Test-Path $logDir)){
New-Item -Path $logDir -Itemtype Directory
}
Get-ChildItem -Path “C:\Users” -Recurse | Out-File “$logDir\backup\_$timestamp.txt”
建立排程任務
1.打開工作排程器
2.建立基本工作
名稱：EX:每日備份工作
執行時間：EX:每天某個時間
動作：EX:啟動程式
程式路徑：powershell.exe(可在powerdhell用Get-Command powershell查路徑)
引數：-ExecutionPolicy Bypass -File “C:\Backups\daily\_backup.ps1”
注意：如果沒有允許執行腳本，需在PowerShell以系統管理員身份執行下列指令：
Set-ExecutionPolicy RemoteSigned
！可以先用Get-ExecutionPolicy看目前的執行政策，結果分為以下幾種

補充腳本(自動紀錄所有終端機輸出與錯誤訊息)
$timestamp = Get-Date -Format "yyyy-MM-dd-HHmm"
$logDir = "C:\Backups\logs"
$logFile = "$logDir\$log\_timestamp.txt"
$backupFile = "$logDir\backup\_$timestamp.txt"
Start-Transcript -Path $logFile
#建立資料夾(如果不存在)
if(!(Test-Path $logDir)){
New-Item -Path $logDir -ItemType Directory -Force
}
Write-Output "[$(Get-Date -Format 'HH:mm:ss')] Backup Start!"
try{
#執行備份
Get-ChildItem -Path "C:\Users" -Recurse | Out-File -FilePath $backupFile -ErrorAction Stop
Write-Output "[$(Get-Date -Format 'HH:mm:ss')] Backup Succcessful!"
}
catch{
Write-Output "[$(Get-Date -Format 'HH:mm:ss')] Backup Failed:$($\_.Exception.Message)"
}
Write-Output "[$(Get-Date -Format 'HH:mm:ss')] Backup Completed"
Stop-Transcript

