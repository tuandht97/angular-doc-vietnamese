# RouterLink — Chuyển hướng trong Angular

Hướng dẫn về cách sử dụng RouterLink để điều hướng trong Angular.

### Cú pháp nhận route (routerLink)

Inline:
```html
<div routerLink="home"></div>
```

Array binding:
```html
<div [routerLink]="['home']"></div>
```

### Bốn cách viết địa chỉ route
Cách hoạt động tương tự như đường dẫn file (tương đối/ tuyệt đối).

Giả sử hiện tại URL là:
```
http://localhost:4200/#/auth/login
```
và ta muốn chuyển đến `home`.

- `home`  
  Kết quả:
  ```
  http://localhost:4200/#/auth/login/home
  ```
  (ghép vào route hiện tại)

- `./home`  
  Kết quả giống `home`:
  ```
  http://localhost:4200/#/auth/login/home
  ```

- `/home` (đường dẫn tuyệt đối)  
  Kết quả:
  ```
  http://localhost:4200/#/home
  ```
  Khi bắt đầu bằng `/` thì Angular coi đó là đường dẫn tuyệt đối (sau phần hash nếu dùng hash location).

- `../home` (lên 1 cấp rồi vào home)  
  Kết quả:
  ```
  http://localhost:4200/#/auth/home
  ```

### Dynamic route (route động)
Ví dụ định nghĩa route có tham số `id`:
```ts
const routes: Routes = [
  {
    path: "user/:id/info",
    component: DemoComponent
  }
];
```

Trong template bạn truyền id động:
```html
<div [routerLink]="['/user', id, 'info']">Xem thông tin</div>
```
`id` là biến trong file .ts của component.

### Truyền tham số khi điều hướng

1) Thêm tham số vào URL (queryParams)
URL ví dụ:
```
http://localhost:4200/auth/login?id=1
```

Template:
```html
<div routerLink="home" [queryParams]="{id: 1}">Truyền queryParams</div>
```

2) Truyền trạng thái qua history (state)
Dữ liệu sẽ không hiện trên URL, phù hợp khi không muốn lộ tham số hoặc khi dữ liệu lớn:
```html
<div routerLink="home" [state]="{id: 1}">Truyền state</div>
```

Trong component đích, bạn có thể lấy state từ Navigation extras:
```ts
import { Router } from '@angular/router';

constructor(private router: Router) {
  const navigation = this.router.getCurrentNavigation();
  const state = navigation?.extras?.state;
  console.log(state?.id);
}
```

--- 

Ghi chú:
- Dùng `routerLink` (hoặc `[routerLink]`) để tạo liên kết Angular-aware thay vì dùng `<a href="...">` tĩnh.
- Khi dùng HashLocationStrategy (URL chứa `#`), các ví dụ trên vẫn áp dụng — chú ý vị trí kết hợp với phần hash.