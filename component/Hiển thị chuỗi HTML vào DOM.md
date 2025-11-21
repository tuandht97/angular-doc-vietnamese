# Angular — Hiển thị chuỗi HTML vào DOM

Nếu bạn có một chuỗi HTML và muốn render nó thành DOM trong template Angular, dùng binding `innerHTML`:

```html
<!-- Template -->
<div [innerHTML]="theHtmlString"></div>
```

Lưu ý quan trọng:
- Việc dùng `{{ theHtmlString }}` (interpolation) sẽ *không* render HTML — nó sẽ hiển thị chuỗi dưới dạng text. Chỉ `[innerHTML]` mới chèn HTML vào DOM.
- Angular sẽ tự động "sanitize" (lọc) HTML để tránh XSS. Một số thẻ hoặc thuộc tính nguy hiểm (ví dụ `<script>`, `on*` handlers) sẽ bị loại bỏ.

## Khi cần tin tưởng HTML (bỏ qua sanitization)
Chỉ làm điều này nếu bạn hoàn toàn tin tưởng nguồn nội dung (rất nguy hiểm nếu dữ liệu từ user hoặc nguồn không tin cậy). Sử dụng `DomSanitizer` để đánh dấu chuỗi là "trusted":

```ts
// component.ts
import { Component } from '@angular/core';
import { DomSanitizer, SafeHtml } from '@angular/platform-browser';

@Component({...})
export class DemoComponent {
  theHtmlString = '<p style="color: red">Hello <b>world</b></p>';
  safeHtml: SafeHtml;

  constructor(private sanitizer: DomSanitizer) {
    // Bỏ qua kiểm tra bảo mật (chỉ khi bạn chắc chắn nội dung an toàn)
    this.safeHtml = this.sanitizer.bypassSecurityTrustHtml(this.theHtmlString);
  }
}
```

```html
<!-- template -->
<div [innerHTML]="safeHtml"></div>
```

## Các điểm cần cân nhắc
- Không bao giờ dùng `bypassSecurityTrustHtml` cho dữ liệu từ người dùng hoặc nguồn không đáng tin cậy — điều này mở cửa cho XSS.
- Nếu có thể, hãy sanitize phía server hoặc lọc những phần tử/thuộc tính nguy hiểm trước khi gửi về client.
- `innerHTML` sẽ chèn nội dung tĩnh (HTML + CSS). Các script trong HTML sẽ không được thực thi bởi trình duyệt khi chèn bằng innerHTML (và Angular còn loại bỏ script khi sanitize).
- Nếu bạn cần thao tác DOM phức tạp, cân nhắc dùng `Renderer2` hoặc tạo component động thay vì chèn HTML thô.

## Các phương án thay thế
- Tạo component/Template động thay vì chèn HTML thô (an toàn và dễ kiểm soát hơn).
- Dùng thư viện sanitize chuyên dụng để thanh lọc HTML trước khi hiển thị.

Tóm lại: dùng `[innerHTML]` để render HTML; Angular sẽ sanitize mặc định; chỉ dùng `DomSanitizer.bypassSecurityTrustHtml()` khi thực sự tin tưởng nguồn và cần bỏ qua sanitization.