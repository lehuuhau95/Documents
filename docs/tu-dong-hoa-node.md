Dưới đây là hướng dẫn chuẩn + code mẫu hoàn chỉnh để bạn tự động hóa toàn bộ quy trình:

đăng nhập hệ thống

nhấn button A

đợi table load

đọc file dữ liệu (Excel/CSV)

so sánh từng dòng trong table với dữ liệu trong file

tick vào checkbox của dòng đúng

nhấn nút Add

👉 Giải pháp tối ưu nhất: dùng Puppeteer (điều khiển Chrome).

🟩 1. Cài thư viện cần thiết
npm install puppeteer xlsx fs-extra


puppeteer → tự động hoá web (click, nhập text, đọc table…)

xlsx → đọc file Excel

fs-extra → hỗ trợ xử lý file

🟦 2. Code tự động hóa full workflow

👉 Bạn chỉ cần đổi lại URL, selector, tên cột theo hệ thống của bạn.

📂 auto.js
const puppeteer = require("puppeteer");
const XLSX = require("xlsx");
const fs = require("fs-extra");

async function runAutomation() {
    //----------------------------------
    // 1. Đọc file Excel
    //----------------------------------
    const workbook = XLSX.readFile("data.xlsx");
    const sheet = workbook.Sheets[workbook.SheetNames[0]];
    const excelData = XLSX.utils.sheet_to_json(sheet);

    // excelData = [{ Name: "...", CCCD: "..." }, ... ]
    console.log("📄 Đọc dữ liệu file:", excelData.length, "dòng");

    //----------------------------------
    // 2. Mở trình duyệt + load trang
    //----------------------------------
    const browser = await puppeteer.launch({
        headless: false,          // false để xem trình duyệt chạy
        defaultViewport: null,
        args: ['--start-maximized']
    });

    const page = await browser.newPage();
    await page.goto("https://your-system-url.com/login", { waitUntil: "networkidle2" });

    //----------------------------------
    // 3. Đăng nhập
    //----------------------------------
    await page.type("#username", "yourUser");
    await page.type("#password", "yourPass");

    await Promise.all([
        page.click("#btnLogin"),
        page.waitForNavigation({ waitUntil: "networkidle2" })
    ]);

    console.log("🔐 Đăng nhập thành công");

    //----------------------------------
    // 4. Nhấn button A
    //----------------------------------
    await page.click("#btnA");

    //----------------------------------
    // 5. Chờ table load xong
    //----------------------------------
    await page.waitForSelector("#dataTable tbody tr");
    console.log("📊 Table đã load");

    //----------------------------------
    // 6. Lấy dữ liệu dòng trong table
    //----------------------------------
    // Lấy index của các dòng đúng để tick checkbox
    const rows = await page.$$eval("#dataTable tbody tr", (trs) => {
        return trs.map(tr => {
            const tds = tr.querySelectorAll("td");
            return {
                name: tds[1]?.innerText.trim(),     // cột Name
                cccd: tds[2]?.innerText.trim(),     // cột CCCD
            };
        });
    });

    console.log("📄 Số dòng trong table:", rows.length);

    //----------------------------------
    // 7. So sánh với file và tick checkbox
    //----------------------------------
    for (let i = 0; i < rows.length; i++) {
        const row = rows[i];

        // kiểm tra có trong file Excel không
        const match = excelData.find(
            x => x.Name === row.name && x.CCCD === row.cccd
        );

        if (match) {
            console.log(`✔ Match: ${row.name} | ${row.cccd}`);

            // tick vào checkbox tương ứng hàng i
            const checkboxSelector = `#dataTable tbody tr:nth-child(${i + 1}) input[type='checkbox']`;
            await page.click(checkboxSelector);
        }
    }

    //----------------------------------
    // 8. Nhấn nút Add
    //----------------------------------
    await page.click("#btnAdd");

    console.log("🎉 Đã xử lý xong toàn bộ — OK!");
}

runAutomation();

🟧 3. File Excel mẫu (data.xlsx)

Cột phải khớp giá trị bạn so sánh:

Name	CCCD
Nguyễn Văn A	123456789
Trần Thị B	987654321
🟥 4. Bạn cần chỉnh sửa 4 phần chính:
1. Selector login
"#username", "#password", "#btnLogin"

2. Selector button A
"#btnA"

3. Selector table
"#dataTable tbody tr"

4. Selector checkbox trong row
`#dataTable tbody tr:nth-child(${i + 1}) input[type='checkbox']`


Nếu bạn gửi HTML table hoặc hệ thống của bạn, mình sửa luôn selector cho đúng.

🟩 5. Bạn cần mở rộng không?

Mình có thể viết thêm:

🔸 thêm retry nếu page load chậm
🔸 log ra file
🔸 xuất báo cáo Excel
🔸 ghi screenshot từng bước
🔸 chặn popup / captcha
🔸 chạy auto mỗi ngày bằng node-cron
🔸 chạy ẩn (headless: true)
