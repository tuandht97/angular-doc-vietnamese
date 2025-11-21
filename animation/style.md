# Angular style() — Cách dùng

style() được dùng để khai báo một bộ style (CSS) trong animation.

```ts
function style(
  tokens: '*' |
  { [key: string]: string | number } |
  Array<'*' | { [key: string]: string | number }>
): AnimationStyleMetadata;
```

Nội dung:
- style khai báo một đối tượng key-value tương ứng với các thuộc tính CSS, ví dụ:
```ts
style({ width: 100, height: 0 })
```

Auto-styles (ký hiệu '*'):
- Bạn có thể dùng '*' làm giá trị style để biểu thị "giá trị tự động" — tức là giá trị hiện tại của phần tử ở DOM.
- Điều này hữu ích khi bạn không biết trước giá trị cuối cùng (ví dụ chiều cao được thiết lập động bằng ngStyle hoặc nội dung thay đổi).

Ví dụ minh họa:
Giả sử phần tử có CSS:
```css
.demo {
  width: 100px;
  height: 100px;
}
```
Bạn muốn animate chiều cao từ 0 tới giá trị thực tế (100px) khi phần tử xuất hiện:

Component animations:
```ts
animations: [
  trigger('autoHeight', [
    state('void', style({ height: '0px' })),
    state('*', style({ height: '*' })),
    transition('void => *', animate(500))
  ])
]
```

Template:
```html
<div class="demo" [@autoHeight]></div>
```

Khi chiều cao được thiết lập động bằng [ngStyle]:
```html
<div class="demo" [ngStyle]="{ height: data.height }" [@autoHeight]></div>
```
và data.height được set ở runtime, sử dụng style({ height: '*' }) sẽ khiến animation chuyển từ 0 đến đúng giá trị height hiện có trong DOM trong thời gian 500ms.

Ghi chú:
- '*' rất hữu ích khi giá trị cuối cùng không cố định hoặc được tính động.
- Bạn có thể dùng '*' cho bất kỳ thuộc tính nào mà bạn muốn animate đến "giá trị hiện có" của phần tử.