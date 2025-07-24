# Flask 自動化部署 Troubleshooting

> 日期：2025-07-17  
> 主題：使用 Ansible 自動部署 Flask API 並處理部署過程的錯誤

---

## 問題 1：`pip3` 無法找到

**錯誤訊息：**
```
TASK [Install pip for python3]
fatal: FAILED! => {"msg": "[Errno 2] No such file or directory: b'/usr/local/bin/pip3'"}
```

**原因：**
Ubuntu 24.04 採用 PEP 668 導致無法用 get-pip.py 全域安裝 pip。

**解法：**
改用 Python 內建 `venv` 建立虛擬環境，並在其中使用 pip 安裝 Flask：
```yaml
- name: Create venv
  command: python3 -m venv /opt/myflask/venv

- name: Install Flask in venv
  command: /opt/myflask/venv/bin/pip install flask
```

---

## 問題 2：systemd 服務檔寫入錯誤目錄

**錯誤訊息：**
```
msg": "Destination directory /etc/systemd/system//opt does not exist"
```

**原因：**
`copy` 模組中 `dest:` 的值寫成了錯誤的變數 `{{ flask_app_dir }}`，導致將 service 檔寫入 `/etc/systemd/system//opt/...`。

**解法：**
修正為：
```yaml
dest: /etc/systemd/system/{{ '{{ flask_service }}' }}.service
```

---

## 問題 3：無法啟用 systemd 服務

**錯誤訊息：**
```
msg": "Unable to enable service /opt/myflask: Failed to enable unit: File /opt/myflask: Invalid argument"
```

**原因：**
`systemd:` 模組的 `name:` 給的是應用程式目錄 `/opt/myflask`，但應該指定 service 名稱。

**解法：**
修正為：
```yaml
name: "{{ '{{ flask_service }}' }}"
```

---

## 小結與建議

1.  Ubuntu 24.04 預設禁止全域安裝 pip 套件，建議一律使用 venv。
2. 撰寫 systemd 服務時，請特別注意目錄與 service 名稱的變數。
3. 可搭配 `notify` 與 `handlers` 精簡 systemd 相關操作。

---
