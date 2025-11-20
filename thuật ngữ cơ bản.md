# Giải thích (bằng ngôn ngữ dễ hiểu) các thuật ngữ Angular

**Ahead-of-time (AoT) — biên dịch trước (precompile)**   

Khi đóng gói dự án, biên dịch sẵn ứng dụng trước, sau khi đóng gói xong có thể khởi chạy luôn, thay vì đóng gói cả trình biên dịch vào ứng dụng rồi biên dịch khi chạy. Dùng cho môi trường production.

**Just-in-time (JiT) — biên dịch tại thời điểm chạy**   

Khởi động và biên dịch các component và module trong trình duyệt, ứng dụng chạy ở dạng động. Dùng trong quá trình phát triển.

**Directive — chỉ thị**   

Nói cho Angular biết cách tạo hoặc thay đổi phần tử HTML.  
Chia làm ba loại:
  - Attribute directive (chỉ thị thuộc tính)
  - Structural directive (chỉ thị cấu trúc)
  - Component (thực tế component cũng là một dạng directive)

**Attribute directive — chỉ thị thuộc tính**

Là các lệnh lắng nghe hoặc sửa đổi các phần tử HTML, attribute, property hoặc hành vi của component, thường dùng để thay đổi thuộc tính HTML (ví dụ style). Ví dụ: `ngClass`, `ngStyle`.

**Structural directive — chỉ thị cấu trúc**

Lắng nghe hoặc sửa đổi cấu trúc phần tử, xóa hoặc thêm DOM. Ví dụ `ngIf` , `ngFor`.

**Component — thành phần**   
Mọi thứ trên trang web đều có thể được coi là component.  
Một nút hay một bảng đều có thể là component; Component giống như một bộ phận của ô tô, được cấu thành từ nhiều vật liệu (HTML, CSS, JS...), và tự quản lý logic riêng của nó.

**Packaging barrel**

Là cách đóng gói một phần tập tin của component vào một file `index.ts` để xuất ra cho nơi khác dùng.

Ví dụ cấu trúc:
```
├─ login  
│  ├─ login.component.ts  
│  ├─ login.service.ts  
│  ├─ login.directive.ts  
│  ├─ index.ts  
...
```

`index.ts`:
```
export * from './login.component.ts';
export * from './login.service.ts';
```

Ở chỗ khác import:
```
import { loginComponent, LoginService } from '../login';
```
Khi import thư mục sẽ mặc định tìm file `index.ts` bên trong.

> Angular module cũng có thể xuất các component, service, directive cùng nhau.

**annotation**

Thực ra là đồng nghĩa với decoration.

**Binding**

Hầu như chỉ việc data binding — gắn thuộc tính của đối tượng dữ liệu vào thuộc tính của đối tượng HTML.  
Đôi khi cũng có ý chỉ việc “ràng buộc” giữa token (khóa) và provider trong dependency injection, nhưng cách dùng này ít gặp và thường được ghi rõ trong ngữ cảnh.

**Data binding**

Hiển thị dữ liệu từ backend lên giao diện và biến thao tác của người dùng thành dữ liệu.

**Bootstrap**

Bạn khởi chạy ứng dụng Angular bằng cách “bootstrap” nó với application root NgModule (`AppModule`).  
Quá trình bootstrap xác định component gốc (root component) của ứng dụng — component đầu tiên được tải. Có thể bootstrap nhiều ứng dụng trên cùng một `index.html`, mỗi ứng dụng có root component riêng.

**camelCase**

Chữ cái đầu của tên là chữ thường, chữ cái đầu của các từ tiếp theo viết hoa. `Dùng cho function, property, method`.

**PascalCase**  
Mỗi từ bắt đầu bằng chữ hoa. Thường `dùng cho tên class`.

**Quy ước đặt tên dash-case**

Dùng dấu gạch ngang (`-`) để phân tách từ. Thường dùng cho selector của directive/component (ví dụ `my-app`) và tên file (ví dụ `hero-list.component.ts`).

**snake_case**

Dùng dấu gạch dưới (`_`) để phân tách từ.

**Decorator**

Một cách “trang điểm” cho một thứ gì đó để Angular biết đó là gì. `@Component` nói Angular đây là component, `@Input` nói đây là thuộc tính đầu vào, `@Injectable` nói đây là service.

**Dependency Injection**

Những thứ cần thiết (dependency) được lấy từ `providers` mà không cần người dùng quan tâm cách khởi tạo.

**Injector**

Là một đối tượng trong hệ thống DI của Angular, có thể tìm một dependency trong cache của nó hoặc dùng provider đã đăng ký để tạo dependency đó.

**Provider**

Hệ thống DI dựa vào provider để tạo instance của dependency. Provider liên kết một lookup token với code (công thức) để tạo giá trị dependency.

**ECMAScript** 

Tức JavaScript, có nhiều phiên bản. Phiên bản JS mới nhất được phê duyệt thời điểm ghi bài là ECMAScript 2016 (ES2016 / ES7).

**ECMAScript 2015 (ES6) ** 

Gọi tắt là ES6 hoặc ES2015.

**ES5**

“ECMAScript 5”, phiên bản JS mà hầu hết trình duyệt hiện dùng.

**Input property**

Dữ liệu được truyền từ component khác vào; giá trị chảy từ bên phải của biểu thức trong template vào thuộc tính này. Thường dùng để truyền giữa component cha và con.

Ví dụ ở cha (template):
```html
<me [receiver]="'dữ liệu từ ngoài'"></me>
```

Ở component con:
```ts
@Component({
  selector: 'me',
  ...
})
...
@Input() receiver: string;

ngOnChanges() {
  console.log("Dữ liệu được truyền:", this.receiver); // in ra 'dữ liệu từ ngoài'
}
```

**Output property**

Truyền dữ liệu bằng cách phát (emit) một sự kiện tới component cha. Dòng dữ liệu chảy từ thuộc tính này ra template (qua `$event`).

Ở component con (ts):
```ts
@Output() sendHero: EventEmitter<any> = new EventEmitter();

ngOnInit() {
  this.sendHero.emit('timo');
}
```

Ở component cha (template + ts):  
html:
```html
<me (sendHero)="getHero($event)"></me>
```
ts:
```ts
getHero(hero: string) {
  console.log("Người được truyền là:", hero); // Người được truyền là timo
}
```

**Interpolation**

Chèn biến vào HTML.

ts:
```ts
public me = 'Angular';
```
html:
```html
<div>Tôi là {{me}}, Rất vui được gặp bạn。</div> <!-- Tôi là Angular, Rất vui được gặp bạn. -->
```

**Lifecycle hooks**

Các thời điểm khác nhau để làm việc gì đó. Các hook chính:
  - `ngOnChanges` — gọi khi giá trị của input/output binding thay đổi.
  - `ngOnInit` — gọi sau lần `ngOnChanges` đầu tiên.
  - `ngDoCheck` — thay đổi kiểm tra do developer tự định nghĩa.
  - `ngAfterContentInit` — gọi sau khi nội dung component được khởi tạo.
  - `ngAfterContentChecked` — gọi sau mỗi lần kiểm tra nội dung.
  - `ngAfterViewInit` — gọi sau khi view của component được khởi tạo.
  - `ngAfterViewChecked` — gọi sau mỗi lần kiểm tra view.
  - `ngOnDestroy` — gọi trước khi directive/component bị hủy.

**Module**

Module là một khối mã có tính tập trung, phục vụ một mục đích duy nhất. Như frontend và backend là hai module; nếu muốn giao tiếp thì qua interface (trong Angular là import/exports).

**Observable**

Dùng khi gọi API; biến dữ liệu bất đồng bộ thành một luồng dữ liệu. Được lấy từ RxJS — một thư viện bên thứ ba.

**Pipe**

Là hàm chuyển đổi dữ liệu, ví dụ từ dữ liệu thô sang hiển thị. Angular có pipe built-in như `DatePipe`, `UpperCasePipe`, `LowerCasePipe`, `CurrencyPipe`, `PercentPipe`. Cũng có thể tự định nghĩa pipe.

Ví dụ:
ts:
```ts
birthday = 1510156800000;
```
html:
```html
<p>{{ birthday | date:"yy/MM/dd" }}</p>
```
Kết quả:
```
2017/11/9
```

**Reactive forms**

Kỹ thuật xây dựng form trong Angular bằng mã trong component (khác với template-driven forms). Khi dùng reactive forms:
  - Component là nguồn (single source of truth). Validation được định nghĩa trong code của component.
  - Trong class dùng `new FormControl()` hoặc `FormBuilder` để tạo control rõ ràng.
  - Template không dùng `ngModel` cho các input.
  - Các directive liên quan bắt đầu bằng `Form` như `FormGroup`, `FormControl`, `FormControlName`.

Reactive forms mạnh và linh hoạt, phù hợp với các tình huống nhập dữ liệu phức tạp, tạo form động.

**Router**

Thông qua cấu hình route khác nhau, tải các component hoặc module khác nhau để ghép thành các trang.

**Routing component**

Là component có `router-outlet`. `router-outlet` giống ổ cắm — mỗi “lỗ” là một route, cắm “thiết bị” nào vào thì route đó sẽ hiển thị component tương ứng.

Ví dụ cấu trúc:
```
├─ list  
│ ├─ list.component.ts  
│ ├─ list.routes.ts  
│ ├─ lol  
│ │  ├─ lol.component.ts  // chứa html, css, ...
```

`list.component.ts`:
```ts
@Component({
  ...
  template: `
    <h1>Component có router-outlet</h1>
    <router-outlet></router-outlet>
  `
})
```

`list.routes.ts`:
```ts
import { ListComponent } from './list.component';
import { LolComponent } from './lol/lol.component';

export const listRoutes = [{
  path: 'list',
  component: ListComponent,
  children: [
    {
      path: 'lol',
      component: LolComponent
    }
  ]
}];
```
Khi khớp route `'list'`, Angular thấy trong HTML của `ListComponent` có `router-outlet`, rồi sẽ tìm child route phù hợp và chèn component con vào vị trí `router-outlet`.

**Scoped package**

Ý chỉ các package mã nguồn framework Angular được chia theo chức năng, mỗi package có phạm vi và nhiệm vụ riêng, thường bắt đầu bằng `@angular`, ví dụ:
  - `@angular/core` — core: change detection, DI, rendering...
  - `@angular/common` — các directive chung
  - `@angular/compiler` — công cụ biên dịch
  - `@angular/platform-browser`, `@angular/platform-browser-dynamic` — nền tảng trình duyệt; còn `@angular/platform-server` cho server-side rendering
  - `@angular/forms` — form
  - `@angular/http` — HTTP (yêu cầu mạng)
  - `@angular/router` — routing
  - `@angular/animations` — animation

Lợi ích chia package là không phải import nếu không dùng (ví dụ không cần animation thì không import `@angular/animations`).

Các package liên quan khác:
  - `core-js`: polyfill giúp tương thích các tính năng JS mới với các trình duyệt khác nhau.
  - `rxjs`: xử lý bất đồng bộ và kết hợp sự kiện, thường dùng cho dữ liệu từ API.
  - `zone.js`: thư viện giúp bắt các sự kiện bất đồng bộ của trình duyệt; cơ chế change detection của Angular dựa vào đây — bắt buộc phải import.

**Service**

Service dùng để đóng gói dữ liệu và logic không gắn với view cụ thể, hoặc dùng để chia sẻ dữ liệu/logic giữa các component. Thường dùng cho gọi API hoặc truyền dữ liệu.

**Template**
Thực chất là HTML.
```ts
@Component({
  template: `<div>Thực chất chỉ là HTML</div>`
})
```

**Template expression**

Là các biểu thức trong HTML. Angular thực thi biểu thức để lấy giá trị và gán cho thuộc tính binding (có thể là phần tử HTML, component hoặc directive).
```html
<div [property]="1+1"></div>
```

**Transpile**

Chuyển một dạng JavaScript (ví dụ TypeScript) sang một dạng JavaScript khác (ví dụ ES5).

**View**

Phần hiển thị trên màn hình dùng để hiển thị thông tin và phản hồi tương tác người dùng (click, di chuột, phím…).