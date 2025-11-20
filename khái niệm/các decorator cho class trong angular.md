# Angular — Các decorator cho class

Angular có 5 loại decorator cho class, dùng để chỉ định mục đích của mỗi class và cách Angular sẽ xử lý class đó:
- `NgModule` — đánh dấu một module
- `Component` — đánh dấu một component
- `Directive` — đánh dấu một directive
- `Injectable` — đánh dấu một service
- `Pipe` — đánh dấu một pipe

---

## 1. NgModule
Dùng để đánh dấu một class là một module và cấu hình các dữ liệu liên quan đến module đó.

Những cấu hình chính:

- imports  
  Nhập các module mà module hiện tại cần sử dụng. `Lưu ý: các module lazy-loaded không nên import thường (nếu import thì mất tác dụng lazy-load)`.

- declarations: Array<Type<any> | any[]>  
  Khai báo các component, directive, pipe — gọi chung là "declarables". Một declarable chỉ được khai báo trong đúng một NgModule.

- providers: []  
  Đăng ký các service (provider) cho injector của module.

- exports: Array<Type<any> | any[]>  
  Nếu module khác muốn sử dụng component/directive/pipe của module này thì phải export chúng.

- entryComponents: []  
  Khai báo các component tạo động (dynamic). Angular sẽ tạo ComponentFactory cho các component này và lưu vào ComponentFactoryResolver. (Lưu ý: với Ivy (Angular 9+), entryComponents thường không còn cần nữa.)

- bootstrap: Array<Type<any> | any[]>  
  Các component sẽ được bootstrap khi module này được khởi động (thường dùng trong AppModule). Các component ở đây tự động được thêm vào entryComponents.

- schemas: Array<SchemaMetadata | any[]>  
  Cho phép các phần tử/thuộc tính không chuẩn trong template (ví dụ dùng custom elements). Xác định schema các phần tử/thuộc tính được phép.

- id: string  
  Tên hoặc định danh duy nhất cho module khi đăng ký vào getModuleFactory. Nếu `undefined` thì module sẽ không được đăng ký.

- jit: true  
  Nếu là `true` thì module này sẽ bị AOT compiler bỏ qua và luôn dùng JIT biên dịch.

---

## 2. Component
Decorator để đánh dấu một class là component và cung cấp metadata để Angular xử lý/khởi tạo component đó. Component thực chất là một dạng đặc biệt của directive.

Các thuộc tính phổ biến:

- selector: string  
  CSS selector để gắn component trong template (có thể là tag, attribute, class...). Ví dụ `selector: 'mo-dir'` thì dùng `<mo-dir></mo-dir>`; `selector: '[mo-dir]'` thì dùng `<div mo-dir></div>`.

- template (string) / templateUrl (string)  
  Chỉ dùng một trong hai: `template` chứa HTML inline, `templateUrl` là đường dẫn tới file HTML.

- styles / styleUrls  
  `styles` là CSS inline, `styleUrls` là danh sách file CSS.

- animations  
  Một hoặc nhiều trigger() định nghĩa state() và transition() cho animation.

- providers  
  Đăng ký service dành riêng cho component (component-level providers).

- changeDetection  
  Chỉ định chiến lược change detection (Ví dụ Default hoặc OnPush).

- inputs: string[]  
  Liệt kê tên các thuộc tính đầu vào (tương đương `@Input`). Khác với `@Input` (annotation trên thuộc tính), `inputs` là một mảng chuỗi.

Ví dụ:
```ts
@Component({
  selector: 'mo-dir',
  inputs: ['id: id = 123'],
  template: `
   {{ id }}
  `
})
class BankAccount {
  id: number;
}
```
(Ở ví dụ gốc `inputs: [id: 123]` ý là khai báo một input `id` có giá trị mặc định `123`. Khi dùng thực tế thường báo `@Input() id: number = 123`.)

- outputs: string[]  
  Liệt kê các event xuất ra (tương đương `@Output`). Là mảng tên event.

Ví dụ:
```ts
@Component({
  selector: 'mo-dir',
  outputs: ['idChange']
})
class ChildDir {
  idChange: EventEmitter<string> = new EventEmitter<string>();
}
```
Tương đương `@Output() idChange = new EventEmitter<string>();`.

---

## 3. Directive
Directive cũng có `selector` để xác định nơi directive được áp dụng. Selector là một CSS selector và có nhiều dạng:

- Element / tag:
```ts
@Directive({ selector: 'mo' })
```
sử dụng: `<mo></mo>`

- Class:
```ts
@Directive({ selector: '.mo' })
```
sử dụng: `<div class="mo"></div>`

- Attribute:
```ts
@Directive({ selector: '[mo]' })
```
sử dụng: `<div mo></div>`

- Attribute với giá trị:
```ts
@Directive({ selector: '[type=text]' })
```
sử dụng: `<input type="text">`

- Loại trừ selector (not):
Ví dụ muốn match phần tử có attribute `mo` nhưng không có class `.foo`:
```ts
@Directive({ selector: 'div[mo]:not(.foo)' })
```
`<div mo class="foo"></div>` — không match  
`<div mo></div>` — match

- Nhiều selector (any):
```ts
@Directive({ selector: '.foo, .bar' })
```
Các phần tử có class `foo` hoặc `bar` (hoặc cả hai) đều match.

Các thuộc tính khác của Directive:

- inputs, outputs: string[] — tương tự component
- providers — inject service vào directive
- exportAs: string  
  Cho phép expose instance directive ra template dưới một tên để truy cập từ template.

Ví dụ:
```ts
@Directive({
 selector: '[changeColor]',
 exportAs: 'changeColor'
})
class ChangeColorDirective {
  toggleColor() {
    // logic thay đổi màu
  }
}
```
Trong template:
```html
<p changeColor #changeColorRef="changeColor"></p>
<button (click)="changeColorRef.toggleColor()">Toggle</button>
```
`changeColorRef` là reference tới instance directive nhờ `exportAs`.

- queries, host, jit  
  Tham khảo thêm ở tài liệu chính thức:
  - queries: https://angular.cn/api/core/Directive#queries  
  - host: https://angular.cn/api/core/Directive#host  
  - jit: https://angular.cn/api/core/Directive#jit

---

## 4. Injectable
Decorator dùng để đánh dấu một service có thể được inject.

Ví dụ:
```ts
@Injectable({
  providedIn: 'root',
})
export class MyService { ... }
```

Giá trị `providedIn` thường là:
- `'root'` — injector cấp ứng dụng (application-level injector) — phổ biến nhất.
- `'platform'` — singleton chia sẻ cho tất cả ứng dụng trên trang (platform injector).
- `'any'` — tạo một instance riêng cho mỗi module/token (bao gồm lazy-loaded modules).

---

## 5. Pipe
Pipe dùng để biến đổi dữ liệu trong template.

- name: string  
  Tên pipe để dùng trong template (ví dụ `| date`).

- pure: boolean  
  - `true` (mặc định): pure pipe — `transform` chỉ được gọi khi tham số vào thay đổi (so sánh bằng reference/value tuỳ loại).
  - `false`: impure pipe — `transform` sẽ được gọi ở mỗi chu kỳ change detection (dù tham số không thay đổi).

Ví dụ tạo custom pipe:
```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'mo',
})
export class MoPipe implements PipeTransform {
  transform(name: string, id: number): any {
    // xử lý và trả về kết quả
  }
}
```

Sử dụng trong component:
```ts
@Component({
  selector: 'mo-dir',
  template: `
    <p>{{ name | mo: id }}</p>
  `
})
class BankAccount {
  name: string = "deepthan";
  id: number = 1;
}
```

---