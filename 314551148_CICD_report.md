# 314551148 CICD 作業報告

## CI Pipeline 說明

本作業新增 `.github/workflows/ci_314551148.yaml`，並設定在任何 branch push 時自動執行。

主要 pipeline 內容：

```yaml
name: CI 314551148

on:
  push:
    branches:
      - '**'

jobs:
  checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-node@v5
        with:
          node-version: '22'
          cache: npm
      - run: npm ci
      - run: npm run typecheck
      - run: npm run format:check
      - run: |
          mkdir -p reports
          npm test -- --reporter=default --reporter=junit --outputFile.junit=reports/vitest-junit.xml
```

設計策略：

- `npm ci`：依照 `package-lock.json` 安裝固定版本相依套件，確保 CI 環境一致。
- `npm run typecheck`：執行 TypeScript 型別檢查，不輸出編譯結果。
- `npm run format:check`：使用 Prettier 檢查程式碼與設定檔格式。
- `npm test`：使用 Vitest 執行測試，並輸出 `reports/vitest-junit.xml`。
- `Publish test result summary`：讀取 JUnit XML，將測試總數、失敗數、錯誤數與執行時間寫入 GitHub Actions job summary。
- `Upload test report artifact`：上傳 JUnit XML，方便在 GitHub Actions 結果頁下載檢查。

因為 GitHub Actions 預設會在任一步驟回傳非 0 exit code 時標記 job 失敗，所以 TypeScript、Prettier 或 Test 任一檢查失敗時，pipeline 都會顯示 failed。

## CI 執行結果截圖

請在 push 後到 GitHub repository 的 Actions 頁面，開啟 `CI 314551148` workflow，貼上至少一張成功執行截圖。

- 成功截圖：待補
- GitHub Actions 結果頁連結：待補

## 失敗案例說明

失敗案例可使用以下任一方式製造：

1. TypeScript 型別錯誤：例如在 `src/app.ts` 加入 `const value: number = 'bad';`。
2. Prettier 格式錯誤：例如手動改成不符合 `.prettierrc` 的縮排或引號格式。
3. 測試失敗：例如把 `test/app.test.ts` 中 `/health` 預期結果改成 `{ status: 'fail' }`。

建議示範方式：修改 `test/app.test.ts` 的預期值，push 後 GitHub Actions 會在 `Run tests with JUnit report` 步驟失敗；失敗原因是 API 實際回傳 `{ status: 'ok' }`，但測試預期值被改成錯誤內容。修正方式是把測試預期值改回 `{ status: 'ok' }` 後重新 push。

- 失敗截圖：待補
- 錯誤原因：待補
- 修正方式：待補

## 繳交資訊

最後請將本報告補上姓名、成功截圖、失敗截圖與 GitHub Actions 連結後，匯出成 PDF，檔名格式為：

`314551148_姓名_CICD_作業.pdf`
