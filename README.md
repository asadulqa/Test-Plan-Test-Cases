# ReConvert Automation — Portfolio Project

> **Background:** This repository showcases QA automation and test documentation work I completed at my **previous company**, [ReConvert / Upsell.com](https://app.reconvert.com) — a Shopify post-purchase upsell platform. It is shared here as a **portfolio sample** of my skills in test planning, Playwright, Selenium, and pytest. I no longer work at ReConvert; app access, tokens, and test stores may require credentials from that organization.

End-to-end test automation and manual test plans for the ReConvert Shopify app — post-purchase upsells, Thank You pages, Product page slots, and the Flow Builder editor.

| | |
|---|---|
| **Former employer** | ReConvert / Upsell.com |
| **Role focus** | QA automation, test plans, Flow Builder widget testing |
| **Product** | Shopify embedded app for post-purchase & checkout upsells |
| **Test store (internal)** | `reconvert-automated-testing.myshopify.com` |

**App entry (when access is available):** `https://app.reconvert.com/shopify/app/?shop=<shop>&token=<JWT>`

---

## What this repo covers

| Area | Description |
|------|-------------|
| **Dashboard** | App load, Edit flows, analytics table |
| **Flow Builder** | Thank you page, Product page, slot & widget editors |
| **Banner (pre-made)** | Status, title, rich text, toggles, CTA button, Save/persist |
| **Product recommendations** | Product details block, Success/Error banner, slot lifecycle |
| **Collection picker** | Select specific collection → Automated Collection |

Manual test plans and test cases live in [`docs/RECONVERT_TEST_PLAN.pdf`](docs/RECONVERT_TEST_PLAN.pdf).

---

## Project structure

```
reconvert_automation/
├── docs/
│   ├── RECONVERT_TEST_PLAN.html      # Master test plan (HTML source)
│   ├── RECONVERT_TEST_PLAN.pdf       # Generated PDF
│   └── generate-test-plan-pdf.cjs    # Regenerate PDF from HTML
├── pages/
│   ├── ReConvertAppPage.js           # Flow Builder & widget page object
│   ├── ShopifyAdminPage.js           # Shopify admin navigation
│   └── ShopifyLoginPage.js           # Shopify login flow
├── tests/                            # Playwright (JavaScript)
│   ├── shopify-reconvert.spec.js
│   ├── shopify-login-dashboard.spec.js
│   ├── flow-builder-product-page.spec.js
│   └── flow-builder-product-recommendation-product-details.spec.js
├── test_product_page.py              # Playwright (Python) — Banner widget
├── selenium/                         # Selenium + pytest
│   ├── test_banner_premade.py
│   ├── test_product_banner.py
│   └── product_banner_page.py
├── playwright_python_bdd/            # pytest-bdd smoke scenarios
│   ├── features/reconvert_app.feature
│   └── README.md
├── playwright.config.js
├── package.json
└── .env.example
```

---

## Prerequisites

- **Node.js** 18+ (Playwright JS suite)
- **Python** 3.9+ (Playwright Python & Selenium suites)
- **Google Chrome** (Selenium; Playwright installs its own Chromium)
- A valid **signed ReConvert app URL** for the test shop (see [Security](#security))

---

## Setup

### 1. Playwright (JavaScript)

```bash
cd reconvert_automation
npm install
npx playwright install chromium
cp .env.example .env   # Shopify login credentials (optional)
```

### 2. Playwright (Python) — Banner tests

```bash
source playwright_python_bdd/.venv/bin/activate   # or create a new venv
pip install -r playwright_python_bdd/requirements.txt
playwright install chromium
```

### 3. Selenium — Product banner tests

```bash
cd selenium
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

> **Important:** Run Selenium tests from inside the `selenium/` folder so Python imports the installed `selenium` package, not this directory name.

---

## Environment variables

| Variable | Used by | Description |
|----------|---------|-------------|
| `RECONVERT_APP_URL` | Python & JS Flow Builder tests | Full signed app URL (`shop` + `token`) |
| `RECONVERT_FLOW_PAGE` | Banner tests | Flow page name (default: `Thank you page`) |
| `SHOPIFY_EMAIL` | Login spec | Shopify account email |
| `SHOPIFY_PASSWORD` | Login spec | Shopify account password |
| `STORAGE_STATE` | Playwright JS | Path to saved auth JSON (skip login) |
| `HEADLESS` | Selenium | Set to `1` for headless Chrome |

Example:

```bash
export RECONVERT_APP_URL="https://app.reconvert.com/shopify/app/?shop=reconvert-automated-testing.myshopify.com&token=YOUR_TOKEN"
export RECONVERT_FLOW_PAGE="Thank you page"
```

Never commit real tokens or `.env` files.

---

## Running tests

### Playwright (JavaScript)

```bash
# All JS specs
npm test

# Headed (watch the browser)
npm run test:headed

# Interactive UI mode
npm run test:ui

# Shopify login + dashboard
npm run test:login

# Flow Builder — product page smoke
RECONVERT_APP_URL="<url>" npx playwright test tests/flow-builder-product-page.spec.js

# Product recommendations — product details block
RECONVERT_APP_URL="<url>" npx playwright test tests/flow-builder-product-recommendation-product-details.spec.js
```

### Playwright (Python) — Banner pre-made

```bash
source playwright_python_bdd/.venv/bin/activate
RECONVERT_APP_URL="<url>" pytest test_product_page.py -v          # headless
RECONVERT_APP_URL="<url>" pytest test_product_page.py -v --headed # watch it run
```

| Test | ID | Coverage |
|------|----|----------|
| `test_tc01_banner_premade_defaults` | TC-B01 | Default Banner configuration |
| `test_tc02_banner_status_options` | TC-B02 | All 5 status options |
| `test_tc03_banner_content` | TC-B03 | Title + rich-text description |
| `test_tc04_dismissible_and_collapsible_toggles` | TC-B04 | Toggle behaviour |
| `test_tc05_button_configuration_and_persistence` | TC-B05 | Button config + Save/persist |

### Selenium

```bash
cd selenium
source .venv/bin/activate

pytest test_banner_premade.py -v              # Banner status (Thank you page)
pytest test_product_banner.py -v              # Success/Error critical banner
HEADLESS=1 pytest test_product_banner.py -v   # headless
```

### pytest-bdd smoke

```bash
cd playwright_python_bdd
cp .env.example .env   # set RECONVERT_APP_URL
source .venv/bin/activate
pytest -v
```

---

## Test plan documentation

The master test plan includes 16 detailed test cases, smoke/regression checklists, and a defect logging template.

| File | Purpose |
|------|---------|
| [`docs/RECONVERT_TEST_PLAN.pdf`](docs/RECONVERT_TEST_PLAN.pdf) | Printable / shareable PDF |
| [`docs/RECONVERT_TEST_PLAN.html`](docs/RECONVERT_TEST_PLAN.html) | Editable HTML source |

**Regenerate PDF:**

```bash
node docs/generate-test-plan-pdf.cjs
```

Output is written to `docs/RECONVERT_TEST_PLAN.pdf` and copied to `~/Desktop/RECONVERT_TEST_PLAN.pdf`.

**Open in Google Docs:** Upload the PDF to Google Drive → right-click → **Open with → Google Docs**.

---

## Key implementation notes

### Embedded iframe

The Flow Builder renders inside:

```
iframe.embedded-screen-main-iframe
```

All widget controls (Banner status, product details, Save) must be accessed through this frame. Automation waits for **"Welcome to the Flow Builder!"** before interacting.

### Viewport

Use a desktop viewport (**1600 × 1000**) in automation. On narrower widths the settings panel collapses into a bottom sheet ("Swipe up to edit"), which breaks stable selectors.

### Chatbot panel

An AI assistant panel (`iframe.embedded-chatbot-panel`) can overlay the editor. Tests close it best-effort before clicking editor controls.

### Save / persistence

After clicking **Save**, wait ~3 seconds before reloading to verify persisted values.

---

## Page objects

### `ReConvertAppPage` (JavaScript)

Shared page object for Flow Builder navigation:

```js
import { ReConvertAppPage } from '../pages/ReConvertAppPage.js';

const app = new ReConvertAppPage(page);
await app.gotoDirect(process.env.RECONVERT_APP_URL);
await app.openFlowBuilder();
await app.selectProductPage();
await app.closeChatbot();
await app.openProductRecommendationsEditor();
```

### `BannerEditor` (Python)

Page object in `test_product_page.py` for the Banner pre-made widget on the Thank you page flow.

---

## Portfolio note

This project demonstrates:

- **Test planning** — master test plan with 16+ cases, smoke/regression checklists ([`docs/RECONVERT_TEST_PLAN.pdf`](docs/RECONVERT_TEST_PLAN.pdf))
- **Multi-stack automation** — Playwright (JS + Python), Selenium, pytest-bdd
- **Page Object Model** — reusable locators for iframe-heavy Shopify embedded apps
- **Real product complexity** — Flow Builder widgets, Save/persistence, cross-frame navigation

ReConvert and Upsell.com are trademarks of my former employer. This repo is for **demonstration purposes only** and is not affiliated with or maintained by ReConvert.

---

## Security

- **Do not commit** `.env`, JWT tokens, or signed app URLs to git.
- Tokens from a previous role may be **expired or revoked** — do not rely on URLs in old specs; obtain fresh credentials if you still have org access.
- Store any valid credentials only in local `.env` (see `.env.example`).
- The test plan PDF redacts tokens; always use `RECONVERT_APP_URL` at runtime.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| App shows blank / login fails | Token expired — generate a new signed URL |
| `Edit flows` not found | Wait longer (60s timeout); check network to `app.reconvert.com` |
| Settings panel missing | Widen viewport to 1600×1000; close chatbot panel |
| Selenium import error | Run pytest from `selenium/`, not project root |
| Iframe element not found | Switch to `iframe.embedded-screen-main-iframe` before locating widgets |
| Save persistence fails | Increase post-save wait; confirm Save button was enabled |

---

## Related links

- Former employer product: [Upsell.com / ReConvert](https://app.reconvert.com)
- [Playwright docs](https://playwright.dev)
- [pytest-playwright](https://playwright.dev/python/docs/test-runners#pytest)
