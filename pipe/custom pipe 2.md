# Cách tự tạo (custom) pipe

## 1. Viết một pipe đơn giản
Tạo một pipe trả về thứ tự: truyền vào 1 hiển thị `Thứ nhất`, truyền vào 2 hiển thị `Thứ hai`.

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'getOrder'
})
export class GetOrderPipe implements PipeTransform {
  transform(value: number): any {
    switch (value) {
      case 1:
        return 'Thứ nhất';
      case 2:
        return 'Thứ hai';
      default:
        return '';
    }
  }
}
```

Sử dụng trong template:
```html
{{ 1 | getOrder }}
```
Kết quả hiển thị:
```
Thứ nhất
```

## 2. Truyền tham số vào pipe
Bạn có thể truyền tham số thêm vào pipe:

Template:
```html
{{ 2 | getOrder:'Tham số' }}
```

Pipe:
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'getOrder'
})
export class GetOrderPipe implements PipeTransform {
  transform(value: number, args: string): any {
    switch (value) {
      case 1:
        return 'Thứ nhất';
      case 2:
        return 'Thứ hai' + args;
      default:
        return '';
    }
  }
}
```

Kết quả:
```
Thứ haiTham số
```

## 3. Pipe trả về HTML — cách hiển thị
Giả sử bạn viết một pipe trả về chuỗi HTML (ví dụ trả về một thẻ div với màu chữ):

Pipe tạo HTML:
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'color'
})
export class ColorPipe implements PipeTransform {
  transform(value: string): any {
    return `<div style="color: ${value}">${value}</div>`;
  }
}
```

Sử dụng:
```html
{{ 'red' | color }}
```
Mặc định Angular sẽ hiển thị chuỗi HTML đó như văn bản thuần, không render thành HTML. Lý do: Angular thực hiện xử lý an toàn để tránh chèn HTML độc hại vào DOM.

Để cho phép render HTML do bạn tin tưởng, tạo một pipe dùng DomSanitizer để đánh dấu nội dung là "an toàn":

```typescript
import { Pipe, PipeTransform } from "@angular/core";
import {
  DomSanitizer,
  SafeHtml,
  SafeStyle,
  SafeScript,
  SafeUrl,
  SafeResourceUrl
} from "@angular/platform-browser";

@Pipe({
  name: "safe"
})
export class SafePipe implements PipeTransform {
  constructor(protected sanitizer: DomSanitizer) {}

  public transform(
    value: any,
    type: string
  ): SafeHtml | SafeStyle | SafeScript | SafeUrl | SafeResourceUrl {
    switch (type) {
      case "html":
        return this.sanitizer.bypassSecurityTrustHtml(value);
      case "style":
        return this.sanitizer.bypassSecurityTrustStyle(value);
      case "script":
        return this.sanitizer.bypassSecurityTrustScript(value);
      case "url":
        return this.sanitizer.bypassSecurityTrustUrl(value);
      case "resourceUrl":
        return this.sanitizer.bypassSecurityTrustResourceUrl(value);
      default:
        throw new Error(`Invalid safe type specified: ${type}`);
    }
  }
}
```

Lưu ý: bypassSecurityTrust... sẽ làm cho Angular bỏ qua kiểm tra an toàn — chỉ dùng khi bạn hoàn toàn tin tưởng nguồn nội dung.

Để render HTML trả về bởi pipe `color`, bạn có thể kết hợp hai pipe và dùng thuộc tính [outerHTML]:

```html
<span [outerHTML]="'red' | color | safe: 'html'"></span>
```

Kết quả: chuỗi HTML do pipe `color` trả về sẽ được render, ví dụ hiển thị chữ "red" với màu đỏ.

---
Ghi chú ngắn:
- Luôn xử lý giá trị null/undefined trong pipe để tránh lỗi.
- Cân nhắc dùng Angular built-in DatePipe, DecimalPipe... khi có thể thay vì tự triển khai lại.