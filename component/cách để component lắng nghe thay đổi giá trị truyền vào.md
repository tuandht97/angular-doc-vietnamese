# Các cách để component lắng nghe thay đổi giá trị truyền vào

Dưới đây là vài cách thông dụng để một component nhận biết và xử lý khi giá trị được truyền vào thay đổi.

### 1. Sử dụng lifecycle hook OnChanges

Áp dụng khi bạn muốn nhận tất cả thay đổi của các `@Input()` (được gọi mỗi khi reference của input thay đổi).

```ts
import { Input, OnChanges, SimpleChanges } from '@angular/core';

export class Xxx implements OnChanges {
  @Input() title: string;

  ngOnChanges(sc: SimpleChanges) {
    if ('title' in sc) { // kiểm tra tồn tại trước khi dùng
      // sc.title.currentValue --> giá trị mới, xử lý logic ở đây
    }
  }
}
```

`SimpleChanges` cho bạn `previousValue`, `currentValue` và `firstChange` để xử lý chi tiết hơn.

### 2. Dùng getter / setter cho @Input

Bạn có thể dùng accessor để trực tiếp chạy logic khi giá trị được gán vào thuộc tính.

```ts
import { Input } from '@angular/core';

export class Xxx {
  private _title: string;

  @Input()
  get title() {
    return this._title;
  }
  set title(value: string) {
    // xử lý logic ngay khi nhận value mới
    this._title = value;
  }
}
```

Accessor thích hợp khi bạn muốn xử lý/biến đổi giá trị ngay khi nó được set.

---

Lưu ý quan trọng:
- Nếu `@Input` là kiểu tham chiếu (object, array) và bạn chỉ thay đổi thuộc tính bên trong (không thay đổi reference), cả `ngOnChanges` lẫn setter sẽ không được gọi.  
- Để bắt được các thay đổi nội tại của object/array, bạn có thể:
  - Truyền một bản sao (deep copy) từ cha xuống mỗi khi thay đổi, hoặc
  - Quan sát các thay đổi bên trong bằng các cơ chế khác (ví dụ: Observable trong service), hoặc
  - Trong một số trường hợp, dùng `ChangeDetectionStrategy.OnPush` kết hợp cập nhật reference để tối ưu.