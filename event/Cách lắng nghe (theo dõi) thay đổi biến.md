# Angular — Cách lắng nghe (theo dõi) thay đổi biến

Tổng quan: có nhiều cách để lắng nghe sự thay đổi của dữ liệu/biến trong Angular — tuỳ vào ngữ cảnh (đối tượng, form template-driven, reactive form, v.v.) bạn có thể chọn phương pháp phù hợp.

## 1. Sử dụng KeyValueDiffer / KeyValueChanges để theo dõi đối tượng
KeyValueDiffers cho phép so sánh (diff) các thuộc tính của một object theo từng vòng kiểm tra (ngDoCheck). Khi có thay đổi, bạn sẽ nhận được chi tiết (mục thêm, mục sửa, mục xóa).

HTML:
```html
<button (click)="a()">1233</button>
```

TypeScript:
```ts
import { KeyValueChanges, KeyValueDiffer, KeyValueDiffers } from '@angular/core';

export class Customer {
  firstName: string = '';
  favoriteColor: string = '';
}

export class Xx {
  private customerDiffer: KeyValueDiffer<string, any>;
  private customer: Customer;

  constructor(private differs: KeyValueDiffers) {
    this.customer = new Customer();
    // Tạo differ cho object customer
    this.customerDiffer = this.differs.find(this.customer).create();
  }

  customerChanged(changes: KeyValueChanges<string, any>) {
    console.log('changes');
    // Nếu muốn xem chi tiết:
    // changes.forEachRemovedItem((record) => ...);
    // changes.forEachAddedItem((record) => ...);
    // changes.forEachChangedItem((record) => ...);
  }

  ngDoCheck(): void {
    const changes = this.customerDiffer.diff(this.customer);
    if (changes) {
      this.customerChanged(changes);
    }
  }

  a() {
    this.customer.firstName += '2';
  }
}
```

Ghi chú:
- KeyValueDiffer hữu ích khi bạn cần lắng nghe thay đổi trên một object không phải là Observable và muốn nhận chi tiết các bản ghi bị thêm / sửa / xóa.
- Phải gọi diff(...) trong ngDoCheck để kiểm tra thay đổi mỗi vòng change detection.

## 2. Sử dụng ngModelChange (template-driven forms)
Trong template-driven form hoặc khi dùng [(ngModel)], bạn có thể lắng nghe sự kiện ngModelChange:

HTML:
```html
<input type="text" (ngModelChange)="doSomething($event)" [ngModel]="customer.firstName">
```

TypeScript:
```ts
doSomething(event: any) {
  console.log(event); // giá trị model hiện tại
}
```

Ghi chú:
- ngModelChange phát ra giá trị mới của model ngay khi người dùng thay đổi input.

## 3. Sử dụng valueChanges (Reactive Forms / Dynamic Forms)
Khi dùng Reactive Forms (FormGroup / FormControl), bạn có thể subscribe vào valueChanges để nhận mọi thay đổi của form hoặc control:

TypeScript:
```ts
ngOnInit() {
  this.form = this.fb.group({
    // controls...
  });
}

ngAfterViewInit(): void {
  this.form.valueChanges.subscribe(change => {
    console.log(change);
  });
}
```

Ghi chú:
- valueChanges phát ra object/giá trị mới mỗi khi form/control thay đổi.
- Thường kết hợp với debounceTime, distinctUntilChanged,... để tránh quá nhiều emission.

## 4. Lựa chọn phương pháp
- Nếu dữ liệu là Observable (ví dụ stream từ service hoặc Form.valueChanges), hãy subscribe vào Observable.
- Nếu dùng template-driven form, dùng ngModelChange cho input cụ thể.
- Nếu muốn theo dõi trực tiếp một object POJO và cần biết chi tiết thêm/sửa/xóa, dùng KeyValueDiffer trong ngDoCheck.
- Luôn nhớ unsubscribe (nếu subscription thủ công) khi component bị huỷ để tránh rò rỉ bộ nhớ.
