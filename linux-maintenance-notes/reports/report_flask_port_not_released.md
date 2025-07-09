# 系統維運事件報告：Flask 無法釋放 port 問題

## 發生時間

## 2025/07/03 15:43:27(UTC+8)

## 問題描述

使用 `sudo lsof -i :5000` 檢查後，每次 kill 掉 Flask 的 PID，不久後又會出現新的 PID，導致 port 5000 無法釋放。

## 排查過程

1.懷疑可能是定時腳本重啟Flsak，利用下列指令先將Cron排程停用

sudo systemctl stop cron

停用後仍然出現新的PID，故不是Cron問題

2.執行下列指令查看systemd

sudo systemctl list-units –type service

發現有設定flask-app.service服務，導致Flask一直不斷自動restart

3.執行以下指令停用並解除 systemd 控管：

sudo systemctl stop flask-app
sudo systemctl disable flask-app

再次使用 `sudo lsof -i :5000` 查詢，Flask 不再自動重啟，port 5000 已釋放。

## 後續建議

日後如要維持 Flask 長時間執行，可使用 systemd，但需明確設定其 restart 行為。

若不再需要 systemd 控制 Flask，可刪除 `flask-app.service` 的設定檔，避免誤啟。