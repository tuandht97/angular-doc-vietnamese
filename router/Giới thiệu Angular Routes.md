# Giới thiệu Angular Routes

Routes là một tập cấu hình các route. Kiểu dữ liệu:
```ts
export declare type Routes = Route[];

export interface Route {
    path?: string;
    pathMatch?: string;
    matcher?: UrlMatcher;
    component?: Type<any>;
    redirectTo?: string;
    outlet?: string;
    canActivate?: any[];
    canActivateChild?: any[];
    canDeactivate?: any[];
    canLoad?: any[];
    data?: Data;
    resolve?: ResolveData;
    children?: Routes;
    loadChildren?: LoadChildren;
    loadComponent?: LoadComponent;
    runGuardsAndResolvers?: RunGuardsAndResolvers;
}
```

Các trường quan trọng:

- path: đường dẫn
```ts
path: 'signin'
```
`path` là chuỗi dùng để router so khớp với URL. Nếu `path` là `signin` thì URL tương ứng sẽ có `.../signin`.

- pathMatch: quy tắc so khớp đường dẫn
```ts
pathMatch: 'prefix' // mặc định
pathMatch: 'full'
```
`prefix` (tiền tố) — mặc định; router kiểm tra xem URL có chứa `path` hay không.  
`full` — yêu cầu URL phải khớp hoàn toàn với `path` để coi là trùng khớp.

- matcher: định nghĩa chiến lược so khớp tùy chỉnh, thay thế `path` và `pathMatch`.

- component: component cần render khi route khớp.

- redirectTo: địa chỉ route để redirect.

- outlet: cho phép một route tương ứng với nhiều outlet (nhiều router-outlet).
Ví dụ:
```ts
{ path: 'news', outlet: 'outlet1', component: Demo1Component },
{ path: 'news', outlet: 'outlet2', component: Demo2Component },
```
Trong template:
```html
<router-outlet name="outlet1"></router-outlet>
<router-outlet name="outlet2"></router-outlet>
```

- canActivate: guard kiểm soát xem có được phép vào route hay không.

- canActivateChild: guard kiểm soát child routes.

- canDeactivate: guard cho phép can thiệp trước khi rời route.

- canLoad: ngăn chặn lazy-loaded module được tải nếu không thỏa điều kiện.

- data: đối tượng tùy ý để gắn dữ liệu metadata cho route (ví dụ title, permissions...).

- resolve: resolver, dùng để resolve dữ liệu trước khi route được kích hoạt.

- children: khai báo các route con (nested routes).

- loadChildren: khai báo lazy-loaded module (kiểu string hoặc function trả về Promise/Module).

- runGuardsAndResolvers: cấu hình khi nào chạy guards và resolvers (ví dụ 'paramsChange', 'always', ...).

---

Ghi chú ngắn:
- Một declarative Routes đầy đủ cho phép bạn kiểm soát mạnh mẽ cách ứng dụng điều hướng, xác thực, preload/lazy-load và truyền dữ liệu trước khi render.
- Khi thiết kế routes, cân nhắc `pathMatch: 'full'` cho các redirect gốc (ví dụ redirect `''` tới `/home`) để tránh match không mong muốn.
- Dùng `data` và `resolve` để chuyển dữ liệu vào component một cách có tổ chức trước khi hiển thị.