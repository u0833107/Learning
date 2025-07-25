# Ansible Playbook 錯誤排查記錄：`service` 模組 state 值錯誤

## 🧩 問題描述

在執行 Ansible Playbook `nginx_setup.yaml` 時出現以下錯誤：

```
fatal: [localhost]: FAILED! => {
  "changed": false,
  "msg": "value of state must be one of: reloaded, restarted, started, stopped, got: start"
}
```

## 🔍 錯誤原因

Ansible 的 `service` 模組要求 `state` 欄位必須是以下值之一：

- `started`
- `stopped`
- `restarted`
- `reloaded`

但錯誤中顯示：

```
got: start
```

說明原本的 playbook 使用了不存在的 `start` 值。

## 🛠️ 處理步驟

1. 打開 `nginx_setup.yaml`：
   ```yaml
   - name: Ensure nginx is running
     service:
       name: nginx
       state: start  # ❌ 錯誤的設定
   ```

2. 將 `state: start` 修改為正確的 `state: started`：
   ```yaml
   - name: Ensure nginx is running
     service:
       name: nginx
       state: started  # ✅ 正確
   ```

3. 儲存檔案後重新執行：
   ```bash
   ansible-playbook -i inventory/hosts nginx_setup.yaml -K
   ```

## ✅ 處理結果

Playbook 成功安裝並啟動 nginx 服務，Ansible 任務全部執行成功。

## 📘 補充筆記

| 指令        | 說明                         |
|-------------|------------------------------|
| `started`   | 啟動服務                     |
| `stopped`   | 停止服務                     |
| `restarted` | 重新啟動                     |
| `reloaded`  | 重新載入設定（不會關掉服務）|
