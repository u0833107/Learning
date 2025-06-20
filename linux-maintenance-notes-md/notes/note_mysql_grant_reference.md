MySQL 權限授權範圍說明與對照表
本文件說明在使用 GRANT 指令時，MySQL 權限作用範圍的差異與常見用途。

基本語法說明
GRANT 權限 ON 資料庫.資料表 TO '使用者'@'主機';

權限範圍差異
|     授權範圍     |                                     語法範例                                  |                      適用場景                   |
|:----------------:|:-----------------------------------------------------------------------------:|:-----------------------------------------------:|
|    整個資料庫    |    GRANT SELECT ON linux_practice.* TO 'user'@'localhost';                    | 授權整個資料庫的所有表格，如開發者、測試人員    |
|    單一資料表    |    GRANT SELECT ON linux_practice.users TO 'user'@'localhost';                | 僅授權特定表格，如只查users的API用戶            |
|     特定欄位     |    GRANT SELECT(name,email) ON linux_practice.users TO 'user'@'localhost';    | 僅能查欄位，如報表系統                          |

使用建議

• 對開發者/維運：使用 linux_practice.*，方便操作所有表。
• 對外部查詢：建議使用 db.table 或欄位級授權，符合最小權限原則。
• 權限過小可能會導致 USE、SELECT 失敗。
