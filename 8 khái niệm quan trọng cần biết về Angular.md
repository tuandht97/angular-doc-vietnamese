# 8 khái niệm quan trọng trong Angular

## Module
Module có hai nghĩa:
1. Code được tổ chức theo module (module vật lý)
2. Các đơn vị chức năng được tổ chức theo module (module logic)

Module vật lý là đặc tính module của TS/ES6 (tập tin).  
Module logic là cách đóng gói các component, directive, service rải rác trong ứng dụng theo chức năng.  

![image](assets\images\module.png)

_Trước hết, để Angular chạy được thành công cần tối thiểu phải định nghĩa một module, vì cần một module làm điểm khởi động cho ứng dụng — đó gọi là module gốc (root module)._  
_Khi ứng dụng phát triển, ta sẽ thêm các chức năng mới. Những chức năng mới này có thể đóng gói thành một module mới gọi là module tính năng (feature module). Khi có các feature module, chức năng trước đây đặt trong root module có thể tách ra vào các feature module để giữ cho root module ngắn gọn._  
_Sau này, nếu có thêm nhiều feature module hơn, ta có thể tách ra các component hoặc directive có chức năng chung thành một module độc lập (shared module). Module này về mặt logic không phải feature module._
_Cuối cùng còn có module lõi (core module). Ứng dụng thường có một số component hoặc service toàn cục chỉ cần khởi tạo một lần khi ứng dụng khởi động, ví dụ service quản lý thông tin đăng nhập, hoặc component header/footer chung. Thay vì đặt tất cả vào root module, thiết kế tốt hơn là tách chúng vào core module. Core module chỉ nên import vào root module, tránh import vào feature module hoặc shared module để giảm khả năng gây ra xung đột khi chạy ứng dụng._
    
Angular đã đóng gói sẵn một số module thường dùng, ví dụ:
> ApplicationModule: chứa các tiện ích liên quan khởi động ứng dụng;  
> CommonModule: chứa các directive và pipe nội bộ thường dùng;  
> BrowserModule: chứa các tiện ích cho chạy trên nền trình duyệt, đồng thời xuất ra cả CommonModule và ApplicationModule, vì vậy thường import BrowserModule là đủ;  
> FormsModule và ReactiveFormsModule: đóng gói các công cụ liên quan form;  
> RouterModule: đóng gói các component và directive liên quan routing;  
> HttpModule: đóng gói các service liên quan yêu cầu mạng.

Do đó, nếu muốn dùng ngIf và ngStyle, nhớ import CommonModule trước; các module khác tương tự.

## Component
Component gồm hai phần: @Component và một class.
Nếu chỉ định nghĩa một class thì Angular không biết cách hiểu class đó; khi gắn metadata cho class bằng decorator thì Angular biết coi class đó là component.

![image](/assets/images/component.png)

> Nếu muốn tìm hiểu cách metadata được gắn vào class, có thể nghiên cứu sâu về reflect-metadata (polyfill).

> Data binding phù hợp cho các component có quan hệ cấp (không quá xa); nếu cấp quá sâu hoặc ở các nhánh khác nhau thì thường dùng các cách khác, ví dụ dùng service làm trung gian.

## Template
Template của Angular dựa trên HTML; HTML bình thường cũng có thể dùng làm template.

```
@Component({
  template: `<p>deepthan</p>`
})
```
## Metadata
@Component là decorator; metadata chủ yếu được truyền vào tham số hàm của decorator. 
Selector và template đều là metadata.

Decorator về bản chất là một hàm tùy chỉnh; hàm xử lý các decorator của Angular nằm trong modules/@angular/core/src/util/decorators.ts.

## Data binding
Cả property binding và event binding đều được gọi chung là data binding.

1. Interpolation (chèn biểu thức)

    ```
    <p>Xin chào，{{data.name}}</p>
    ```
2. Property binding

    ```
    [deep] = 'data[0]'
    ```
3. Event binding

    ```
    (sendData) = getData(value)
    ```
4. Two-way binding (ràng buộc hai chiều)

    ```
    <input [(ngModel)='value']>
    ```
    [()] là cú pháp sugar để thực hiện two-way binding; ngModel là directive hỗ trợ two-way binding. Khi giá trị input thay đổi sẽ tự cập nhật vào value, và khi value trong class thay đổi cũng cập nhật lại input.

## Directive
Directive có thể tương tác linh hoạt với DOM, thay đổi style hoặc layout.
1. Structural directives: thêm/xóa/sửa DOM, ví dụ ngIf, ngFor

    ```
    <p *ngIf='bol==true'></p>
    ```
2. Attribute directives: thay đổi giao diện hoặc hành vi của element, ví dụ ngStyle, ngClass

    ```
    html:
    <p [ngStyle]='setStyles()' </p>
    ts:
    setStyles(){
        return {
            'color' : 'red'
        }
    }
    ```

## Service
Service là đơn vị đóng gói một chức năng riêng, giống thư viện tiện ích, thường được dùng trong component như phần mở rộng chức năng. Nó có thể là một chuỗi, JSON, hàm hoặc class — hầu hết đối tượng đều có thể đóng gói thành service. 

Ví dụ service thông báo log:

    // import statement
    @Injectable()
    export class LoggerService {
      private level: string;
      setLevel(level: string) { 
        this.level = level;
      }
      
      debug(msg: string) { }
      warn(msg: string) {  }
      error(msg: string) { }
    }

## Dependency Injection (DI)
Thông qua cơ chế DI, service và các module khác có thể được inject vào bất kỳ component (hoặc module hoặc service) nào mà developer không cần quan tâm cách chúng được khởi tạo. DI giúp quản lý phụ thuộc giữa các module.

Ví dụ:

    import {LoggerService} from './logger-service';
    // other import statement
    @Component({
      selector: 'contact',
      template: '...'
      providers: [LoggerService]
    })
    export class ContactListComponent {
      constructor(logger: LoggerService) { 
        logger.debug('xxx');
      }
    }

Metadata providers trong @Component là chìa khóa của DI — nó tạo một injector cho component và khởi tạo một instance LoggerService lưu trong injector đó. Khi component cần LoggerService, chỉ khai báo một tham số kiểu LoggerService trong constructor, Angular sẽ tự tìm instance trong injector và truyền vào khi khởi tạo component, nhờ đó component có thể sử dụng LoggerService.

## Các khái niệm khác:
> Angular kiểm tra xem giá trị của đối tượng có thay đổi hay không vào những thời điểm thích hợp; những thời điểm này không phải theo tần suất cố định mà thường là sau các sự kiện bất đồng bộ như thao tác người dùng (click), callback setTimeout hoặc XHR. Angular bắt các sự kiện bất đồng bộ này bằng thư viện Zones.