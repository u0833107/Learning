# ⚙️ PowerShell 任務自動化（Day02）

## 一個簡單的自動備份腳本

```powershell
$timestamp = Get-Date -Format "yyyy-MM-dd-HHmm"
$logDir = "C:\Backup\logs"

# 若資料夾不存在則建立
If (!(Test-Path $logDir)) {
    New-Item -Path $logDir -ItemType Directory
}

Get-ChildItem -Path "C:\Users" -Recurse | Out-File "$logDir\backup_$timestamp.txt"
```

---

## 建立排程任務（Task Scheduler）

1. 開啟「工作排程器」
2. 選擇「建立基本工作」
3. 命名：EX：每日備份
4. 設定時間與觸發條件
5. 動作 → 啟動程式  
6. 程式路徑：`powershell.exe`  
7. 引數：  
```bash
-ExecutionPolicy Bypass -File "C:\Backups\daily_backup.ps1"
```

---

## 執行權限設定

```powershell
Set-ExecutionPolicy RemoteSigned
```

可先查目前設定：
```powershell
Get-ExecutionPolicy
```

---

## 補充：完整紀錄 log 的腳本

```powershell
$timestamp = Get-Date -Format "yyyy-MM-dd-HHmm"
$logDir = "C:\Backups\logs"
$logFile = "$logDir\log_$timestamp.txt"
$backupFile = "$logDir\backup_$timestamp.txt"

Start-Transcript -Path $logFile

if (!(Test-Path $logDir)) {
    New-Item -Path $logDir -ItemType Directory -Force
}

Write-Output "[$(Get-Date -Format 'HH:mm:ss')] Backup Start!"

try {
    Get-ChildItem -Path "C:\Users" -Recurse | Out-File -FilePath $backupFile -ErrorAction Stop
    Write-Output "[$(Get-Date -Format 'HH:mm:ss')] ✅ Backup Successful!"
}
catch {
    Write-Output "[$(Get-Date -Format 'HH:mm:ss')] ❌ Backup Failed: $($_.Exception.Message)"
}

Write-Output "[$(Get-Date -Format 'HH:mm:ss')] Backup Completed"
Stop-Transcript
```
