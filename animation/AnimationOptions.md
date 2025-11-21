# AnimationOptions — Cách dùng

Import:
```ts
import { AnimationOptions } from '@angular/animations';
```

Giao diện:
```ts
interface AnimationOptions {
  delay?: number | string;
  params?: { [name: string]: any };
}
```

Ý nghĩa:
- delay: number | string  
  Thời gian trễ (delay) của animation — có thể là số (ms) hoặc chuỗi định dạng thời gian.
- params: object  
  Đối tượng tham số truyền khi khởi chạy animation để tùy chỉnh giá trị trong các style/animation (vd. dùng với animation()/useAnimation()).

Các hàm animation có thể nhận AnimationOptions:
- transition()
- sequence()
- group()
- query()
- animation()
- useAnimation()
- animateChild()
- Các animation được xây dựng bằng AnimationBuilder

Một số interface con liên quan:
- AnimateChildOptions
- AnimationQueryOptions

Ghi chú ngắn:
- Sử dụng params để truyền các giá trị động vào animation (ví dụ chiều cao/độ mờ...) thay vì hardcode trong metadata.
- delay hữu ích khi bạn muốn lùi thời điểm bắt đầu của một chuỗi animation hoặc bước riêng lẻ.