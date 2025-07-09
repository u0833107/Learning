# 🧾 系統維運報告 — API 無法連線問題處理紀錄

## 🕒 發生時間  
2025/07/04 16:45:01（UTC+8）

---

## 📌 問題描述  
交易系統的 API（`http://localhost/api/order`）無法連線，使用 `curl` 測試時回傳 `502 Bad Gateway` 錯誤。

---

## 🔍 排查過程  

1. 查看 nginx 錯誤日誌：
   ```bash
   sudo less /var/log/nginx/error.log
   ```
   發現錯誤訊息：
   ```
   connect() failed (111: Connection refused) while connecting to upstream
   ```

2. 檢查 Flask 服務狀態：
   ```bash
   sudo systemctl status flask-app
   ```
   顯示為 `inactive`，Flask 未啟動。

---

## ⚠️ 問題原因  
Flask 應用未啟動，導致 nginx 嘗試將請求轉發至 5000 port 失敗，因此回傳 502 錯誤。

---

## 🛠️ 處理方法  

1. 重啟 Flask 服務：
   ```bash
   sudo systemctl restart flask-app
   ```

2. 使用 curl 測試 API 是否恢復正常：
   ```bash
   curl http://localhost/api/order
   ```

3. 成功回傳 JSON 資料後，確認服務恢復正常。

---

## 📂 證據記錄  

📎 **圖一：** `/var/log/nginx/error.log` 顯示：  
```
connect() failed (111: Connection refused) while connecting to upstream
```

---

## 🔄 後續處理  

1. 撰寫自動監控腳本（如 `check_api.sh`），定期檢查 `/api/order` 回應狀態。
2. 將腳本加入 crontab 排程：
  ```cron
  */5 * * * * root /home/milly/falsk_project/check_api.sh
  ```
3. 搭配 log 留存與通知機制（email、line notify 等）追蹤錯誤。

