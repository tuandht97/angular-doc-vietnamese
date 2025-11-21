# ng-template

### Khái niệm
`ng-template` là một phần tử template — nội dung bên trong nó không được render ra DOM ngay lập tức. Chỉ khi có mã khác chủ động tham chiếu hoặc hiển thị template này thì nội dung bên trong mới được chèn và hiển thị.

Thường `ng-template` được dùng kết hợp với các structural directive như `*ngIf`, `*ngFor`, `[ngSwitch]` hoặc các directive tùy chỉnh.

Nếu bạn viết như sau thì sẽ KHÔNG hiển thị gì:
```html
<ng-template>
  <p>Đây là một nội dung</p>
</ng-template>
```

### Cách sử dụng
Ví dụ phổ biến: dùng `ng-template` làm phần `else` cho `*ngIf`:

```html
<p *ngIf="isCat; else isDog">
  Mèo
</p>

<ng-template #isDog>
  <p>Chó</p>
</ng-template>
```

Giải thích:
- Khi `isCat` là `true` sẽ hiển thị thẻ `<p>` chứa "Mèo".
- Khi `isCat` là `false` sẽ lấy template có `#isDog` và chèn nội dung `<p>Chó` vào vị trí `else`.

### Khi nào dùng `ng-template`
- Khi bạn có một đoạn HTML muốn giữ lại nhưng không render ngay, chờ điều kiện hoặc được directive/logic khác dùng.
- Khi cần tách template phức tạp ra ngoài để tái sử dụng hoặc để các structural directive khác tham chiếu.

### Ghi chú thêm
- `ng-template` bản chất không tạo một phần tử DOM hiển thị; Angular sẽ thay thế / chèn nội dung tương ứng vào DOM khi cần.
- Bạn có thể kết hợp `ng-template` với `TemplateRef` và `ViewContainerRef` trong TypeScript để tạo/nhúng template một cách lập trình.