# Cú pháp sự kiện trong HTML (Angular)

Trong Angular, cú pháp để lắng nghe sự kiện trên phần tử là:

```html
<p (eventName)="yourEventName()"></p>
```

#### 1. Ví dụ sự kiện click và giải thích

```html
<a (click)="onClick()">Click sự kiện</a>
```

- `click` là tên sự kiện (ở đây là sự kiện nhấp chuột).
- Dấu `()` (ngoặc tròn) biểu thị "lắng nghe sự kiện".
- Phần bên phải dấu `=` là biểu thức hoặc tên hàm trong component, ví dụ `onClick()`.

Trong component TypeScript bạn định nghĩa hàm xử lý:
```typescript
onClick() {
  // xử lý khi click
}
```

#### 2. Bắt sự kiện chuột phải (menu tùy chỉnh)

Bạn có thể lắng nghe sự kiện chuột phải bằng `contextmenu`:

```html
<div (contextmenu)="onRightClick($event)">Tôi là một nút</div>
```

Trong component xử lý sự kiện, dùng `event.preventDefault()` để chặn menu ngữ cảnh mặc định của trình duyệt:

```typescript
onRightClick(event: MouseEvent) {
  event.preventDefault();
  console.log('Đã nhấn chuột phải');
  // hiển thị menu tùy chỉnh ở đây
}
```

Ghi chú:
- `$event` là đối tượng sự kiện gốc (MouseEvent, KeyboardEvent, v.v.), bạn có thể truy cập thông tin vị trí chuột, phím bấm, v.v.
- Ngoài `click` và `contextmenu`, Angular hỗ trợ nhiều sự kiện DOM khác (mouseenter, keyup, submit, focus, blur, ...).