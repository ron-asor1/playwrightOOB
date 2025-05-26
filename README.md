# Playwright Test Automation Infrastructure - README

## 📦 Project Setup

### 1. Initialize the Project

```bash
npm init -y
npm install -D @playwright/test
npx playwright install
```

### 2. Project Structure (Best Practice)

```
playwright-project/
├── tests/                # Test files
│   └── example.spec.ts
├── pages/               # Page Object Models (POM)
│   └── login.page.ts
├── utils/               # Custom utilities, data, helpers
│   └── test-data.ts
├── playwright.config.ts # Playwright config
├── test-results/        # Test execution output
├── allure-results/      # Raw Allure results
├── README.md
└── package.json
```

---

## ⚙️ Playwright Configuration

### 3. `playwright.config.ts`

```ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './tests',
  retries: 1,
  reporter: [['list'], ['allure-playwright']],
  use: {
    headless: true,
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    trace: 'on-first-retry',
  },
});
```

### 4. Allure Reporter Setup

```bash
npm install -D allure-playwright
```

Add this to `package.json` scripts:

```json
"scripts": {
  "test": "npx playwright test",
  "allure:report": "allure generate ./allure-results --clean -o ./allure-report && npx http-server ./allure-report"
}
```

> Install Allure CLI if needed: [https://docs.qameta.io/allure/](https://docs.qameta.io/allure/)

---

## 🧪 Writing Tests

### 5. Basic Example - `tests/example.spec.ts`

```ts
import { test, expect } from '@playwright/test';

test('homepage has title', async ({ page }) => {
  await page.goto('https://playwright.dev');
  await expect(page).toHaveTitle(/Playwright/);
});
```

---

## 🔗 GitHub Integration

### 6. GitHub Actions Workflow (`.github/workflows/playwright.yml`)

```yaml
name: Playwright Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm ci

      - name: Install browsers
        run: npx playwright install --with-deps

      - name: Run tests
        run: npm test

      - name: Generate Allure Report
        run: npm run allure:report

      - name: Upload Allure HTML Report
        uses: actions/upload-artifact@v4
        with:
          name: allure-report-${{ github.run_id }}
          path: allure-report
```

---

## 🚀 Best Practices

### ✅ General

* Use Page Object Model (POM) to separate UI logic
* Store test data in `/utils`
* Use `expect.soft()` for non-blocking validations

### 📊 Reporting

* Use Allure for structured, interactive test reports
* Capture screenshots/videos for failure debugging

### 💡 Tips

* Use `--project=chromium` to run only on specific browsers
* Use `.only()` and `.skip()` smartly while debugging
* Clean results after each run with `--clean`

---

## 📂 Resources

* [Playwright Docs](https://playwright.dev)
* [Allure Docs](https://docs.qameta.io/allure/)
* [GitHub](https://docs.github.com/en/actions) 
