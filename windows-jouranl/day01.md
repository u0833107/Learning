# 🪟 Windows 系統基礎維運（Day01）

## 使用事件檢視器查看系統錯誤

### 開啟事件檢視器：
```bash
Eventvwr.msc
```

### 錯誤分類：
- 系統：EX 服務啟動錯誤、磁碟異常  
- 應用程式：EX 應用程式當機、錯誤模組

### 紀錄重點：
1. 時間
2. 來源（如：Service Control Manager）
3. 事件 ID
4. 錯誤訊息摘要

> 🔍 參考：`C:\Users\milly\OneDrive\桌面\錯誤事件記錄範例.txt`

---

## 使用 PowerShell 查看服務狀態

```powershell
Get-Service              # 查看所有服務狀態

# 停止 spooler 服務
Stop-Service -Name spooler

# 啟動 spooler 服務
Start-Service -Name spooler
```

---

## 確認磁碟使用情況與格式

```bash
diskmgmt.msc
```

### 檢查項目：
1. 總硬碟容量  
2. 分割區格式（NTFS、FAT32）  
3. 是否有未配置空間  

---

## 建立 PowerShell 腳本（遞迴列出資料夾）

```powershell
Get-ChildItem -Path "C:\Users" -Recurse | Out-File "C:\Users\milly\OneDrive\桌面\user_list.txt"
```

---

## 讀取最近 10 筆事件紀錄

```powershell
Get-EventLog -LogName System -Newest 10
```

---

## 補充指令

- `Get-Help` 查指令說明  
- `sc qc 服務名稱` 檢查服務詳細資訊
