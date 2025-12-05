🕒 1. node-cron là gì?

node-cron là thư viện giúp bạn tạo cron job ngay trong Node.js mà không cần cài đặt cron của hệ điều hành.

Bạn có thể dùng node-cron để:

Gửi email tự động theo lịch

Chạy job dọn dẹp database mỗi ngày

Crawl dữ liệu mỗi giờ

Đồng bộ dữ liệu định kỳ

Tự động backup

⚙️ 2. Cài đặt node-cron
npm install node-cron

📌 3. Cú pháp Cron (rất quan trọng)

node-cron sử dụng đúng cú pháp cron chuẩn:

* * * * * *
│ │ │ │ │ └── Thứ (0-7, 0 hoặc 7 = Chủ Nhật)
│ │ │ │ └──── Tháng (1-12)
│ │ │ └────── Ngày (1-31)
│ │ └──────── Giờ (0-23)
│ └────────── Phút (0-59)
└──────────── Giây (0-59)


👉 node-cron hỗ trợ 6 trường, bao gồm giây (cron Linux chỉ có 5 trường).

Ví dụ:

Cron	Ý nghĩa
* * * * * *	mỗi giây
0 * * * * *	mỗi phút tại giây 0
0 0 * * * *	mỗi giờ
0 0 7 * * *	mỗi ngày lúc 7:00
0 0 0 * * 1	mỗi thứ 2 lúc 00:00
0 */10 * * * *	mỗi 10 phút
0 30 14 * * *	14:30 hằng ngày
0 0 */2 * * *	mỗi 2 giờ
🚀 4. Ví dụ cơ bản: tạo cron chạy mỗi phút
const cron = require('node-cron');

cron.schedule('* * * * *', () => {
  console.log('Job chạy mỗi phút!', new Date());
});

Giải thích

schedule() → tạo job

'* * * * *' → cron rule chỉ có 5 trường (không có giây) → mặc định giây = 0

🕹️ 5. Ví dụ: chạy mỗi giây
cron.schedule('* * * * * *', () => {
  console.log('Job chạy mỗi giây');
});

📅 6. Chạy job mỗi ngày vào giờ cụ thể
cron.schedule('0 30 8 * * *', () => {
  console.log('Chào buổi sáng! 8:30 hằng ngày');
});

🌐 7. Cron chạy theo múi giờ

Mặc định node-cron dùng thời gian server. Bạn có thể đặt timezone:

cron.schedule('0 0 7 * * *', () => {
  console.log('Chạy theo giờ VN', new Date());
}, {
  timezone: "Asia/Ho_Chi_Minh"
});

⏸️ 8. Dừng / khởi động lại cron job

Tạo job:

const task = cron.schedule('* * * * * *', () => {
  console.log('job chạy...');
});


Dừng:

task.stop();


Khởi động lại:

task.start();

🔧 9. Job chỉ chạy 1 lần (cron + timeout)
const task = cron.schedule('* * * * * *', () => {
  console.log('Chạy 1 lần rồi stop');
  task.stop(); 
});

📥 10. Truyền tham số vào job
function crawlSite(url) {
  console.log("Crawl:", url);
}

cron.schedule('0 */5 * * * *', () => {
  crawlSite("https://example.com");
});

🔄 11. Cron chạy song song nhiều job
cron.schedule('* * * * *', () => console.log('Job 1'));
cron.schedule('*/10 * * * * *', () => console.log('Job 2'));
cron.schedule('0 0 12 * * *', () => console.log('Job 3'));

🎯 12. Cron nâng cao: chạy theo pattern
a) Mỗi 10 phút
'0 */10 * * * *'

b) Mỗi thứ 2, 4, 6 lúc 21:00
'0 0 21 * * 1,3,5'

c) Tháng 1 và tháng 6 lúc 0 giờ
'0 0 0 * 1,6 *'

d) Mỗi phút nhưng chỉ trong khoảng 9h–17h
'0 * 9-17 * * *'

🧪 13. Kiểm tra Cron có hợp lệ không
cron.validate('0 0 7 * * *');      // true
cron.validate('something wrong');  // false

📂 14. Cron kết hợp async/await
cron.schedule('0 * * * * *', async () => {
  try {
    const res = await fetch('https://api.example.com/data');
    const data = await res.json();
    console.log('Dữ liệu:', data);
  } catch (err) {
    console.error('Lỗi:', err);
  }
});

💾 15. Cron + Database ví dụ
cron.schedule('0 */30 * * * *', async () => {
  console.log("Dọn dẹp dữ liệu cũ...");

  await db.collection('logs').deleteMany({
    createdAt: { $lt: Date.now() - 7*24*60*60*1000 } // 7 ngày
  });

  console.log("Xong!");
});

🛡️ 16. Lưu ý quan trọng
❗ Node-cron sẽ dừng khi:

Server Node.js restart

Container Docker restart

Server sleep (hosting free)

Máy bạn shutdown

👉 Nếu bạn cần cron không bao giờ dừng, dùng:

PM2 + cron

Docker container với restart policy

Server cron (Linux crontab)

BullMQ + Redis (job queue mạnh mẽ)

📘 17. Ví dụ hoàn chỉnh
const cron = require('node-cron');

// Job chạy mỗi 30 giây
const task = cron.schedule('0,30 * * * * *', () => {
  console.log('Chạy lúc:', new Date().toLocaleTimeString());
}, {
  scheduled: true,
  timezone: "Asia/Ho_Chi_Minh",
});

// dừng sau 2 phút
setTimeout(() => {
  console.log('Dừng job');
  task.stop();
}, 120000);

✅ Bạn muốn mình viết cho bạn dự án mẫu thực tế không?

Mình có thể làm mẫu:

📌 Cron gửi email mỗi ngày
📌 Cron tự động crawl Shopee / Tiki
📌 Cron tự động backup file
📌 Cron kiểm tra server sống hay không
📌 Cron chạy trong Docker + PM2
