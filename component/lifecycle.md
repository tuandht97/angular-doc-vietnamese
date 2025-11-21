# Giới thiệu lifecycle trong Angular

> Component/directive đều có vòng đời (component thực ra cũng là directive). Dưới đây lấy component làm ví dụ.

```ts
import { OnChanges, SimpleChanges } from "@angular/core";
```

## 1. Có những lifecycle nào?

### 1.1 OnChanges
- Gọi khi nào: được gọi khi giá trị truyền vào component thay đổi (thường là dữ liệu từ `@Input`).
- Tham số: *SimpleChanges* — là một object có cấu trúc như sau:
```ts
class SimpleChange {
  constructor(previousValue: any, currentValue: any, firstChange: boolean)
  previousValue: any      // giá trị trước khi thay đổi
  currentValue: any       // giá trị sau khi thay đổi
  firstChange: boolean    // có phải lần thay đổi đầu tiên hay không
  isFirstChange(): boolean // trả về boolean giống firstChange
}
```

<details>
<summary>Ví dụ dùng SimpleChanges</summary>

```ts
@Input() title;

ngOnChanges(simpleChange: SimpleChanges) {
  // Kiểm tra tồn tại tránh lỗi
  if ('title' in simpleChange) {
    console.log("Giá trị trước khi thay đổi", simpleChange.title.previousValue);
    console.log("Giá trị sau khi thay đổi", simpleChange.title.currentValue);
  }
}
```
</details>

- Lưu ý: nếu giá trị truyền vào là kiểu tham chiếu (object/array), chỉ khi tham chiếu thay đổi (reference changed) thì `ngOnChanges` mới được gọi.

### 1.2 OnInit
- Gọi khi nào: được gọi sau khi khởi tạo component. Chỉ chạy một lần.
- Lưu ý: thường dùng để thực hiện các thao tác khởi tạo như gọi API, thiết lập dữ liệu ban đầu.
- Lưu ý thêm: tại thời điểm `ngOnInit` chạy, view chưa chắc đã render xong (nhưng input đã có).

### 1.3 DoCheck
- Gọi khi nào:
  - Được gọi mỗi khi có bất kỳ change detection nào diễn ra (ví dụ: xử lý sự kiện click, callback HTTP, thay đổi route...).
  - Nếu trạng thái thay đổi nhưng Angular không tự phát hiện được, `ngDoCheck` có thể được gọi để developer tự kiểm tra thay đổi.
- Lưu ý: `ngDoCheck` rất thường xuyên được gọi — không khuyến khích thực hiện công việc nặng trong hook này.

### 1.4 AfterContentInit
- Gọi khi nào: được gọi sau khi nội dung được projection vào component thông qua `ng-content` đã được khởi tạo.
- Lưu ý: directive không có lifecycle này (chỉ component có).

### 1.5 AfterContentChecked
- Gọi khi nào: được gọi sau mỗi lần change detection cho nội dung được projection (`ng-content`) hoàn tất.
- Lưu ý: directive không có lifecycle này.

### 1.6 AfterViewInit
- Gọi khi nào: được gọi sau khi view của component và các view con đã được khởi tạo hoàn chỉnh.
- Lưu ý: directive không có lifecycle này.
- Thường dùng để truy vấn `@ViewChild`/`@ViewChildren` an toàn hoặc thao tác DOM.

### 1.7 AfterViewChecked
- Gọi khi nào: được gọi sau mỗi lần change detection cho view của component và các view con hoàn tất.
- Lưu ý: directive không có lifecycle này.

### 1.8 OnDestroy
- Gọi khi nào: được gọi trước khi component bị hủy.
- Dùng để hủy subscription, remove event listener, dọn dẹp tài nguyên để tránh rò rỉ bộ nhớ.

---

## 2. FAQ

### 2.1 Khác biệt giữa constructor và OnInit?
Trong `constructor` không phải mọi dữ liệu đều đã sẵn sàng — ví dụ `@ContentChildren` / `@ContentChild` / `@ViewChildren` / `@ViewChild` / `@Input` có thể chưa tồn tại ở thời điểm constructor chạy. Vì vậy các thao tác phụ thuộc vào những dữ liệu này nên đặt trong `ngOnInit` hoặc các lifecycle phù hợp.

### 2.2 Khác biệt giữa AfterContentInit và AfterViewInit?
- `AfterContentInit` được gọi khi phần nội dung được `ng-content` truyền vào component đã được khởi tạo xong. Dùng hook này nếu bạn cần thao tác với nội dung được projection.
- `AfterViewInit` được gọi khi view của component và các view con (bao gồm template nội bộ và `@ViewChild`/... ) đã khởi tạo xong. Dùng hook này để thao tác DOM nội bộ hoặc truy cập `@ViewChild`.

>LƯU Ý: không nên thay đổi giá trị của parent component trong các lifecycle `AfterContentInit` hoặc `AfterViewInit`!