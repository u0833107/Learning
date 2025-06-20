MySQL 遠端連線錯誤排查 - (10060) 無法連線

問題描述

使用 Windows PowerShell 嘗試從主機連線至 VirtualBox 虛擬機中的 MySQL：

指令：mysql -u remotedb -h 10.0.2.15 -p

錯誤訊息：Error 2003 (HY000): Can't connect to MySQL server on '10.0.2.15' (10060)

初步確認
- MySQL 服務狀態為 active
- MySQL 設定 bind-address 為 0.0.0.0，port = 3306
- 使用 ss 指令確認 MySQL 已監聽 0.0.0.0:3306
- 使用 GRANT 指令，確認 remotedb@'10.0.2.%' 有正確權限

問題關鍵

在 NAT 模式中，主機無法直接透過 10.0.2.15（虛擬機內部 IP）連線。
原本的 Port Forwarding 將主機連接埠也設為 3306，導致衝突或無作用。

解決方法
1. 修改 VirtualBox Port Forwarding 設定：
 - 名稱：mysql
 - 協定：TCP
 - 主機連接埠：13306
 - 客體連接埠：3306

2. 從主機改用以下連線指令：
 mysql -u remotedb -h 127.0.0.1 -P 13306 -p
備註
若使用橋接模式（非 NAT），可使用虛擬機實際 IP（如 10.0.2.15）連線，但建議初學者保留 NAT 並使用 Port Forwarding 控制連線。

（此報告可作為連線失敗排查與 MySQL 遠端存取設定的範例）