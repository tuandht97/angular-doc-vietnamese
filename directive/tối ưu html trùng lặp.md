# Một đoạn HTML cùng xuất hiện nhiều lần trong cùng một file thì tách ra thế nào

Bạn có thể tách phần đó thành một component, hoặc dùng `ngTemplateOutlet` để chia sẻ nội dung.

Ví dụ, giả sử có đoạn mã sau:

```html
<div>
  Khu vực thứ nhất
  <p>phần dùng chung</p>
</div>

<div>
  Khu vực thứ hai
  <p>phần dùng chung</p>
</div>
```

Làm sao để tách phần dùng chung ra?

Bạn có thể dùng `ng-template` rồi chèn nó vào nhiều nơi bằng `ngTemplateOutlet`:

```html
<div>
  Khu vực thứ nhất
  <ng-container *ngTemplateOutlet="shared"></ng-container>
</div>

<div>
  Khu vực thứ hai
  <ng-container *ngTemplateOutlet="shared"></ng-container>
</div>

<ng-template #shared>
  <p>phần dùng chung</p>
</ng-template>
```

`ngTemplateOutlet` nhận một tham chiếu template (ví dụ `#shared`) — bạn có thể đặt tên tuỳ ý. Nếu cần truyền tham số vào template, xem phần hướng dẫn về `ngTemplateOutlet` (sử dụng `context`).