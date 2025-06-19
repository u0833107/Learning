網站無法連線 - 模擬維運排查紀錄
Step 1：模擬錯誤情境
故意停掉 nginx 服務：
sudo systemctl stop nginx
 Step 2：測試網站狀態
使用 curl 測試本機網站連線：
curl http://localhost
錯誤訊息顯示：
Failed to connect to localhost port 80 after 0 ms: Couldn't connect to server
Step 3：進行排查
依照維運排查邏輯逐項確認：
 Step 4：問題修復與驗證
啟動 nginx：
sudo systemctl start nginx
再次確認 port 是否開啟：
sudo ss -tulnp
確認 nginx 已重新啟動，port 80 已處於 LISTEN 狀態
再次測試：
curl http://localhost
結果顯示網頁內容，網站恢復正常。