# Các phương thức Router: Guard (bảo vệ route), chặn lazy-load, chặn pre-load, xác nhận khi rời trang

## Mô tả
Hướng dẫn này minh họa cách sử dụng các route guard trong Angular để:
- Chặn truy cập nếu người dùng chưa có quyền (ví dụ chưa đăng nhập),
- Chặn lazy-loaded module không được tải nếu bị từ chối,
- Chặn khi rời khỏi một route (canDeactivate) để hỏi người dùng xác nhận (ví dụ khi có thay đổi chưa lưu).

Cấu trúc ví dụ (sơ lược):
```
├─ shared
│   └─ service
│       └─ guard
│           ├─ authorization.provide.ts
│           ├─ can-visit.provide.ts
│           └─ can-deactivate.provide.ts
├─ tables
    ├─ manwang
    └─ gailun
```

----

## 1. Kiểm tra đăng nhập trong authorization.provide.ts
Ở đây giả sử chúng ta có 1 service Authorization để kiểm tra trạng thái đăng nhập:

```ts
@Injectable()
export class Authorization {
    ...
    public isLogin(): boolean {
       // Kiểm tra xem đã đăng nhập hay chưa; ví dụ giả định chưa đăng nhập
       return false;
    }
}
```

## 2. Định nghĩa Guard (can-visit.provide.ts) và đăng ký vào ShareModule
File `can-visit.provide.ts` (ví dụ) cài đặt `CanActivate` và `CanLoad` để cả việc truy cập route và việc lazy-load module đều có thể bị chặn:

```ts
import { Injectable } from '@angular/core';
import { CanActivate, CanLoad, ActivatedRouteSnapshot, RouterStateSnapshot, Route } from '@angular/router';
import { Observable } from 'rxjs/Observable';
import { Authorization } from '../authorization.service';

import { NzMessageService } from 'ng-zorro-antd';

@Injectable()
export class CanAuthProvide implements CanActivate, CanLoad  {

    constructor(private AuthSrv: Authorization, private msg: NzMessageService) {}

    check(): Observable<boolean> {
        return new Observable((observer) => {
            if (this.AuthSrv.isLogin()) {
                observer.next(true);
                observer.complete();
                return;
            }
            this.msg.error('Không có quyền');
            observer.next(false);
            observer.complete();
        });
    }

    canActivate(
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot): boolean | Observable<boolean> | Promise<boolean> {
        return this.check();
    }

    canLoad(route: Route): boolean | Observable<boolean> | Promise<boolean> {
        return this.check();
    }

}
```

Đăng ký các provider vào module chia sẻ (`share.module.ts`):

```ts
import { Authorization  } from './service/guard/authorization.provide.ts';
import { CanAuthProvide } from './service/guard/can-auth.provide';
import { CanLeaveProvide } from './service/guard/can-deactivate.provide';

const SHARE_SERVICE = [
  Authorization,
  CanAuthProvide,
  CanLeaveProvide
];

@NgModule({
    ...SHARE_SERVICE,
  ]
})
export class ShareModule {
  static forRoot(): ModuleWithProviders {
    return {
      ngModule: ShareModule
    };
  }
}
```

> Lưu ý: đảm bảo import đúng đường dẫn và thêm các provider vào `providers` của module tương ứng.

## 3. Cấu hình route để áp dụng guard, lazy-load và preloading
Ví dụ trong `app.module.ts`:

```ts
import { CanAuthProvide } from './share/service/guard/can-auth.provide';

export const ROUTER_CONFIG: Routes = [
  {
    path: 'lists',
    loadChildren: './tables/tables.module#TablesModule',  // lazy-load module tables
    data: { preload: true } , // dùng preloading (tùy cấu hình preloader)
    canActivate: [ CanAuthProvide ], // route guard khi truy cập
    canLoad: [ CanAuthProvide ]  // nếu guard trả false thì module lazy-load sẽ không được tải
  }
];
```

- canActivate: kiểm tra trước khi kích hoạt route (component có thể đã được tải).
- canLoad: kiểm tra trước khi lazy-loaded module được tải — nếu trả false, module sẽ không được tải về.

## Xác nhận khi rời trang (canDeactivate)
Muốn hỏi người dùng "Bạn có chắc muốn rời?" khi rời khỏi một component (ví dụ có form đã chỉnh sửa), dùng `CanDeactivate`.

Định nghĩa `can-deactivate.provide.ts` (ví dụ):

```ts
import { Injectable } from '@angular/core';
import { Router, CanDeactivate, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
import { NzModalService } from 'ng-zorro-antd';
import { Observable } from 'rxjs'

@Injectable()
export class CanLeaveProvide implements CanDeactivate<any> {
    constructor (private confirmSrv: NzModalService) {}

    canDeactivate(
        component: any,
        currentRoute: ActivatedRouteSnapshot,
        currentState: RouterStateSnapshot,
        nextState?: RouterStateSnapshot): boolean | Observable<boolean> | Promise<boolean> {
            if(component.isModified()){
                return new Observable((observer) => {
                    this.confirmSrv.confirm({
                        title: 'Bạn có chắc chắn muốn rời khỏi trang không？',
                        content: 'Bạn đã điền một phần vào biểu mẫu; việc rời đi sẽ làm mất thông tin bạn đã nhập。',
                        okText: 'Rời khỏi',
                        cancelText: 'Hủy',
                        onOk: () => {
                            observer.next(true);
                            observer.complete();
                        },
                        onCancel: () => {
                            observer.next(false);
                            observer.complete();
                        }
                    });
                });
            }else{
                return false;
            }
    }
}
```

- Component mà bạn muốn kiểm tra cần cung cấp method `isModified()` (hoặc tên khác) trả về true nếu có thay đổi chưa lưu.
- Nếu `isModified()` trả true, guard sẽ hiển thị hộp thoại xác nhận; trả về true để cho phép rời, false để hủy việc chuyển trang.

Ví dụ `ManWang` component:

```ts
export class ManWangComponent {
  isModified(){
    // Kiểm tra người dùng có sửa nội dung hay không; trả true nếu đã sửa
    return true;
  }
}
```

Cấu hình route để áp dụng `canDeactivate` (ví dụ `table.routes.ts`):

```ts
import { ManWang } from './manwang/manwang.component' ;
import { CanLeaveProvide } from '../share/service/guard/can-deactivate.provide';

export const tableRoutes = [
    {
        path: '',
        component: TablesComponent,
        children: [
            {
                path: 'ManWang',
                component: ManWang,
                canDeactivate: [ CanLeaveProvide ]
            }
        ]
    }
]
```

----

## Ghi chú và lưu ý
- `canLoad` chặn việc tải module khi lazy-loading — nếu guard trả false, module sẽ không được load về (tiết kiệm băng thông).
- `canActivate` chặn việc truy cập route ngay cả khi module đã được load.
- `canDeactivate` dùng để bảo vệ việc rời khỏi component (xử lý form chưa lưu, cảnh báo người dùng).
- Các guard có thể trả `boolean`, `Observable<boolean>` hoặc `Promise<boolean>` để hỗ trợ thao tác bất đồng bộ (ví dụ gọi API kiểm tra token).
- Khi sử dụng các modal/confirm bất đồng bộ trong guard, trả về một Observable/Promise và hoàn thành observer khi người dùng chọn.
- Đừng quên đăng ký provider của các guard/service trong module (hoặc cung cấp bằng `providedIn: 'root'` trong decorator `@Injectable`).
