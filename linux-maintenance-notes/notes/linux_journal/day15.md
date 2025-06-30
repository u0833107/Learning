# Day15 作業系統原理與基本概念補充

### 什麼是作業系統？核心職責？

Q1： 作業系統（OS）的三大核心功能是什麼？

- 程序管理：負責建立、排程、終止與切換程序(process)，並確保CPU資源分配合理。

- 記憶體管理：管理RAM的使用，包括分配與回收記憶體、虛擬記憶體機制(如swap)

- 檔案系統管理：管理資料儲存方式，讓使用者能方便透過檔名與目錄方便讀寫檔案，並負責權限控制

Q2： 核心（Kernel）與使用者空間（User space）的差異

權限和功能不同。

|         項目      |     Kernel Space（核心空間） |     User Space（使用者空間) |
|:-----------------:|:----------------------------:|:---------------------------:|
|         權限      |           高(ring 3)         |          低(ring 0)         |
|       負責範圍    |      控制硬體、系統資源管理  |        執行一般應用程式     |
|       錯誤影響    |       錯誤可能導致系統當機   |      錯誤僅影響該應用程式   |
|     程式執行模式  |      系統呼叫(system call)   |           非特權指令        |

Q3： 為什麼作業系統需要管理記憶體與處理程序？

為了保持系統穩定性並有效率的處理多個應用程式。

記憶體管理目的：

- 避免不同程序彼此覆蓋記憶體(保護性)

- 提供多工支援(記憶體分頁與虛擬記憶體)

- 優化效能(避免記憶體碎片)

程序管理目的：

- 多任務並行執行(Multitasking)

- 依優先順序與CPU負載做排程(Scheduler)

- 確保每個process有公平的資源和獨立性

### 小結

作業系統負責「資源管理」、「程序控制」與「硬體抽象」，讓使用者與程式能安全、穩定地與硬體互動。

### 開機流程(Linux與Windows)

基本流程：

- Linux：BIOS/UEFI→Bootloader(GRUB) →Kernel→systemd(PID1)
- Windows：BIOS/UEFI→Boot Manager→winload.exe→ntoskrnl.exe

補充1：名詞解釋

- GRUB：Linux最常見的bootloader，也可以dual boot多系統

- systemd：是目前主流的init系統，管理服務與目標狀態(target)

- PID 1：是整個使用者空間的第一個process，當他掛了整個系統都會死掉

補充2：什麼是init

- Init是initialization的縮寫，意思是初始化程序

- Linux開機後的第一個使用者空間程式(PID 1)

- 負責依序啟動各種系統服務與背景程序(daemon)

- 早期的Linux發行版常用的init系統是SysVinit(使用/etc/init.d腳本機制)

補充3：什麼是systemd

Systemd是現今主流的init系統，設計用來取代傳統的SysVinit，同樣是PID 1，但設計上更模組化、平行化、可追蹤與自我管理

功能包含以下：

1. 啟動與管理服務

2. 日誌管理

3. 使用者session管理

4. 掛載檔案系統

5. 依賴控制與開機速度最佳化

### 小結

init是第一個被kernel啟動的程式，systemd是現今主流的init系統，負責啟動與管理所有的系統服務與資源

### 什麼是 Context Switching

Q1：CPU 如何在多個 process 間切換？

作業系統會暫時停止目前正在處理的process，保存其上下文(如：暫存器、程式計數器、堆疊位置等)再載入下一個要執行的process的上下文資訊。整個動作稱為上下文切換(Context Switch)。

Context = 一個程序的CPU執行狀態資訊

觸發時機：

- 時間片用完(preemptive)

- 等待I/O或資源

- 優先權切換

Q2：為什麼Context Switch太多會拖慢效能？

每次執行上下文切換，作業系統必須儲存與還原程序狀態，這會花費CPU時間與資源，且在這段時間內CPU無法執行實際的應用程式邏輯，屬於間接成本(overhead)。若上下文切換發生的太頻繁，會造成效能下降。

補充1：間接成本(overhead)

為了完成某項工作而產生的額外資源消耗或時間開銷但本身不直接產生效益。

以context switch為例：

當CPU要從一個process切換到另一個process時，要

1. 保存目前程序的狀態

2. 載入新的程序的狀態

3. 這段期間CPU無法執行實際的程式邏輯

這段切換工作就是間接成本(overhead)

補充3：進階用延伸補充

- 一次Context Switch會占用數百個CPU cycle，甚至更多

- 有些輕量級程序(thread)或container會優化switch開銷

- 用vmstat可觀察上下文交換次數(CS欄位)

  `vmstat 1 5   #每秒輸出一次系統狀態，共五次`

