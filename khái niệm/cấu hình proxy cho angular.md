# Cấu hình Proxy cho Angular

Bài viết này giới thiệu cách cấu hình proxy cho Angular dựa trên Angular CLI
- Tài liệu chính thức: https://angular.io/guide/build#using-corporate-proxy
- Github: https://github.com/angular/angular-cli/blob/master/docs/documentation/stories/proxy.md

## Tại sao cần làm điều này?
Viết một file proxy để chuyển (proxy) các request khớp sang một địa chỉ khác, giải quyết vấn đề CORS trong phát triển cục bộ.

## Cách cấu hình
- Tạo file `proxy.config.js` trong thư mục gốc của dự án.
- Cấu hình nội dung trong file đó.
- Thêm tùy chọn `--proxy-config proxy.config.js` vào lệnh chạy dự án trong `package.json` hoặc khi chạy `ng serve`.

Ví dụ cấu hình:
```js
const PROXY_CONFIG = [
  {
    context: ['/api'],
    target: 'http://xxx',
    secure: false,
    changeOrigin: true,
    pathRewrite: {
      '^/api': '',
    },
  },
];
module.exports = PROXY_CONFIG;
```

Chú giải:
- `context`: các đường dẫn (path) cần khớp để áp dụng proxy
- `target`: địa chỉ đích sẽ proxy tới
- `pathRewrite`: viết lại phần path của request; là một object, key là `^+path cần viết lại`, value là path thay thế
- `secure`: cấu hình bảo mật (ví dụ với https tự ký)
- `changeOrigin`: thay đổi origin header của request khi proxy

## Ví dụ cấu hình cụ thể
Giả sử request cục bộ:
```
http://localhost:4208/auth/login
```
muốn proxy tới:
```
http://www.abc.com/news/login
```
cấu hình có thể là:
```js
const PROXY_CONFIG = [
  {
    context: ['/auth/login'],
    target: 'http://www.abc.com',
    pathRewrite: {
        '^/auth/login': '/news/login',
    },
  },
];

module.exports = PROXY_CONFIG;
```

### Hỏi: Nếu có hai API, một là `api/v1/1`, và một là `api/v1/cer/login`, làm sao để proxy chúng tới hai địa chỉ khác nhau?
Bạn có thể viết hai mục cấu hình; Angular CLI sẽ kiểm tra theo thứ tự và chọn mục khớp nhất (mục cụ thể hơn nên đặt trước mục tổng quát):
```js
{
  context: ['/api/v1/cer/login'],
  target: 'http://xxx1',
  secure: false,
  changeOrigin: true,
},
{
  context: ['/api'],
  target: 'http://xxx2',
  secure: false,
},
```
Khi dùng `/api` như context chung, mọi đường dẫn khớp `/api` sẽ đi qua target `xxx2`. Nhưng nếu có một entry chi tiết hơn như `/api/v1/cer/login` đặt trước, request tới `/api/v1/cer/login` sẽ khớp entry chi tiết hơn và được proxy tới `xxx1`.

## Ghi chú thêm
- Đặt các rule cụ thể (chi tiết) trước các rule tổng quát để tránh bị ghi đè.
- Kiểm tra kỹ `pathRewrite` nếu bạn cần giữ, xóa hoặc chuyển đổi một phần path khi proxy.
- Khi proxy tới HTTPS tự ký (self-signed), có thể cần `secure: false`.
- `changeOrigin: true` thường hữu ích khi server đích kiểm tra header origin/host.

---
Tài liệu tham khảo:
- https://angular.io/guide/build#using-corporate-proxy
- https://github.com/angular/angular-cli/blob/master/docs/documentation/stories/proxy.md