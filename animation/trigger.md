# Angular trigger() — Cách dùng

trigger() là hàm dùng để định nghĩa một animation trigger. Khi trigger này được gắn vào một phần tử trong template, các định nghĩa animation bên trong sẽ được chạy theo các trạng thái/transition được khai báo.

```ts
function trigger(
  name: string,
  definitions: AnimationMetadata[]
): AnimationTriggerMetadata;
```

Tham số:
- name: Tên của animation trigger, ví dụ "growWidth".
- definitions: Một mảng các metadata mô tả animation, ví dụ các transition, state, style, animate...

Ví dụ (một transition đơn giản):
```typescript
[
  transition("void => *", [
    style({ opacity: 0 }),
    animate(600, style({ opacity: 1 }))
  ])
]
```

Sử dụng trong component:
- Định nghĩa trigger này trong phần `animations` của component.
- Trong template, gắn trigger lên phần tử bằng cú pháp [@triggerName], và đổi giá trị để kích hoạt các transition tương ứng.

Ví dụ tóm tắt:
```typescript
@Component({
  selector: 'my-comp',
  template: `<div [@growWidth]="state">...</div>`,
  animations: [
    trigger('growWidth', [
      transition('void => *', [
        style({ width: '0px' }),
        animate('300ms ease-out', style({ width: '100px' }))
      ])
    ])
  ]
})
export class MyComp {
  state = 'in';
}
```

Ghi chú:
- `transition` có thể dùng biểu thức trạng thái (ví dụ `'open => closed'`, `'* => void'`, hoặc boolean/alias do bạn định nghĩa).
- Kết hợp `state()` để định nghĩa các style ở trạng thái cố định.
- Tham khảo tài liệu Angular animations để biết các helper khác như `keyframes`, `group`, `sequence`, v.v.