# AOT — Biên dịch trước (Ahead-of-Time)

### Ahead-of-time (AOT) compilation
Nhà phát triển có thể biên dịch ứng dụng Angular tại thời điểm build (build-time). Sử dụng compiler-cli (ngc) để biên dịch ứng dụng, ứng dụng có thể khởi động trực tiếp từ một module factory, tức là không cần đóng gói trình biên dịch Angular vào gói JavaScript nữa. Ứng dụng được biên dịch trước sẽ tải nhanh hơn và có hiệu suất cao hơn. (compiler: trình biên dịch)

### Tại sao cần biên dịch trước?
Biên dịch giúp ứng dụng Angular đạt hiệu quả chạy cao hơn, chủ yếu là cải thiện hiệu năng, đồng thời tiết kiệm tài nguyên và băng thông.  
Angular sinh ra mã "thân thiện với máy ảo JS" (JS VM friendly) thay vì dùng một bộ logic chung cho mọi thành phần khi thực hiện render và change detection. Mã này giúp máy ảo JS tối ưu truy cập thuộc tính, thực thi change detection và render nhanh hơn.

### Những gì bị biên dịch?
Template của component được biên dịch thành các lớp JS chứa logic để phát hiện thay đổi trên binding và render UI.

### Quy trình JiT (Just-in-Time)
Quy trình phát triển ứng dụng không dùng AoT thường là:
- Dùng TypeScript phát triển ứng dụng Angular
- Dùng tsc biên dịch mã TypeScript
- Bundle
- Minify
- Triển khai

Khi người dùng mở trang:
- Trình duyệt tải mã JS
- Angular khởi động
- Angular bắt đầu quá trình biên dịch JiT trong trình duyệt, ví dụ sinh mã JS cho các component trong app
- Trang được render

### Quy trình AoT
Quy trình phát triển ứng dụng dùng AoT:
- Dùng TypeScript phát triển ứng dụng Angular
- Dùng ngc để biên dịch ứng dụng
  - Trình biên dịch Angular biên dịch template, sinh mã TypeScript
  - Mã TypeScript được biên dịch thành JavaScript
- Bundle
- Minify
- Triển khai

Mặc dù quy trình lúc build phức tạp hơn, phía người dùng thì đơn giản:
- Tải mã
- Angular khởi động
- Trang được render

### Khác biệt chính giữa JiT và AoT
- Thời điểm biên dịch khác nhau
- JiT sinh JS trực tiếp trong trình duyệt, trong khi AoT sinh mã TypeScript ở build-time (vì AoT sinh ra file .ts trước khi chuyển sang .js trong pipeline build).