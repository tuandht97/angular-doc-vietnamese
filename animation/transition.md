# Angular transition() — Cách dùng

transition() định nghĩa cách chuyển đổi giữa các trạng thái của animation.

```ts
function transition(
  stateChangeExpr: string,
  steps: AnimationMetadata | AnimationMetadata[],
  options: AnimationOptions | null = null
): AnimationTransitionMetadata;
```

Tham số:
- stateChangeExpr: string  
  Biểu thức mô tả chuyển đổi trạng thái, ví dụ A => B. Hỗ trợ nhiều dạng:
  1. Chạy animation khi trạng thái thay đổi (ví dụ khi phần tử vào DOM):
     ```
     transition("void => *", animate(500))
     ```
  2. Chạy cùng animation cho cả hai chiều:
     ```
     transition("void <=> *", animate(500))
     ```
  3. Định nghĩa nhiều cặp trạng thái dùng chung animation:
     ```
     transition("on => off, off => void", animate(500))
     ```

- steps: AnimationMetadata | AnimationMetadata[]  
  Các bước animation sẽ thực thi, ví dụ animate(...) hoặc mảng các bước như `[ animate('100ms ease-in'), animate(600) ]`.

- options: AnimationOptions | null  
  Tùy chọn cho animation như delay hoặc params để thay đổi timing/style.

Ghi chú:
- Thay cho `"void => *"` bạn có thể dùng alias `:enter`:
  ```
  transition(':enter', animate(500))
  ```
- Tương tự `" * => void"` có thể viết là `:leave`:
  ```
  transition(':leave', animate(500))
  ```

Ví dụ sử dụng trong component:
```typescript
import { Component } from '@angular/core';
import { trigger, transition, style, animate } from '@angular/animations';

@Component({
  selector: 'app-example',
  template: `<div [@fadeInOut] *ngIf="show">Nội dung</div>`,
  animations: [
    trigger('fadeInOut', [
      transition(':enter', [
        style({ opacity: 0 }),
        animate('500ms ease-out', style({ opacity: 1 }))
      ]),
      transition(':leave', [
        animate('500ms ease-in', style({ opacity: 0 }))
      ])
    ])
  ]
})
export class ExampleComponent {
  show = true;
}
```

Tổng kết ngắn:
- transition() là nơi bạn khai báo khi nào và bằng cách nào các style thay đổi giữa các state sẽ được animate.
- Kết hợp với state(), style(), animate() để tạo animation phong phú và có thể dùng các alias `:enter` / `:leave` cho trường hợp vào/ra DOM.