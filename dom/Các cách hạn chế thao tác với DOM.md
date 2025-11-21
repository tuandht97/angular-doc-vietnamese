# Trong Angular thực ra bạn không cần thao tác DOM trực tiếp

Yêu cầu: có một div, mình muốn động thêm/loại bỏ class hoặc thay đổi style của nó.

HTML ban đầu:
```html
<div class="old-style"></div>
```

CSS ban đầu:
```css
.old-style{
    width: 100px;
    height: 100px;
    background: #000;
}
```

Mục tiêu: thay đổi style thành
```css
.new-style{
    width: 200px;
    background: #069;
}
```

Các cách làm:

### Cách 1 — Thao tác DOM: xoá và thêm class (qua Renderer2)
Ý tưởng: lấy tham chiếu phần tử, dùng Renderer2 để removeClass/addClass.

HTML:
```html
<div class="old-style" #demo></div>
```

TypeScript:
```typescript
import { Component, ElementRef, ViewChild, Renderer2, OnInit } from '@angular/core';

@Component({...})
export class DemoComponent implements OnInit {
  @ViewChild('demo') demo: ElementRef;

  constructor(private renderer2: Renderer2) { }

  ngOnInit() {
    this.renderer2.removeClass(this.demo.nativeElement, 'old-style');
    this.renderer2.addClass(this.demo.nativeElement, 'new-style');
  }
}
```

Lưu ý: thường nên thao tác phần tử sau khi view khởi tạo xong (ngAfterViewInit) để chắc chắn @ViewChild đã có giá trị.

---

### Cách 2 — Thao tác DOM: setAttribute / setStyle (qua Renderer2)
Ý tưởng: đặt thuộc tính style mới cho phần tử.

HTML:
```html
<div class="old-style" #demo></div>
```

TypeScript:
```typescript
import { Component, ElementRef, ViewChild, Renderer2, OnInit } from '@angular/core';

@Component({...})
export class DemoComponent implements OnInit {
  @ViewChild('demo') demo: ElementRef;

  constructor(private renderer2: Renderer2) { }

  ngOnInit() {
    // Thay nhiều style cùng lúc
    this.renderer2.setAttribute(this.demo.nativeElement, 'style', 'width: 200px; background: #006699;');
    // Hoặc thay 1 style cụ thể:
    // this.renderer2.setStyle(this.demo.nativeElement, 'width', '200px');
    // this.renderer2.setStyle(this.demo.nativeElement, 'background', '#006699');
  }
}
```

---

### Cách 3 — Không thao tác DOM: dùng ngClass (khuyến nghị)
Ý tưởng: điều khiển class bằng binding — tận dụng trình bày (declarative) của Angular.

TypeScript:
```typescript
changeClass: boolean = false;

// Khi muốn đổi class, gán:
// this.changeClass = true;
```

HTML:
```html
<div [ngClass]="changeClass ? 'new-class' : 'old-class'"></div>
```

Khi `changeClass` là false → class là `old-class`; khi true → `new-class`.

---

### Cách 4 — Không thao tác DOM: dùng ngStyle
Ý tưởng: bind trực tiếp các thuộc tính style tới biến trong component.

TypeScript:
```typescript
widthTemp: string = '100px';
backgroundTemp: string = '#000';

// Khi muốn đổi:
// this.widthTemp = '200px';
// this.backgroundTemp = '#069';
```

HTML:
```html
<div [ngStyle]="{'background-color': backgroundTemp, 'width': widthTemp}"></div>
```

---

Tổng kết:
- Nên ưu tiên cách khai báo (ngClass / ngStyle) vì giữ code sạch, dễ test và không phụ thuộc trực tiếp vào DOM.
- Nếu bắt buộc phải thao tác DOM, dùng Renderer2 (không dùng trực tiếp nativeElement) để giữ khả năng tương thích nền tảng và an toàn.
- Thực hiện các thao tác trên @ViewChild sau khi view đã khởi tạo (ngAfterViewInit) để tránh lỗi lấy tham chiếu null.