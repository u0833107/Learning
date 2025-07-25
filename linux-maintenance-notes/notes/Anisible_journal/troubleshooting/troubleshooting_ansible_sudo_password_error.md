# Ansible Playbook 執行錯誤紀錄：無法執行 sudo 指令

## 問題描述

在執行 Ansible Playbook 指令時：
```
ansible-playbook -i inventory/hosts nginx_setup.yaml
```
出現以下錯誤：
```
fatal: [localhost]: FAILED! => {"changed": false, "module_stderr": "sudo: a password is required", ...}
```

## 問題原因

Ansible 預設無法在非互動式環境中自動提供 sudo 密碼，因此當 Playbook 嘗試執行需要 sudo 權限的任務時（例如安裝 nginx），就會因為無法提供密碼而中斷執行。

## 解決方式

使用 `-K` 選項，在執行時主動輸入 sudo 密碼：
```
ansible-playbook -i inventory/hosts nginx_setup.yaml -K
```

這樣 Playbook 開始時會提示輸入 sudo 密碼，讓後續所有需要 sudo 權限的任務可以順利執行。

## 補充說明

如果希望避免每次都輸入密碼，可以修改 sudo 設定檔 `/etc/sudoers`（不建議在未經授權環境中這麼做）來對 Ansible 所用帳號啟用 NOPASSWD：

```bash
# 使用 visudo 編輯 sudoers
sudo visudo

# 加入如下內容（以 milly 為例）
milly ALL=(ALL) NOPASSWD: ALL
```
