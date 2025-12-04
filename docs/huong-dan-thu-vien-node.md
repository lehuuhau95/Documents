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

Bạn đã nói:
hướng dẫn tôi nodejs connect sqlserver đi, ch tiết nha
ChatGPT đã nói:

Dưới đây là hướng dẫn đầy đủ – chi tiết – chạy được ngay để bạn kết nối Node.js với SQL Server (MSSQL) bằng thư viện mssql.

✅ 1. Cài đặt môi trường
Yêu cầu:

Node.js (>= 14)

SQL Server đã chạy (local hoặc remote)

Tài khoản đăng nhập SQL (SQL Authentication hoặc Windows Auth)

✅ 2. Cài thư viện kết nối SQL Server

Trong project Node.js:

npm install mssql

✅ 3. Tạo file cấu hình kết nối
➤ config/db.js
const sql = require('mssql');

const config = {
    user: 'sa',              // Username SQL Server
    password: 'your_password', // Password
    database: 'TestDB',      // Tên database
    server: 'localhost',     // IP hoặc domain server SQL
    port: 1433,              // Port mặc định của SQL Server
    options: {
        encrypt: false,      // Nếu dùng local thì để false
        trustServerCertificate: true // Bật cho phép SSL tự động
    }
};

module.exports = config;


🔥 Lưu ý: Nếu bạn dùng SQL Server Express → đổi server: 'localhost\\SQLEXPRESS'.

✅ 4. Tạo file kết nối và chạy thử truy vấn
➤ test-connection.js
const sql = require('mssql');
const config = require('./config/db');

async function testConnection() {
    try {
        console.log("🔌 Đang kết nối tới SQL Server...");

        let pool = await sql.connect(config);

        console.log("✅ Kết nối thành công!");

        // Chạy truy vấn thử
        let result = await pool.request().query("SELECT TOP 10 * FROM Users");

        console.log("📌 Dữ liệu:");
        console.table(result.recordset);

        sql.close();
    } catch (err) {
        console.error("❌ Lỗi kết nối:", err.message);
    }
}

testConnection();

✅ 5. Chạy thử
node test-connection.js


Nếu đúng, bạn sẽ thấy:

🔌 Đang kết nối tới SQL Server...
✅ Kết nối thành công!
📌 Dữ liệu:
┌─────────┬──────────────┬───────────────┬───────────┐
│ (index) │    Id        │    Name       │   Email   │
└─────────┴──────────────┴───────────────┴───────────┘

🚀 6. Hướng dẫn thực hiện CRUD đầy đủ
➤ INSERT
let result = await pool.request()
    .input('name', sql.NVarChar, 'Nguyễn Văn A')
    .input('email', sql.NVarChar, 'vana@example.com')
    .query("INSERT INTO Users (Name, Email) VALUES (@name, @email)");

➤ SELECT có điều kiện
let result = await pool.request()
    .input('id', sql.Int, 5)
    .query("SELECT * FROM Users WHERE Id = @id");

➤ UPDATE
let result = await pool.request()
    .input('id', sql.Int, 5)
    .input('name', sql.NVarChar, "Tên Mới")
    .query("UPDATE Users SET Name = @name WHERE Id = @id");

➤ DELETE
let result = await pool.request()
    .input('id', sql.Int, 5)
    .query("DELETE FROM Users WHERE Id = @id");

🔥 7. Tạo module SQL để tái sử dụng
➤ services/db.js
const sql = require('mssql');
const config = require('../config/db');

const poolPromise = new sql.ConnectionPool(config)
    .connect()
    .then(pool => {
        console.log("⚡ SQL Server Connected");
        return pool;
    })
    .catch(err => console.log("❌ SQL Connection Failed: ", err));

module.exports = {
    sql, poolPromise
};

➤ cách dùng trong file khác:
const { sql, poolPromise } = require('./services/db');

async function getUsers() {
    const pool = await poolPromise;
    const result = await pool.request().query("SELECT * FROM Users");
    return result.recordset;
}

📌 8. Lỗi thường gặp & cách fix
❌ Error: Login failed for user

✔ Sai user/password
✔ Database không cho phép SQL Authentication
👉 Mở SQL Management → Properties → Security → enable SQL Server and Windows Authentication mode

❌ ConnectionError: Failed to connect localhost:1433

✔ SQL chưa mở port
✔ SQL Browser chưa bật
✔ Firewall chặn 1433

👉 Mở SQL Server Configuration Manager → bật:

TCP/IP

SQL Server Browser

❌ TypeError: sql.connect is not a function

✔ Bạn import sai:

const sql = require('mssql'); // ĐÚNG

🎉 Bạn muốn tôi viết thêm phần nào?

Tôi có thể hỗ trợ:

✔ Kết nối bằng Express API
✔ Kết nối bằng Sequelize + MSSQL
✔ Tool tự động backup SQL bằng Node.js
✔ Tạo RESTful API CRUD đầy đủ

👉 Bạn cần phần nào?

Bạn đã nói:
tôi muốn chạy câu lệnh query, gọi stored có truyền param
ChatGPT đã nói:

Ok, dưới đây là hướng dẫn chi tiết – rõ ràng – đầy đủ ví dụ để bạn chạy:

Query thường có truyền tham số

Gọi Stored Procedure có truyền tham số (input + output)

Tất cả dùng thư viện mssql.

✅ 1. Query SQL có truyền tham số

Ví dụ:

SELECT * FROM Users WHERE Id = @id AND Name LIKE @name

➤ Node.js:
const sql = require("mssql");
const config = require("./config/db");

async function runQuery() {
    try {
        const pool = await sql.connect(config);

        const result = await pool.request()
            .input("id", sql.Int, 10)
            .input("name", sql.NVarChar, "%an%")
            .query("SELECT * FROM Users WHERE Id = @id AND Name LIKE @name");

        console.log(result.recordset);
    } catch (err) {
        console.error("❌ Lỗi:", err);
    }
}

runQuery();

✅ 2. Gọi Stored Procedure có truyền tham số

Giả sử bạn có SP:

CREATE PROCEDURE GetUserById
    @UserId INT
AS
BEGIN
    SELECT * FROM Users WHERE Id = @UserId;
END

➤ Node.js gọi Stored Procedure:
const sql = require("mssql");
const config = require("./config/db");

async function callStored() {
    try {
        const pool = await sql.connect(config);

        const result = await pool.request()
            .input("UserId", sql.Int, 5)
            .execute("GetUserById");

        console.log(result.recordset);
    } catch (err) {
        console.error("❌ Lỗi:", err);
    }
}

callStored();

✅ 3. Gọi Stored Procedure có Output Parameter

SQL Stored Procedure:

CREATE PROCEDURE CountUsersByRole
    @RoleId INT,
    @Total INT OUTPUT
AS
BEGIN
    SELECT @Total = COUNT(*) FROM Users WHERE RoleId = @RoleId;
END

➤ Node.js xử lý cả input + output:
const sql = require("mssql");
const config = require("./config/db");

async function callStoredWithOutput() {
    try {
        const pool = await sql.connect(config);

        const request = pool.request();

        request.input("RoleId", sql.Int, 2);
        request.output("Total", sql.Int);

        const result = await request.execute("CountUsersByRole");

        console.log("Tổng số user:", result.output.Total);
    } catch (err) {
        console.error("❌ Lỗi:", err);
    }
}

callStoredWithOutput();

✅ 4. Gọi Stored Procedure với nhiều kiểu dữ liệu

Ví dụ SP:

CREATE PROCEDURE InsertUser
    @Name NVARCHAR(50),
    @Email NVARCHAR(100),
    @Age INT,
    @IsActive BIT
AS
BEGIN
    INSERT INTO Users (Name, Email, Age, IsActive)
    VALUES (@Name, @Email, @Age, @IsActive);
END

➤ Node.js:
await pool.request()
    .input("Name", sql.NVarChar, "Nguyễn Văn Nam")
    .input("Email", sql.NVarChar, "nam@example.com")
    .input("Age", sql.Int, 30)
    .input("IsActive", sql.Bit, true)
    .execute("InsertUser");

📌 TÓM LẠI – Bạn có 3 kiểu gọi:
Tác vụ	Cách
Query có tham số	.query() + .input()
Gọi Stored Procedure	.execute("ProcName")
Stored có OUTPUT	.input() + .output()
🚀 Bạn muốn tôi viết thêm phần nào?

Tạo API Express để gọi SP

Xử lý transaction (BEGIN – COMMIT – ROLLBACK)

Tạo repository chuẩn clean code cho Node + MSSQL

👉 Bạn muốn tiếp tục với phần nào?

Bạn có thích tính cách này không
