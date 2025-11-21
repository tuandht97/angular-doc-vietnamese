# Angular state() — Cách dùng

state() dùng để định nghĩa kiểu (style) khi một animation mục tiêu ở trong một trạng thái cụ thể.

Chữ ký:
```ts
function state(
  name: string,
  styles: AnimationStyleMetadata,
  options?: { params: { [name: string]: any } }
): AnimationStateMetadata;
```

Tham số:
- name  
  - Tên trạng thái hiện tại. Có thể khai báo nhiều tên cùng lúc, cách nhau bằng dấu phẩy, ví dụ `'active,clicked'`.
  - Ngoài tên tùy ý, bạn còn có thể dùng `void` và `*`:
    - `void`: trạng thái trước khi animation khởi chạy (phần tử chưa tồn tại trong DOM).
    - `*`: trạng thái mặc định / bất kỳ trạng thái nào (wildcard).
- styles  
  - Các style áp dụng khi ở trạng thái này, ví dụ `style({ width: 0 })`.
- options  
  - Tuỳ chọn bổ sung (thường để khai báo params cho state metadata).

Ví dụ sử dụng trong component:
```ts
import { Component } from '@angular/core';
import { trigger, state, style, transition, animate } from '@angular/animations';

@Component({
  selector: 'app-example',
  template: `
    <button (click)="toggle()">Toggle</button>
    <div [@openClose]="isOpen ? 'open' : 'closed'">
      Nội dung animating
    </div>
  `,
  animations: [
    trigger('openClose', [
      state('open', style({
        height: '*',
        opacity: 1,
        transform: 'translateX(0)'
      })),
      state('closed', style({
        height: '0px',
        opacity: 0,
        transform: 'translateX(-20px)'
      })),
      // chuyển đổi hai chiều giữa open và closed
      transition('open <=> closed', [
        animate('300ms ease-in-out')
      ]),
      // ví dụ transition từ void => *
      transition('void => *', [
        style({ opacity: 0 }),
        animate('200ms ease-out', style({ opacity: 1 }))
      ])
    ])
  ]
})
export class ExampleComponent {
  isOpen = true;
  toggle() { this.isOpen = !this.isOpen; }
}
```

Ghi chú:
- state() chỉ định style "tĩnh" khi ở trạng thái đó; để chuyển giữa các trạng thái, dùng transition() và animate().
- Bạn có thể định nghĩa nhiều state với tên khác nhau hoặc dùng ký hiệu đặc biệt `void` / `*` để chỉ trạng thái trước/sau.
- options.params hữu ích khi bạn muốn truyền tham số động vào style/state (xem tài liệu AnimationStateMetadata để hiểu rõ hơn).