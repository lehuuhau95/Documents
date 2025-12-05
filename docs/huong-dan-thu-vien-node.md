Dưới đây là hướng dẫn nhanh + ví dụ để bạn có thể viết một tool tự động hóa tác vụ bằng Node.js. Mình sẽ minh họa với một tool chạy theo lịch (cron), đọc file, xử lý dữ liệu và gửi request API.

✅ 1. Khởi tạo project Node.js
mkdir automation-tool
cd automation-tool
npm init -y

✅ 2. Cài đặt các thư viện cần thiết
npm install axios node-cron fs-extra


axios → gọi API

node-cron → chạy theo lịch

fs-extra → đọc / ghi file tiện hơn

✅ 3. Ví dụ: Tool tự động chạy mỗi phút

automation.js

const cron = require("node-cron");
const axios = require("axios");
const fs = require("fs-extra");

// Hàm chính để xử lý tác vụ
async function runTask() {
    try {
        console.log("🔄 Đang chạy tác vụ...");

        // 1. Đọc dữ liệu từ file JSON
        let data = await fs.readJson("./data.json");

        // 2. Gọi API
        const res = await axios.get("https://api.publicapis.org/entries");

        // 3. Lưu kết quả mới
        data.lastRun = new Date().toISOString();
        data.apiResult = res.data.count;

        await fs.writeJson("./data.json", data, { spaces: 2 });

        console.log("✅ Hoàn thành tác vụ!");
    } catch (err) {
        console.error("❌ Lỗi:", err.message);
    }
}

// Lịch chạy: mỗi phút 1 lần
cron.schedule("* * * * *", () => {
    runTask();
});

✅ 4. Tạo file dữ liệu ban đầu

data.json

{
  "lastRun": "",
  "apiResult": 0
}

✅ 5. Chạy tool
node automation.js

🎯 Tool này có thể mở rộng để làm gì?

Bạn có thể bổ sung chức năng:

✔ Tự động gửi email
✔ Quét folder và xử lý file
✔ Tự động backup dữ liệu
✔ Tự động đọc Excel và import DB
✔ Tự động push dữ liệu lên API
✔ Tự động crawl web để lấy dữ liệu

Nếu bạn muốn, mình có thể viết bản hoàn chỉnh hơn theo yêu cầu của bạn:

👉 Bạn muốn tool tự động hóa tác vụ gì? (ví dụ: gửi mail, backup, crawl web, xử lý file, gọi API định kỳ,…)
