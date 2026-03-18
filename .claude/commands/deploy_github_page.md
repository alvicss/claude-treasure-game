# Deploy to GitHub Pages

你是 GitHub Pages 部署嚮導。請依照以下流程，引導使用者完成完整的部署作業。

## 部署流程

### STEP 1：環境前置檢查

執行以下指令，確認環境是否就緒：

```bash
git --version
gh --version
node --version
npm --version
```

**若 `gh` 指令不存在**，提示使用者安裝 GitHub CLI：
- Windows：`winget install --id GitHub.cli`
- macOS：`brew install gh`
- 安裝後請重新開啟終端機，再執行此指令

---

### STEP 2：確認 GitHub 登入狀態

```bash
gh auth status
```

**若未登入**，引導使用者登入：

```bash
gh auth login
```

提示使用者：
1. 選擇 `GitHub.com`
2. 選擇 `HTTPS`
3. 選擇 `Login with a web browser`
4. 複製 one-time code，在瀏覽器完成驗證

---

### STEP 3：確認 Git 初始化

```bash
git status
```

**若尚未初始化 git**，執行：

```bash
git init
git add .
git commit -m "initial commit"
```

---

### STEP 4：確認或建立 GitHub Remote Repo

執行以下指令確認是否已有 remote：

```bash
git remote -v
```

**若沒有 remote**，詢問使用者想要的 repo 名稱，然後執行（以 `<REPO_NAME>` 為例）：

```bash
gh repo create <REPO_NAME> --public --source=. --remote=origin --push
```

這會自動：
- 在 GitHub 建立公開 repo
- 設定 origin remote
- 推送目前程式碼

**若已有 remote**，從 remote URL 取出 repo 名稱，供後續設定使用。

---

### STEP 5：取得 repo 資訊

從 remote URL 解析出 GitHub username 與 repo name：

```bash
git remote get-url origin
```

格式通常為 `https://github.com/<USERNAME>/<REPO_NAME>.git`，請記住這兩個值。

---

### STEP 6：設定 vite.config.ts 的 base 路徑

GitHub Pages 的網址格式為 `https://<USERNAME>.github.io/<REPO_NAME>/`，
因此需要設定 Vite 的 `base` 為 `/<REPO_NAME>/`。

讀取目前的 `vite.config.ts`，在 `defineConfig({` 中加入 `base` 設定：

```ts
export default defineConfig({
  base: '/<REPO_NAME>/',   // ← 加入這行
  plugins: [react()],
  // ... 其餘設定不變
});
```

確認修改後告知使用者已更新。

---

### STEP 7：安裝 gh-pages 套件

```bash
npm install --save-dev gh-pages
```

---

### STEP 8：在 package.json 加入 deploy script

讀取 `package.json`，在 `scripts` 區塊加入：

```json
"predeploy": "npm run build",
"deploy": "gh-pages -d build"
```

---

### STEP 9：執行部署

```bash
npm run deploy
```

這會自動：
1. 執行 `npm run build` 建置專案
2. 將 `build/` 資料夾推送到 `gh-pages` branch

---

### STEP 10：設定 GitHub Pages 來源

```bash
gh api repos/<USERNAME>/<REPO_NAME>/pages \
  --method POST \
  -f source='{"branch":"gh-pages","path":"/"}'
```

若已設定過（API 回傳 409），則改用 PATCH 更新：

```bash
gh api repos/<USERNAME>/<REPO_NAME>/pages \
  --method PUT \
  -f source='{"branch":"gh-pages","path":"/"}'
```

---

### STEP 11：確認部署結果

```bash
gh browse
```

或告知使用者網址為：
`https://<USERNAME>.github.io/<REPO_NAME>/`

提醒使用者 GitHub Pages 首次啟用需要 **1~3 分鐘** 才會生效。

---

## 後續再次部署

每次更新程式碼後，只需執行：

```bash
git add .
git commit -m "update"
git push
npm run deploy
```

---

## 注意事項

- `base` 路徑必須與 repo 名稱完全一致（區分大小寫）
- 若 repo 名稱為 `<USERNAME>.github.io`，則 `base` 設為 `/`
- 部署完成後，若畫面空白，通常是 `base` 設定錯誤
