# Angular Dynamic Component

## 1. Viết một component động
Cách viết component động không khác component bình thường:

```ts
...
@Component({
  selector: 'login',
})
export class LoginComponent {}
```

## 2. Khai báo (declaration)
Trước Angular 9 (không dùng Ivy) bạn cần khai báo component động trong `entryComponents` của module:

```ts
@NgModule({
    declarations: [LoginComponent],
    entryComponents: [LoginComponent]
})
```

(Lưu ý: với Ivy (Angular 9+) thường không cần `entryComponents` nữa.)

## 3. Cách sử dụng — có hai cách phổ biến

### 3.1 Cách 1 — dùng ngComponentOutlet
Trong TypeScript:
```ts
import { LoginComponent } from 'xxx';
...
export class UserComponent {
    loginComponent = LoginComponent;
}
```

Trong template:
```html
<ng-container *ngComponentOutlet="loginComponent"></ng-container>
```

### 3.2 Cách 2 — tạo bằng ComponentFactoryResolver (tương thích với các phiên bản cũ)
Template:
```html
<ng-template #loginContainer></ng-template>
```

TypeScript:
```ts
import { ComponentFactory, ComponentFactoryResolver, OnInit, ViewChild, ViewContainerRef } from '@angular/core';
import { LoginComponent } from 'xxx';

export class UserComponent implements OnInit, OnDestroy {
   loginRef: any;
   @ViewChild('loginContainer', { read: ViewContainerRef, static: true })
   loginContainer!: ViewContainerRef;

   constructor(private cfr: ComponentFactoryResolver) {}

   ngOnInit() {
       this.loginContainer.clear();
       const factory: ComponentFactory<any> = this.cfr.resolveComponentFactory(LoginComponent);
       this.loginRef = this.loginContainer.createComponent(factory);
   }

   ngOnDestroy(){
       // Khi component cha hủy, cần hủy cả dynamic component nếu có
       if (this.loginRef) {
           this.loginRef.destroy();
       }
   }
}
```

(Lưu ý: với Angular 13+ có API mới `ViewContainerRef.createComponent` mà không cần `ComponentFactoryResolver`.)

## 4. Truyền tham số và bắt event cho dynamic component

Khi tạo dynamic component bằng cách trên, bạn có thể:
- Truyền @Input bằng cách gán trực tiếp vào instance:
```ts
this.loginRef.instance.someInputProp = value;
```

- Lắng nghe @Output bằng cách subscribe vào EventEmitter của instance:
```ts
this.loginRef.instance.someOutput.subscribe(data => {
  // xử lý data
});
```

Những điểm lưu ý:
- `this.loginRef.instance` là thể hiện (instance) của component động, bạn có thể truy cập thuộc tính và EventEmitter trên đó.
- Nhớ hủy subscription hoặc hủy component (`destroy()`) trong `ngOnDestroy` để tránh rò rỉ bộ nhớ.