# Flask App Systemd 部署 Troubleshooting

本文件紀錄在使用 Ansible 部署 Flask App 並透過 systemd 管理時，常見的錯誤與解法。

---

## ❌ 問題：Flask App 啟動失敗

使用指令：

```
sudo systemctl status flask-app
```

出現錯誤：

```
Port 5000 is in use by another program.
Main process exited, code=exited, status=1/FAILURE
Failed to start flask-app.service
```

---

## ✅ 原因

Flask 預設使用 port `5000`。若之前手動執行過 `python3 app.py` 或背景有其他程式佔用 5000，會導致 systemd 無法再次綁定此 port。

---

## 🛠️ 解法一：關掉佔用 port 的程式

1. 查看是誰佔用了 5000 port：

```
sudo lsof -i :5000
```

2. 結束該程式：

```
sudo kill -9 <PID>
```

3. 重啟服務：

```
sudo systemctl restart flask-app
```

---

## 🛠️ 解法二：改用其他 Port（如 5050）

1. 修改 `app.py`：

```python
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5050)
```

2. 儲存後重啟 systemd：

```
sudo systemctl daemon-reload
sudo systemctl restart flask-app
```

3. 檢查服務狀態：

```
sudo systemctl status flask-app
```

---

## ✅ 結果

- Systemd 成功啟動
- Flask App 在新 port（如 `http://localhost:5050`）正常提供服務

---

## 🔁 備註

- 開發期間避免同時使用 systemd 與手動執行 `python3 app.py`
- 若需要併行多個 Flask App，請確保使用不同的 port
