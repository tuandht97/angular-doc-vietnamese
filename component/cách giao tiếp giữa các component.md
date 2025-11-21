# Tổng hợp các cách giao tiếp giữa các component trong Angular

## 1. @Input / @Output
> Phù hợp cho các component có quan hệ gần (cha - con).

Ví dụ — Component cha:
```ts
@Component({
  selector: 'parent',
  template: `
    <son [name]="name" (sendName)="sendName($event)"></son>
  `,
})
export class ParentComponent {
  name = "momomo";

  sendName(newName: string) {
    // newName là dữ liệu nhận từ component con
  }
}
```

Ví dụ — Component con:
```ts
import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'son',
  template: `
   <p>{{ name }}</p>
   <button (click)="modifyName()">Change</button>
  `,
})
export class SonComponent {
  @Input() name: string;
  @Output() sendName = new EventEmitter<string>();

  modifyName() {
    this.sendName.emit('deepthan');
  }
}
```

Ghi chú: trong `(sendName)="sendName($event)"` thì `$event` là tên đặc biệt dùng để lấy payload từ EventEmitter.

---

## 2. Dùng service (Subject / Observable)
> Phù hợp cho giao tiếp giữa các component không có mối quan hệ trực tiếp (cách xa nhau trong cây component).

Tạo một service làm kênh trung gian:
```ts
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable()
export class NameService {
  name$ = new Subject<any>();

  sendName(name: string) {
    this.name$.next(name);
  }
}
```

Component A (gửi dữ liệu):
```ts
import { NameService } from 'xxx';

export class AComponent {
  constructor(private nameSrv: NameService) {
    this.nameSrv.sendName('momomo');
  }
}
```

Component B (nhận dữ liệu):
```ts
import { Subscription } from 'rxjs';
import { NameService } from 'xxx';

export class BComponent implements OnDestroy {
  nameSub: Subscription;

  constructor(private nameSrv: NameService) {
    this.nameSub = this.nameSrv.name$.subscribe((name) => {
      // Xử lý dữ liệu nhận được
    });
  }

  ngOnDestroy() {
    this.nameSub.unsubscribe();
  }
}
```

Ghi chú quan trọng: `luôn hủy (unsubscribe) subscriptions khi component bị hủy để tránh memory leak`.
