# ViewChild

### Chức năng
Dùng để lấy tham chiếu tới component hoặc phần tử DOM trong TypeScript, và có thể `chủ động gọi các phương thức trên component con`.

### Cách sử dụng
- Giả sử có một component con:

> Component con (son.component.ts)
```ts
import { Component } from "@angular/core";

@Component({
  selector: "son-comp",
  template: `<p>Con component</p>`,
})
export class SonComponent {
  play() {
    console.log("Đã gọi phương thức play() của component con");
  }
}
```

#### Cách 1 — Truy vấn theo class component (trong TypeScript của component cha)
> Component cha (parent.component.ts)
```ts
import { Component, ViewChild } from "@angular/core";
import { SonComponent } from "./son.component";

@Component({
  selector: "parent-comp",
  template: `<p (click)="click()">Click để gọi phương thức của component con</p>
             <son-comp></son-comp>`,
})
export class ParentComponent {
  @ViewChild(SonComponent) 
  sonRef!: SonComponent;

  click() {
    this.sonRef.play();
  }
}
```

#### Cách 2 — Truy vấn theo template reference (khai báo #ref trong HTML)
> Component cha (parent.component.ts)
```ts
import { Component, ViewChild } from "@angular/core";
import { SonComponent } from "./son.component";

@Component({
  selector: "parent-comp",
  template: `
    <son-comp #son></son-comp>
    <p (click)="click()">Click để gọi phương thức của component con</p>
  `,
})
export class ParentComponent {
  @ViewChild("son") 
  sonRef!: SonComponent;

  click() {
    this.sonRef.play();
  }
}
```

Lưu ý: để chắc chắn `@ViewChild` đã được khởi tạo, nên truy cập nó trong lifecycle hook `ngAfterViewInit()` thay vì constructor.

### Lấy phần tử DOM trong component con
- Ví dụ component con có template:
```ts
@Component({
  selector: 'demo',
  template: `
    <ul>
      <li></li>
    </ul>
  `
})
export class DemoComponent {}
```

- Trong template của component cha:
```html
<demo #demo></demo>
```

- Trong component cha, truy vấn thành phần DOM:
```ts
import { ViewChild, ElementRef, AfterViewInit } from '@angular/core';

export class ParentComponent implements AfterViewInit {
  @ViewChild('demo') demo!: ElementRef;

  ngAfterViewInit() {
    // ElementRef.nativeElement là phần tử DOM gốc của component được query
    const parentElement = this.demo.nativeElement;
    const firstChild = parentElement.children[0];
    const firstLi = parentElement.querySelector("li");
    console.log(parentElement, firstChild, firstLi);
  }
}
```

Lưu ý quan trọng:
- `@ViewChild` trả về:
  - instance component nếu bạn query bằng lớp component,
  - hoặc `ElementRef` nếu bạn query một phần tử DOM,
  - hoặc `TemplateRef`/`ViewContainerRef` tùy loại query.
- Việc truy cập phần tử hoặc instance thông qua `@ViewChild` an toàn thực hiện trong `ngAfterViewInit()` (hoặc muộn hơn), vì lúc đó view con đã được khởi tạo.