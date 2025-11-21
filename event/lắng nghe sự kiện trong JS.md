# Thêm bộ lắng nghe sự kiện trong JS (Angular)

Tổng hợp các cách lắng nghe sự kiện trong Angular (HostListener, RxJS fromEvent, v.v.) cùng ví dụ.

## 1. HostListener lắng nghe sự kiện click
```typescript
import { HostListener } from '@angular/core';

export class XxxComponent {
  @HostListener('window:click', ['$event.target'])
  onClick(targetElement: any) {
    console.log('You clicked on', targetElement);
  }
}
```
- 'click' là tên sự kiện cần xử lý.
- Cú pháp (event) = "..." biểu thị lắng nghe sự kiện.
- `$event` hoặc `$event.target` truyền đối tượng sự kiện hoặc target vào hàm.

## 2. Lắng nghe scroll (dùng RxJS)
Dùng fromEvent để tạo Observable từ event và debounce để giảm tần suất xử lý.
```typescript
import { Subscription, fromEvent } from 'rxjs';
import { debounceTime } from 'rxjs/operators';

export class XxxComponent {
  scroll$: Subscription;

  constructor() {
    this.scroll$ = fromEvent(window, 'scroll').pipe(
      debounceTime(50)
    ).subscribe((event) => {
      this.onWindowScroll(event);
    });
  }

  onWindowScroll(event: Event) {
    console.log('event', event);
  }

  // nhớ unsubscribe khi component bị destroy để tránh rò rỉ
  ngOnDestroy() {
    this.scroll$.unsubscribe();
  }
}
```

## 3. Lắng nghe resize (kích thước cửa sổ thay đổi)
Cách đơn giản:
```typescript
fromEvent(window, 'resize').subscribe(evt => {
  // xử lý
});
```
Cách khác kết hợp HostListener + Subject để debounce / throttle:
```typescript
import { Subject } from 'rxjs';
import { debounceTime, throttleTime } from 'rxjs/operators';

resizeEvent$: Subject<Event> = new Subject<Event>();

@HostListener('window:resize', ['$event'])
onResize(event: Event) {
  this.resizeEvent$.next(event);
}

ngOnInit() {
  this.resizeEvent$
    .pipe(throttleTime(500), debounceTime(490))
    .subscribe(res => {
      console.log('resize', res);
    });
}
```

## 4. Lắng nghe chuột phải (contextmenu)
- `window:contextmenu` lắng nghe sự kiện chuột phải trên window.
- `contextmenu` lắng nghe trên phần tử cụ thể.
```typescript
import { HostListener } from '@angular/core';

@HostListener('window:contextmenu', ['$event'])
onListenerRightClick(event: MouseEvent) {
  event.preventDefault(); // chặn menu mặc định
  this.showRightMenu = false;
}
```

## 5. Lắng nghe paste
```typescript
@HostListener('paste', ['$event'])
onPaste(event: ClipboardEvent) {
  // xử lý dữ liệu dán
  const pasted = event.clipboardData?.getData('text') ?? '';
  console.log('pasted', pasted);
}
```

## Ghi chú chung
- Khi dùng fromEvent / subscription, luôn unsubscribe (ngOnDestroy) để tránh rò rỉ bộ nhớ.
- HostListener tiện lợi cho các event đơn giản, còn fromEvent + RxJS mạnh hơn khi cần debounce/throttle, kết hợp nhiều stream.
- Truyền `$event` vào handler khi cần thông tin chi tiết của sự kiện.
