# Day14

ping測試網路通暢度

`ping 8.8.8.8`

`ping google.com`

補充：

預設 ping 不會自動停止（不像 Windows 的 ping 預設只送 4 次）

會持續送出 ICMP 封包並顯示回應時間、TTL、封包大小等資訊。

可利用Ctrl+C中斷或使用ping -c 次數

EX：`ping -c 4 8.8.8.8`   #只會傳送四次就結束

curl測試網站狀態

`curl`

`curl -I`    #只會顯示header內容

查看目前開了那些port

`netstat -tuln`

或

`ss -tuln`   #看目前哪些服務有在LISTEN狀態、綁在哪個 port

查看自己的IP和路由

`ip a`    #顯示 IP 設定

`ip r`    # 顯示路由

開關防火牆&設定port

```bash
sudo ufw status   #查看狀態(預設為inactive)

sudo ufw enable   #啟用ufw

sudo ufw allow 80   #新增規則(允許(HTTP(nginx))

sudo ufw deny 22   #新增規則(封鎖SSH)

sudo ufw delete allow 80   #刪除規則(允許80 port)
```

補充1：

`sudo ufw status verbose`   #查看預設規則

`sudo ufw status numbered`   #查看目前規則(加上編號)

補充2：

`traceroute google.com`   #查看封包經過幾層路由

