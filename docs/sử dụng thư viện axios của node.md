🧩 1. Axios là gì?

Axios là thư viện HTTP Client giúp bạn gửi các loại request như GET, POST, PUT, DELETE… từ Node.js hoặc Browser.

Axios nổi bật vì:

Dễ dùng hơn fetch (đặc biệt là trong Node.js)

Tự động chuyển JSON

Có interceptor (chèn token, log request…)

Có timeout, retry, cancel request

Hỗ trợ upload / download file

Hỗ trợ mặc định headers, baseURL

⚙️ 2. Cài đặt Axios
npm install axios

🚀 3. Cách dùng Axios cơ bản
📌 GET request
const axios = require('axios');

(async () => {
    const res = await axios.get('https://jsonplaceholder.typicode.com/posts');
    console.log(res.data);
})();

Giải thích:

res.data → dữ liệu trả về

res.status → mã status (200, 404…)

res.headers → headers

Axios tự động parse JSON cho bạn

📝 4. POST request
const res = await axios.post('https://httpbin.org/post', {
    username: "admin",
    password: "123456"
});

console.log(res.data);


Axios sẽ gửi JSON và tự đặt Content-Type: application/json.

🧰 5. Gửi kèm header
axios.get('https://api.example.com/users', {
    headers: {
        Authorization: "Bearer YOUR_TOKEN",
        "User-Agent": "Node-App"
    }
});

🎯 6. Base URL (cực hữu dụng)
const api = axios.create({
    baseURL: "https://api.example.com",
});

api.get('/users'); // tương đương https://api.example.com/users
api.post('/login', { user: "abc" });

⏱️ 7. Timeout
axios.get('https://api.example.com', {
    timeout: 5000 // 5 giây
});

🔁 8. Retry request (tự động gửi lại nếu lỗi)

Axios không có sẵn retry, nhưng dùng plugin:

npm install axios-retry

const axiosRetry = require('axios-retry');

axiosRetry(axios, { retries: 3 });

axios.get('https://example.com');

📦 9. Upload file bằng Axios
const fs = require('fs');

const form = new FormData();
form.append("file", fs.createReadStream("./img.png"));

await axios.post("https://example.com/upload", form, {
  headers: form.getHeaders(),
});

🧲 10. Download file
const fs = require('fs');

const res = await axios.get('https://example.com/image.png', {
    responseType: 'stream'
});

res.data.pipe(fs.createWriteStream('download.png'));

🌐 11. Query Params
axios.get('https://api.example.com/search', {
    params: {
        keyword: "iphone",
        page: 2
    }
});


Axios sẽ tạo URL:

.../search?keyword=iphone&page=2

🧱 12. Interceptor (cực quan trọng!)

Interceptors giúp chèn token, retry, logging…

🔐 Request Interceptor (chèn token)
axios.interceptors.request.use(config => {
    config.headers.Authorization = "Bearer TOKEN_ABC";
    console.log("Request:", config.method, config.url);
    return config;
});

📥 Response Interceptor
axios.interceptors.response.use(
    res => res,
    err => {
        console.log("Lỗi API:", err.message);
        return Promise.reject(err);
    }
);

🧪 13. Xử lý lỗi (try/catch)
try {
    const res = await axios.get("https://wrong-url.com");
} catch (err) {
    console.log("Message:", err.message);
    console.log("Status:", err.response?.status);
}

⚡ 14. Gửi nhiều request cùng lúc
const [posts, users] = await Promise.all([
    axios.get("https://jsonplaceholder.typicode.com/posts"),
    axios.get("https://jsonplaceholder.typicode.com/users")
]);

console.log(posts.data.length, users.data.length);

🔄 15. Axios + async/await + loop
const urls = [
    "https://example.com/a",
    "https://example.com/b",
    "https://example.com/c"
];

for (const url of urls) {
    const res = await axios.get(url);
    console.log(url, res.status);
}

📘 16. Axios với Cookies (session login)
const axiosCookieJarSupport = require('axios-cookiejar-support').default;
const tough = require('tough-cookie');

axiosCookieJarSupport(axios);

const jar = new tough.CookieJar();

const api = axios.create({
    jar,
    withCredentials: true
});

await api.post("https://example.com/login", {
    user: "admin", pass: "123"
});

// cookie được tự lưu trong jar
const res = await api.get("https://example.com/profile");
console.log(res.data);

🛠️ 17. Axios proxy
axios.get('https://api.example.com', {
    proxy: {
        host: '127.0.0.1',
        port: 8080
    }
});

🔥 18. Ví dụ hoàn chỉnh: API client
const axios = require('axios');

const api = axios.create({
    baseURL: "https://api.example.com",
    timeout: 5000,
});

// request interceptor
api.interceptors.request.use(config => {
    config.headers.Authorization = "Bearer TOKEN_123";
    return config;
});

// API wrapper
async function getUsers() {
    try {
        const res = await api.get('/users');
        return res.data;
    } catch (err) {
        console.error("API error:", err.response?.status);
    }
}

(async () => {
    const users = await getUsers();
    console.log(users);
})();

🎯 19. Khi nào nên dùng Axios?
Tình huống	Có nên dùng Axios?
Gửi API JSON	✔ Tuyệt vời
Upload file	✔ Rất phù hợp
Xử lý cookie	✔ Tốt
Interceptor / Token	✔ Xuất sắc
Tải file lớn	✔ Có stream
📌 Bạn muốn mình viết ví dụ thực tế nào?

Mình có thể viết mẫu:

Axios + Node.js API client

Axios crawl dữ liệu từ website

Axios login + lưu cookie

Axios với proxy rotation

Axios trong Express server
