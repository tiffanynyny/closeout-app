# 工程收尾管理系統（雲端同步版 v3）

鉅科資訊的工程收尾追蹤系統。辦公室人員和現場師傅可以用電腦或手機登入，看到同一份即時同步的資料。

## 架構

| 部分 | 使用 |
|---|---|
| 網頁 | 單一 `index.html`，可安裝到手機主畫面（PWA） |
| 資料庫、登入、附件 | Supabase（Postgres、Auth、Storage、Realtime） |
| 權限 | 資料庫 Row Level Security，見 `setup.sql` |
| 架站 | GitHub Pages（或任何靜態網站空間） |

## 檔案

| 檔案 | 說明 |
|---|---|
| `index.html` | 系統本體，最上方填 Supabase 連線設定 |
| `setup.sql` | 在 Supabase SQL Editor 執行一次，建立資料表與權限 |
| `manifest.webmanifest`、`sw.js`、`icon-*.png`、`apple-touch-icon.png` | 讓手機可以「加入主畫面」 |

## 第一次設定

1. Supabase → **SQL Editor** → 貼上 `setup.sql` 全部內容 → **Run**，看到「設定完成 ✅」
2. Supabase → **Authentication** → **Sign In / Providers** → **Email** → 關閉 **Confirm email** → **Save**
   （帳號用英文代號登入，系統會自動補上 `@closeout.local`，不會寄信）
3. 把資料夾所有檔案放上網站空間（GitHub Pages：公開 repo → Settings → Pages → Deploy from branch → main）
4. 打開網址 → **申請帳號**：第一位申請的人自動成為管理者
5. 其他人申請帳號後，管理者到「帳號」頁開通並設定角色

## 角色與權限

| | 管理者 | 辦公室人員 | 現場師傅 |
|---|---|---|---|
| 查看全部工程、日曆、統計、我的待辦 | ✓ | ✓ | ✓ |
| 改收尾項目狀態、備註、上傳附件／拍照 | ✓ | ✓ | ✓ |
| 新增／編輯／刪除工程、階段、負責人、期限 | ✓ | — | — |
| 範本、帳號、異動紀錄、資料匯入匯出 | ✓ | — | — |

權限在資料庫端檢查：一般人員和師傅就算改網頁程式，也只能改「狀態、備註、完成日、附件」四個欄位。

## 安全說明

- `index.html` 裡的 anon key 是公開金鑰，本來就會被瀏覽器看到；資料靠 RLS 保護，未登入或未開通的人讀不到任何資料。
- **service_role key 與資料庫密碼絕對不要放進網頁或 repo。**
- 附件存在私人空間，只能透過 1 小時有效的簽名網址開啟。

## 手機加入主畫面

- **iPhone（Safari）**：分享按鈕 → 加入主畫面
- **Android（Chrome）**：右上角 ⋮ → 安裝應用程式／加到主畫面

## 從本機版搬資料

本機版 → 資料 → 下載完整備份（.json）→ 雲端版 → 資料 → 匯入本機版備份。工程、收尾項目、附件會一起上傳；本機版的帳號密碼不會帶過去，請重新申請。

## 免費方案注意

Supabase 免費方案資料庫 500 MB、附件 1 GB；專案長時間沒人使用可能被暫停，到後台按 Restore 即可恢復。手機照片上傳前會自動壓縮到約 2000 像素寬。
