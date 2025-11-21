# Angular query() — Cách dùng

query() được dùng để chọn các phần tử bên trong phần tử đang chạy animation và thêm animation cho chúng.

```ts
function query(
  selector: string,
  animation: AnimationMetadata | AnimationMetadata[],
  options: AnimationQueryOptions | null = null
): AnimationQueryMetadata;
```

- selector: bộ chọn (selector) giống như trong DOM (ví dụ '.item', 'div', v.v.).
- animation: một AnimationMetadata hoặc mảng AnimationMetadata định nghĩa animation sẽ áp dụng cho các phần tử được chọn.
- options: tuỳ chọn (ví dụ { limit?: number, optional?: boolean }).

Mục đích:
- Tìm một hoặc nhiều phần tử bên trong phần tử đang chạy animation và đưa chúng vào chuỗi animation hiện tại. Thường ta truyền một mảng animation mới để định nghĩa cách những phần tử được chọn sẽ animate.

Cách dùng chính

1) Giới hạn số phần tử được chọn (limit)
- query() sử dụng element.querySelectorAll nên có thể trả về nhiều phần tử. Dùng option.limit để giới hạn số lượng phần tử sẽ được ảnh hưởng.

Ví dụ:
```ts
template: `
  <div [@queryDemo] class="demo">
    <div class="demo1"></div>
    <div class="demo2"></div>
  </div>
`,
animations: [
  trigger('queryDemo', [
    transition('void => *', [
      query('div', animate(...), { limit: 1 })
    ])
  ])
]
```
Ở ví dụ trên, nếu limit: 1 thì chỉ phần tử đầu tiên (demo1) bị chọn.

2) Bật/tắt lỗi khi không tìm thấy phần tử (optional)
- Mặc định, nếu selector không khớp phần tử nào thì query() sẽ ném lỗi. Đặt `{ optional: true }` để bỏ qua lỗi này.

Ví dụ:
```ts
query('.demo-not-be-there', [
  animate(...),
  animate(...)
], { optional: true })
```

Các selector đặc biệt (pseudo-selectors)
- `:enter` / `:leave` — chọn phần tử mới được chèn / bị xoá
- `:animating` — chọn các phần tử đang chạy animation
- `@triggerName` — chọn phần tử có trigger animation tên cụ thể
- `@*` — chọn mọi phần tử có trigger
- `:self` — thêm chính phần tử hiện tại vào chuỗi animation

Bạn có thể kết hợp nhiều selector:
```ts
query(':self, .record:enter, .record:leave, @subTrigger', [...])
```

Ví dụ minh họa
```ts
@Component({
  selector: 'inner',
  template: `
    <div [@queryAnimation]="exp">
      <h1>Title</h1>
      <div class="content">
        Blah blah blah
      </div>
    </div>
  `,
  animations: [
    trigger('queryAnimation', [
      transition('* => goAnimate', [
        // ẩn các phần tử bên trong trước
        query('h1', style({ opacity: 0 })),
        query('.content', style({ opacity: 0 })),

        // lần lượt animate các phần tử bên trong
        query('h1', animate(1000, style({ opacity: 1 }))),
        query('.content', animate(1000, style({ opacity: 1 })))
      ])
    ])
  ]
})
class Cmp {
  exp = '';

  goAnimate() {
    this.exp = 'goAnimate';
  }
}
```

Ghi chú
- query() rất hữu ích để điều phối animation cho nhiều phần tử con trong một trigger.
- Khi dùng nhiều query liên tiếp, thứ tự sẽ ảnh hưởng đến trình tự thực thi animation.
- Luôn cân nhắc sử dụng `{ optional: true }` nếu có khả năng selector không tìm thấy phần tử, để tránh lỗi vào runtime.