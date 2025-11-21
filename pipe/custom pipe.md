# Cách viết một pipe chuyển đổi ngày

> Mục đích: chuyển một thời gian dạng 1509601513000 thành định dạng 2017-10-31

Ta có thể tạo một custom pipe trong thư mục share, ví dụ file: share/pipe/date-transform.pipe.ts

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'dateTransformPipe' })
export class DateTransformPipe implements PipeTransform {
  transform(val: any): string {
    const date = new Date(val);
    const dateWanted =
      date.getFullYear() + '-' + (date.getMonth() + 1) + '-' + date.getDate();
    return dateWanted;
  }
}
```

Khai báo và xuất pipe trong ShareModule (ví dụ: share.module.ts):

```typescript
import { NgModule } from '@angular/core';
import { DateTransformPipe } from './pipe/date-transform.pipe';

@NgModule({
  declarations: [DateTransformPipe],
  exports: [DateTransformPipe]
})
export class ShareModule {}
```

Nhập ShareModule vào module nơi cần dùng pipe:

```typescript
import { ShareModule } from '../share/share.module';

@NgModule({
  imports: [ShareModule]
})
export class SomeFeatureModule {}
```

Sử dụng pipe trực tiếp trong template (ví dụ license.component.html):

```html
<div>{{ data.startDate | dateTransformPipe }}</div>
```

Ghi chú:
- Pipe trên đơn giản chuyển timestamp (hoặc giá trị mà Date chấp nhận) thành chuỗi "YYYY-M-D". Nếu cần định dạng chuẩn hơn (các chữ số luôn 2 ký tự cho tháng/ngày), hoặc xử lý timezone, bạn có thể mở rộng logic (vd. zero-pad, hoặc dùng Angular DatePipe).
- Nếu dữ liệu có thể là null/undefined, hãy kiểm tra/trả về giá trị mặc định trong transform để tránh lỗi.