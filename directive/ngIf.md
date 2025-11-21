# ngIf

`ngIf` dùng để hiển thị hoặc ẩn phần tử trong template, tương tự như câu lệnh `if` trong JavaScript.

Ví dụ logic:
```ts
if (isFruit) {
  // true
} else {
  // false
}
```

Trong Angular, `isFruit` là một biến boolean, ví dụ:
```ts
isFruit = true;
```

## 1. Cơ bản: *ngIf
`*ngIf` hiển thị phần tử khi biểu thức là `true`, ẩn khi là `false`.

```html
<div *ngIf="isFruit; then otherFruit; else animal">
  Quả táo
</div>
```

## 2. *ngIf với then
Khi dùng `then`, bạn có thể tách phần nội dung ra thành một `ng-template`:

```html
<div *ngIf="isFruit; then otherFruit">
  Quả táo
</div>

<ng-template #otherFruit>
  <div>
    Chuối
  </div>
</ng-template>
```

`otherFruit` là tên template do bạn định nghĩa, dùng để tách đoạn template ra ngoài.

## 3. *ngIf với then và else
Khi dùng cả `then` và `else`, nội dung ở `ngIf` và `then` hiển thị khi `true`, còn `else` hiển thị khi `false`:

```html
<div *ngIf="isFruit; then otherFruit; else animal">
  Táo
</div>

<ng-template #otherFruit>
  <div>
    Chuối
  </div>
</ng-template>

<ng-template #animal>
  <div>
    Mèo
  </div>
</ng-template>
```

## Sử dụng biểu thức chính quy (regex) trong ngIf
Nếu muốn dùng regex để quyết định hiển thị, tốt nhất tách logic ra phương thức trong component:

Template:
```html
<ul *ngFor="let data of testStrings">
  <li *ngIf="judge(data)">{{ data }}</li>
</ul>
```

Component (TS):
```ts
testStrings = ["javascript", "jav", "avaj", "nodejs"];

judge(data: string) {
  return /java/.test(data);
}
```

Ghi chú:
- Tránh viết logic phức tạp trực tiếp trong template; đặt vào method giúp dễ bảo trì.
- Nếu `@Input()` truyền object/array, lưu ý `ngIf` (và các check khác) nhìn vào reference — thay đổi thuộc tính bên trong không làm thay đổi reference dẫn tới các lifecycle/ngIf có thể không được kích hoạt; khi cần, hãy truyền bản sao (deep copy) hoặc cập nhật reference.