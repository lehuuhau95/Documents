🧩 Puppeteer là gì?

Puppeteer là thư viện Node.js do Google phát triển, cho phép bạn điều khiển trình duyệt Chrome/Chromium bằng mã JavaScript thông qua DevTools Protocol.

Bạn có thể:

Tự động hoá thao tác web (auto login, click, nhập liệu…)

Crawl / scrape dữ liệu web

Screenshot / PDF trang web

Kiểm thử UI tự động

Render trang (SSR), SEO, kiểm tra cấu trúc trang…

Puppeteer giống như Selenium nhưng nhanh hơn, ổn định hơn, và dễ dùng hơn vì chạy trực tiếp trên Chrome không qua WebDriver.

⚙️ 1. Cài đặt Puppeteer

Chạy lệnh:

npm install puppeteer


Mặc định Puppeteer sẽ tải Chromium phiên bản tương thích với API của nó.

Nếu bạn muốn dùng Chrome đã cài sẵn:

npm install puppeteer
PUPPETEER_EXECUTABLE_PATH="/path/to/chrome"

🚀 2. Chạy Puppeteer cơ bản

Ví dụ đầu tiên: mở trình duyệt → truy cập Google → chụp ảnh màn hình.

const puppeteer = require('puppeteer');

(async () => {
    const browser = await puppeteer.launch({ headless: true });
    const page = await browser.newPage();

    await page.goto('https://google.com');

    await page.screenshot({ path: 'google.png' });

    await browser.close();
})();

Giải thích:

launch() → mở trình duyệt

newPage() → tạo tab mới

goto(url) → truy cập URL

screenshot() → chụp ảnh màn hình

close() → đóng trình duyệt

👀 3. Tùy chọn khi mở trình duyệt
const browser = await puppeteer.launch({
  headless: false,    // bật giao diện cho thấy trình duyệt thật chạy
  slowMo: 50,          // làm chậm thao tác để dễ quan sát
  defaultViewport: null, // fullscreen
});


Các option hay dùng:

Option	Công dụng
headless: false	Mở trình duyệt có giao diện
slowMo: X	Làm chậm thao tác X ms
args: [...]	Truyền thêm tham số cho Chrome
executablePath	Dùng Chrome thật thay vì Chromium
📝 4. Nhập liệu & Click

Ví dụ: điền vào form và click nút.

await page.type('#username', 'myUserName');
await page.type('#password', 'myPassword');
await page.click('#login-btn');

await page.waitForNavigation();

Giải thích:

type(selector, text) → nhập text vào input

click(selector) → click phần tử

waitForNavigation() → đợi trang tải xong sau khi click

🔍 5. Lấy dữ liệu (Scraping)

Ví dụ lấy danh sách tiêu đề từ trang tin tức:

const titles = await page.$$eval('.news-title', els =>
    els.map(el => el.innerText)
);

console.log(titles);

Giải thích:

$$eval(selector, callback)
→ Lấy tất cả các phần tử phù hợp selector rồi xử lý trong callback (chạy trong môi trường trình duyệt)

📄 6. Lấy HTML / InnerHTML / InnerText
const html = await page.content(); // toàn bộ HTML
const text = await page.$eval('.item', el => el.innerText);
const inner = await page.$eval('.item', el => el.innerHTML);

🖼️ 7. Chụp ảnh màn hình / tạo PDF
Screenshot toàn trang:
await page.screenshot({ path: 'full.png', fullPage: true });

PDF:
await page.pdf({
  path: 'page.pdf',
  format: 'A4'
});

⏳ 8. Chờ phần tử xuất hiện

Puppeteer nhanh hơn server nhiều, nên phải chờ phần tử load.

await page.waitForSelector('.item-loaded');


Hoặc đợi thời gian tùy ý:

await page.waitForTimeout(2000); // 2s

🌐 9. Điều khiển chuột & bàn phím nâng cao
await page.mouse.move(100, 200);
await page.mouse.click(100, 200);

await page.keyboard.press('Enter');
await page.keyboard.down('Shift');

🧭 10. Xử lý popup / tab mới
const [newPage] = await Promise.all([
  browser.waitForTarget(target => target.opener() === page.target()),
  page.click('#open-new-tab')
]);

const popup = await newPage.page();

📦 11. Đăng nhập giữ cookie / session
await page.goto('https://example.com/login');

await page.type('#user', 'admin');
await page.type('#pass', '123456');
await page.click('#login');

await page.waitForNavigation();

// lưu cookie
const cookies = await page.cookies();
console.log(cookies);

🛠️ 12. Chống Google reCAPTCHA / Cloudflare

Puppeteer bị phát hiện nếu không giả lập người dùng thật.
Dùng các trick:

await page.setUserAgent(
  'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120 Safari/537.36'
);

await page.evaluate(() => {
  Object.defineProperty(navigator, 'webdriver', { get: () => false });
});


Hoặc dùng package:

npm install puppeteer-extra puppeteer-extra-plugin-stealth

🧩 13. Ví dụ đầy đủ

Đăng nhập → lấy dữ liệu → lưu ảnh:

const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({ headless: false });
  const page = await browser.newPage();

  await page.goto('https://example.com/login');

  await page.type('#username', 'admin');
  await page.type('#password', '123456');

  await Promise.all([
    page.click('#login'),
    page.waitForNavigation(),
  ]);

  const data = await page.$eval('.user-data', el => el.innerText);
  console.log(data);

  await page.screenshot({ path: 'dashboard.png' });

  await browser.close();
})();

🎯 14. Khi nào nên dùng Puppeteer?

Dùng Puppeteer nếu bạn cần:

Crawl dữ liệu cần render JS

Lấy dữ liệu từ SPA (React, Vue, Angular)

Test UI tự động

Tạo screenshot / PDF report

Điều khiển trình duyệt tự động

Không nên dùng nếu:

Crawl lượng cực lớn (→ dùng Playwright hoặc headless API thấp hơn)

Trang có API JSON sẵn (→ fetch API nhanh hơn)

📘 15. Bạn muốn mình tạo ví dụ nâng cao hơn không?

Mình có thể viết cho bạn:

✅ Bot đăng nhập Facebook
✅ Auto crawl SHOPEE / TIKI
✅ Bypass Cloudflare
✅ Render website chụp screenshot hàng loạt
✅ Puppeteer + Proxy + User-Agent rotation
✅ Video bot (Puppeteer Recorder)



----------------------------------------------------------------------------------------------
Trong Puppeteer, bạn dùng page.$eval hay page.$$eval tùy theo bạn muốn thao tác một element hay nhiều element.

✅ 1. page.$eval — dùng khi bạn chỉ cần một element

Chỉ query một phần tử đầu tiên khớp với selector (giống document.querySelector)

Truyền vào callback để thao tác trên element đó

Trả về kết quả do callback trả ra

Ví dụ:
const title = await page.$eval('h1', el => el.textContent);
console.log(title);


👉 Dùng khi bạn chắc chắn selector có 1 phần tử hoặc bạn chỉ cần phần tử đầu tiên.

✅ 2. page.$$eval — dùng khi bạn muốn nhiều element

Query tất cả phần tử khớp selector (giống document.querySelectorAll)

Callback nhận một mảng element

Trả về kết quả do callback trả ra

Ví dụ:
const items = await page.$$eval('.product', els =>
  els.map(el => el.textContent.trim())
);
console.log(items);


👉 Dùng khi selector trả về nhiều phần tử và bạn muốn xử lý chúng (lấy danh sách, duyệt từng item…)
