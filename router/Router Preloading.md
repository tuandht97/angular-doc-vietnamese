# Router Preloading — Tải trước các module lazy-load khi rảnh

- Khái niệm preload chỉ xuất hiện khi bạn dùng lazy-loading.
- Preload = tải trước các module lazy sau khi ứng dụng chính đã ổn định.
- Lưu ý: ý tưởng là chờ "thời gian rảnh" rồi mới tải, không phải tải ngay khi đang render module chính.

## Vấn đề: preload xảy ra quá sớm (BAD)
Ví dụ cấu trúc:
```
├─ app.module.ts
├─ app.routes.ts
├─ shared
│   └─ service
│       └─ preview-load.ts
```

preview-load.ts (cách đơn giản, nhưng tải ngay lập tức):
```ts
import { Observable } from 'rxjs/Rx';
import { PreloadingStrategy, Route } from '@angular/router';

export class PreloadModules implements PreloadingStrategy {
    preload(route: Route, load: Function): Observable<any> {
        return route.data && route.data.preload ? load() : Observable.of(null);
    }
}
```

app.module.ts:
```ts
import { ROUTER_CONFIG } from './app.routes';
import { PreloadModules } from './share/service/preview-load';
...
@NgModule({
 imports: [
    RouterModule.forRoot(ROUTER_CONFIG, { preloadingStrategy: PreloadModules } )
 ],
 providers: [
    PreloadModules
 ],
```

app.routes.ts:
```ts
export const ROUTER_CONFIG: Routes = [
  ...
  { path: 'login', component: RoleLoginComponent },
  {
    path: 'lists',
    loadChildren: './tables/tables.module#TablesModule',
    data: { preload: true }
  }
];
```

Kết quả thực tế: trong ví dụ, khi trang chính còn đang render (chưa xong), module `tables` đã bị tải (JS đã bắt đầu tải). Điều này phá mục đích lazy-load (vì vẫn tải module chậm ngay cả khi phần chính chưa xong).

## Giải pháp: chỉ preload khi rảnh (GOOD)
Ý tưởng: dùng `requestIdleCallback` (nếu có) hoặc setTimeout ngoài Angular zone để trì hoãn việc preload cho đến khi trình duyệt rảnh.

preview-load.ts (tối ưu):
```ts
import { Observable } from 'rxjs/Rx';
import { PreloadingStrategy, Route } from '@angular/router';
import { Injectable, NgZone } from '@angular/core';

export function requestIdle(zone: NgZone) {
  const win: any = window;
  if (win.requestIdleCallback) {
    return (fn) => win.requestIdleCallback(fn);
  }
  return (fn) => zone.runOutsideAngular(() => win.setTimeout(fn, 10));
}

@Injectable()
export class PreloadModules implements PreloadingStrategy {

  constructor(private zone: NgZone) { }

  preload(route: Route, fn: () => Observable<any>): Observable<any> {
    requestIdle(this.zone)(fn);
    return Observable.of(null);
  }

}
```

Kết quả:
- Khi trang chính render xong (thời điểm trang hiển thị), module `tables` vẫn chưa được tải.
- Sau khi mọi thứ đã tải xong và trình duyệt rảnh, preload mới bắt đầu tải module `tables`.

## Ghi chú
- Cách này giữ được lợi ích của lazy-loading (tối ưu trải nghiệm ban đầu) đồng thời tận dụng thời gian rảnh để tải module khi cần, cải thiện UX cho truy cập sau đó.
- Nếu môi trường không hỗ trợ `requestIdleCallback`, dùng `setTimeout` ngoài Angular zone để tránh trigger change detection không cần thiết.
- Có thể tuỳ chỉnh logic: chỉ preload khi route có `data.preload === true` hoặc theo các tiêu chí khác (ưu tiên module nhỏ/ít quan trọng trước).