# 系統維運問題排查報告：Cron 排程未執行

## 發生時間

2025/07/03 15:43:27(UTC+8)

## 問題現象

設定的 Cron 排程腳本 `/home/milly/falsk_project/check_trade_api.sh` 未按照排定時間執行，且 `/home/milly/api_cron.log` 檔案中無任何新內容，懷疑 Cron 未正常運作。

## 排查過程

1. 利用下列指令確認 cron 服務狀態

`sudo systemctl status cron`

結果：Cron 為 active，但實際未執行排程。

2. 查看排程是否有載入錯誤

指令：`sudo journalctl -u cron | tail -n 50`

出現錯誤訊息：

Error: bad username; while reading /etc/crontab
(*system*) ERROR (Syntax error, this crontab file will be ignored)

3. 檢查 /etc/crontab 內容

定位有問題的行，推測可能原因如下：

- 排程行的 user 欄位拼錯或不存在

- command 欄含有空格、中文、錯誤引號等格式錯誤

- 多筆排程中有其他腳本也有類似語法錯誤，導致整份被忽略

4. 移除多餘或錯誤排程行

修正為以下格式：

`*/5 * * * * milly /home/milly/falsk_project/check_trade_api.sh >> /home/milly/api_cron.log 2>&1`

確認：

- `milly` 是有效帳號（使用 `id milly` 驗證）

- 路徑正確、無中文夾雜或打錯字

- 腳本具執行權限（`chmod +x`）

5. 重啟 cron 並觀察 log

指令：

``` bash
sudo systemctl restart cron

sudo journalctl -u cron | tail -n 20
```

執行後不再出現錯誤，Cron 正常執行腳本，log 有更新。

## 問題原因

/etc/crontab 中有其他排程行語法錯誤，導致整份 crontab 被 Cron 忽略

## 解決方法

將無效行清除後問題即解決。

## 證據記錄

(/images/cron_schedule_not_execute.png)

(圖一) log顯示錯誤訊息Error: bad username; while reading /etc/crontab(*system*) ERROR (Syntax error, this crontab file will be ignored)

## 後續建議

1. 每次修改 `/etc/crontab` 後務必用 `journalctl -u cron` 驗證是否有語法錯

2. 多人共用環境可改用 `crontab -e` 為個別使用者設定排程，避免系統級排程失效

3. 建議建立 `/etc/crontab` 備份版本，便於除錯時回溯