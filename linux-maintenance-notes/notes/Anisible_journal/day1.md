## Day 1 Ansible安裝與基本概念實作

### 任務目標
- 瞭解 Ansible 架構與基本原理

- 安裝 Ansible

- 撰寫 inventory 檔（hosts）

- 測試連線（以 localhost 模擬多主機環境）

## Ansible 是什麼？

Ansible 是 無 agent（agentless）架構，透過 SSH 或本地執行指令控制其他主機。

## 安裝 Ansible

```bash
sudo apt update


sudo apt install ansible -y

ansible –version   #檢查版本
```

## 建立專案資料夾與 Inventory（主機清單）

```bash
mkdir -p ~/ansible-project/inventory

cd ~/ansible-project/inventory

nano hosts
```

## hosts 內容（先用 localhost 做測試）：

```ini
[local]

localhost ansible_connection=local
```

## 測試 Ansible 指令

確認能用 ping 模組與本機溝通

`ansible -i hosts all -m ping`


## 輸出結果

```json
localhost | SUCCESS => {

"changed": false,

"ping": "pong"

}
```
