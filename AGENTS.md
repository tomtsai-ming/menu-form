# menu-form 專案規則

## 專案用途
「文章牛肉湯｜點餐選項確認單」：給店家在手機上逐步回答點餐選項（辣度、加價、口味、外帶、出餐註記等）的單頁問卷，填完可複製文字（貼到 LINE）或下載 JSON／TXT 檔交回。

## 結構
- `index.html`：唯一的程式檔，HTML + 內嵌 CSS + 一段內嵌 `<script>`。
  - `const DATA = {...}`：菜單品項、選項分類（groups/cats/money/ask/same）都寫死在這一行 JSON。改菜單就改這裡，注意保持合法 JSON。
  - 填答進度存在瀏覽器 `localStorage`（key：`wenzhang_beef_v1`）。改資料結構時要換 key 或做相容，避免舊存檔讓頁面壞掉。
  - 頁面有 `noindex,nofollow`，不要拿掉。
- `.github/workflows/codeql.yml`：push／PR 到 `main`、每週三排程（cron `27 3 * * 3`）及手動觸發時跑 CodeQL（javascript-typescript，含內嵌腳本）。
- 沒有 package.json、沒有建置步驟、沒有外部相依（無 CDN、無 fetch）。

## 執行／測試
- 本機預覽：直接用瀏覽器開 `index.html`（不需伺服器）。注意 `file://` 下剪貼簿 API 可能不可用，會退回顯示文字框。
- 沒有自動化測試。內嵌 JS 語法檢查（不需安裝套件）：
  ```sh
  awk '/<script>/{f=1;next}/<\/script>/{f=0}f' index.html > /tmp/menu-inline.js && node --check /tmp/menu-inline.js
  ```
- 修改後請在手機寬度（約 375px）實際點過一輪流程：開始填寫 → 各分類 → 完成頁的複製／下載。

## 部署
- 用 GitHub Pages 發布：`main` 分支根目錄，網址 https://tomtsai-ming.github.io/menu-form/ 。合併到 `main` 就會自動上線，所以 push／合併前要先經使用者同意。
- push 到 `main` 會觸發 CodeQL，並可能影響線上頁面：push、開 PR、合併前都要先取得使用者同意。

## 限制
- 不要新增外部腳本、追蹤碼或任何會把填答內容送到外部的請求；資料只透過使用者自己複製／下載交回。
- 不要提交機密或本機檔案（`.env*`、金鑰、密碼、token、`.DS_Store` 等）。
- 對外發送（傳訊息、寄信）一律先問使用者。

## 交付前檢查
1. 上面的 `node --check` 通過。
2. `DATA` 仍為合法 JSON，品項代碼沒有重複或誤改。
3. 瀏覽器手機寬度走過完整流程、無 console 錯誤、無水平捲動。
