⭐ PLAYWRIGHT LÀ GÌ?

Playwright là thư viện automation do Microsoft phát triển, dùng để:

✔ Tự động hóa trình duyệt (Chrome, Firefox, Edge, WebKit/Safari)
✔ Điền form, click, thao tác website
✔ Lấy dữ liệu (web crawling / scraping)
✔ Test UI
✔ Tạo bot biết tương tác với web

Đặc điểm mạnh nhất:

Auto-wait (tự chờ element sẵn sàng → ít lỗi timeout)

Đa trình duyệt (Chrome + Firefox + Safari)

Tắt hiện cửa sổ / chạy server (headless mode)

Rất khó bị phát hiện là bot (anti-detection tốt hơn Puppeteer)

API đơn giản, hiện đại

⭐ CÀI ĐẶT PLAYWRIGHT
1) Cài qua npm
npm install playwright

2) Cài browser engine
npx playwright install

3) (Khuyên dùng) Cài Playwright với trình duyệt thêm
npm init playwright@latest

⭐ CẤU TRÚC CƠ BẢN CỦA PLAYWRIGHT

Playwright có 3 đối tượng chính:

Browser   → Tạo ra BrowserContext → Tạo ra Page → Thao tác web

1) Browser

Trình duyệt thật (Chromium, Firefox, WebKit)

2) BrowserContext

Phiên làm việc độc lập (giống mỗi tab là 1 trình duyệt riêng)

3) Page

Một tab duy nhất để thao tác:

goto()

click()

fill()

content()

evaluate() (run JS trong web)

⭐ CODE PLAYWRIGHT CƠ BẢN
Mở web + click + nhập dữ liệu
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({ headless: false }); // false để thấy trình duyệt
  const page = await browser.newPage();

  await page.goto('https://google.com');

  await page.fill('input[name="q"]', 'playwright');
  await page.press('input[name="q"]', 'Enter');

  await page.waitForTimeout(3000); // đợi nhìn kết quả
  await browser.close();
})();

⭐ PLAYWRIGHT SUPPORT SELECTORS (CHỌN ELEMENT)

Playwright hỗ trợ nhiều kiểu chọn:

1) CSS selector
page.click('#loginBtn');
page.fill('.form input[name=email]', 'abc@gmail.com');

2) Text selector
page.click('text="Đăng nhập"');

3) XPath
page.click('//button[contains(text(),"Đăng nhập")]');

4) Role-based (chuẩn WCAG rất ổn định)
await page.getByRole('button', { name: 'Submit' }).click();

⭐ LẤY DỮ LIỆU (WEB SCRAPING)
Lấy text
const title = await page.textContent('h1');
console.log(title);

Lấy danh sách phần tử
const items = await page.$$eval('.product-title', els =>
  els.map(e => e.textContent.trim())
);

console.log(items);

Lấy HTML
const html = await page.content();

⭐ CHẠY CHẾ ĐỘ HEADLESS (KHÔNG HIỆN TRÌNH DUYỆT)
const browser = await chromium.launch({ headless: true });

⭐ CHỐNG BỊ WEBSITE PHÁT HIỆN BOT
1) Sử dụng stealth mode

Playwright tự động tốt hơn Puppeteer → ít cần plugin.

2) Random user-agent
await page.setExtraHTTPHeaders({
  'user-agent': 'Mozilla/5.0 ...'
});

3) Dùng browser context riêng (k tránh cookie)
const context = await browser.newContext({ userAgent: 'Mozilla...' });

4) Bật “realistic viewport”
await context.newPage({ viewport: { width: 1280, height: 720 } });

⭐ ĐIỀN FORM
await page.fill('#email', 'test@gmail.com');
await page.fill('#password', '123456');
await page.click('button[type=submit]');

⭐ TẢI FILE (DOWNLOAD)
const [ download ] = await Promise.all([
  page.waitForEvent('download'),
  page.click('#downloadButton')
]);

const path = await download.path();
console.log('Downloaded to:', path);

⭐ UPLOAD FILE
await page.setInputFiles('input[type=file]', './myfile.pdf');

⭐ CHỤP ẢNH TRANG WEB
await page.screenshot({ path: 'screenshot.png', fullPage: true });

⭐ GIẢM LỖI TIMEOUT (TỰ CHỜ)

Playwright hỗ trợ auto-wait:

chờ element tồn tại

chờ element có thể click

chờ page load

chờ navigation

Không cần viết thêm .waitForSelector() nhiều.

⭐ ĐỢI CÁC SỰ KIỆN ĐẶC BIỆT
Đợi navigation
await Promise.all([
  page.waitForNavigation(),
  page.click('button.submit')
]);

Đợi selector
await page.waitForSelector('#result');

⭐ CHẠY NHIỀU TRÌNH DUYỆT SONG SONG (CONCURRENT)
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch();

  await Promise.all([
    (async () => {
      const page = await browser.newPage();
      await page.goto('https://google.com');
    })(),

    (async () => {
      const page = await browser.newPage();
      await page.goto('https://bing.com');
    })()
  ]);

  await browser.close();
})();

⭐ DÙNG PLAYWRIGHT CHO TEST (PRO)

Playwright có framework test riêng:

npm init playwright@latest


File test:

import { test, expect } from '@playwright/test';

test('basic test', async ({ page }) => {
  await page.goto('https://example.com');
  await expect(page).toHaveTitle(/Example/);
});

⭐ BEST PRACTICE (CHUẨN CÔNG TY)
✔ Không dùng waitForTimeout trừ khi cần debug
✔ Sử dụng context riêng để tránh rò rỉ cookie/session
✔ Tách code thành "actions" và "selectors"
✔ Luôn wrap task trong try/catch
✔ Log lại toàn bộ step khi chạy server
📌 TỔNG KẾT NGẮN GỌN
Khả năng	Playwright
Automation web	⭐⭐⭐⭐⭐
Web scraping	⭐⭐⭐⭐⭐
Chống detection	⭐⭐⭐⭐
Độ ổn định	⭐⭐⭐⭐⭐
Test UI	⭐⭐⭐⭐⭐

👉 Playwright = lựa chọn tốt nhất năm 2025 để tự động hoá web bằng Node.js
