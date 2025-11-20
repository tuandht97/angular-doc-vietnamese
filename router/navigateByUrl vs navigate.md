# Angular — navigateByUrl vs navigate (Chuyển hướng Router)

So sánh hai cách điều hướng trong Angular: router.navigateByUrl() và router.navigate().

Ví dụ import:
```ts
import { Router, ActivatedRoute } from '@angular/router';

export class Xxx {
  constructor(private router: Router, private route: ActivatedRoute) {}
}
```

## 1. Khác nhau

### 1.1 navigateByUrl()
Chữ ký:
```ts
navigateByUrl(url: string | UrlTree, extras: NavigationExtras = { skipLocationChange: false }): Promise<boolean>
```
- Tham số đầu tiên phải là một chuỗi đường dẫn tuyệt đối (hoặc một UrlTree).
- Ví dụ:
```ts
this.router.navigateByUrl('/home');
```

### 1.2 navigate()
Chữ ký:
```ts
navigate(commands: any[], extras: NavigationExtras = { skipLocationChange: false }): Promise<boolean>
```
- Tham số đầu tiên là một mảng (commands).
- Ví dụ:
```ts
this.router.navigate(['home', 'demo']); // => http://localhost:4200/home/demo
```
- Có thể điều hướng tương đối so với route hiện tại bằng option `relativeTo`:
```ts
this.router.navigate(['demo'], { relativeTo: this.route });
// nếu đang ở /home => sẽ tới /home/demo
```
- Lưu ý: nếu phần tử mảng bắt đầu bằng '/' (ví dụ '/demo') thì sẽ coi đó là đường dẫn tuyệt đối từ gốc, không còn là tương đối nữa.
- Nếu không truyền `relativeTo`, mặc định navigate coi mảng là từ route gốc (root).

## 2. Giống nhau
Cả hai đều nhận `NavigationExtras` để truyền thêm tuỳ chọn:

```ts
interface NavigationExtras {
  relativeTo?: ActivatedRoute | null;
  queryParams?: Params | null;
  fragment?: string;
  preserveQueryParams?: boolean;
  queryParamsHandling?: QueryParamsHandling | null;
  preserveFragment?: boolean;
  skipLocationChange?: boolean;
  replaceUrl?: boolean;
  state?: { [k: string]: any };
}
```

### 2.1 Cách truyền tham số

- Truyền queryParams (hiện trên URL):  
  Ví dụ A (gửi):
  ```ts
  this.router.navigate(['demo'], { queryParams: { id: 1 }, relativeTo: this.route });
  ```
  Ví dụ B (nhận):
  - Nếu dùng route param (path như `/user/:id`) -> đọc bằng `activatedRoute.params`:
    ```ts
    this.activatedRoute.params.subscribe(param => {
      console.log('params', param); // { id: 1 }
    });
    ```
  - Nếu dùng query string (`/user?id=1`) -> đọc bằng `activatedRoute.queryParams`:
    ```ts
    this.activatedRoute.queryParams.subscribe(param => {
      console.log('queryParams', param); // { id: 1 }
    });
    ```

- Truyền state (lưu trong history, không xuất hiện trên URL):  
  Ví dụ gửi:
  ```ts
  import { Router, NavigationExtras } from '@angular/router';

  const navigationExtras: NavigationExtras = { state: { id: 1 } };
  this.router.navigate(['b'], navigationExtras);
  ```
  Ví dụ nhận (trong component đích):
  ```ts
  import { Router } from '@angular/router';

  constructor(private router: Router) {
    const navigation = this.router.getCurrentNavigation();
    const state = navigation?.extras?.state as { id: number };
    console.log(state?.id); // giá trị truyền từ component gửi
  }
  ```

### 2.2 Cả hai đều trả về Promise
Bạn có thể dùng `.then()` để nhận callback:
```ts
this.router.navigate(['demo']).then(nav => {
  console.log(nav); // true: chuyển hướng thành công, false: thất bại
}, err => {
  console.error(err);
});
```
