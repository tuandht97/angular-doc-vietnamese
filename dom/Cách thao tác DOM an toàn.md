# Angular — Cách thao tác DOM an toàn

Lưu ý: Angular không khuyến khích truy cập DOM trực tiếp vì điều đó tạo sự phụ thuộc chặt giữa ứng dụng và lớp hiển thị, khiến ứng dụng khó chạy trên các nền tảng khác (ví dụ Web Worker). Sử dụng template, data-binding hoặc API trừu tượng như Renderer2 khi có thể. ElementRef được xem là "dùng khi bắt buộc" — hãy cẩn trọng.

## ElementRef — truy cập phần tử gốc
ElementRef bao bọc phần tử DOM gốc và có thuộc tính `nativeElement`. Thường dùng qua dependency injection.

Ví dụ:
```html
<!-- template -->
<div class="deep">Nội dung</div>
```

```typescript
import { Component, ElementRef, AfterViewInit } from '@angular/core';

@Component({...})
export class AppComponent implements AfterViewInit {
  constructor(private elementRef: ElementRef) {}

  ngAfterViewInit() {
    const divEle = this.elementRef.nativeElement.querySelector('.deep');
    console.log(divEle); // in ra phần tử <div class="deep">...</div>
  }
}
```

## @ViewChild — lấy tham chiếu tới phần tử/component con
Sử dụng #ref và @ViewChild để truy cập phần tử cụ thể rồi thay đổi style:

```html
<div #deep>My div</div>
```

```typescript
import { Component, ElementRef, ViewChild, AfterViewInit } from '@angular/core';

@Component({...})
export class AppComponent implements AfterViewInit {
  @ViewChild('deep') willChangeDiv!: ElementRef;

  ngAfterViewInit() {
    this.willChangeDiv.nativeElement.style.backgroundColor = 'red';
  }
}
```

> Tuy nhiên trực tiếp thao tác `nativeElement.style` vẫn là truy cập DOM trực tiếp — nên ưu tiên dùng Renderer2.

## Renderer2 — API an toàn để thao tác DOM
Renderer2 cung cấp API trừu tượng, hoạt động an toàn trên nhiều nền tảng và khuyến khích dùng thay vì truy cập nativeElement trực tiếp.

Ví dụ dùng Renderer2 để set style:
```html
<div #deep>My div</div>
```

```typescript
import { Component, ElementRef, ViewChild, AfterViewInit, Renderer2 } from '@angular/core';

@Component({...})
export class AppComponent implements AfterViewInit {
  @ViewChild('deep') willChangeDiv!: ElementRef;

  constructor(private renderer: Renderer2) {}

  ngAfterViewInit() {
    this.renderer.setStyle(this.willChangeDiv.nativeElement, 'backgroundColor', 'red');
  }
}
```

## Một số phương thức chính của Renderer2
Renderer2 cung cấp nhiều phương thức để tạo, thao tác và lắng nghe DOM mà không truy cập trực tiếp:
- data: lưu trữ tùy ý trên instance renderer
- destroy(): huỷ renderer
- createElement(name, namespace?)
- createComment(value)
- createText(value)
- appendChild(parent, newChild)
- insertBefore(parent, newChild, refChild)
- removeChild(parent, oldChild)
- selectRootElement(selectorOrNode)
- parentNode(node)
- nextSibling(node)
- setAttribute(el, name, value, namespace?)
- removeAttribute(el, name, namespace?)
- addClass(el, name)
- removeClass(el, name)
- setStyle(el, style, value, flags?)
- removeStyle(el, style, flags?)
- setProperty(el, name, value)
- setValue(node, value)
- listen(target, eventName, callback): trả về hàm huỷ listener

## Tóm tắt khuyến nghị
- Ưu tiên dùng: template + data-binding.
- Nếu cần thao tác DOM: sử dụng Renderer2 để giữ tính tương thích nền tảng.
- Dùng ElementRef/nativeElement chỉ khi không thể tránh được — và hạn chế phạm vi sử dụng.
- Khi thao tác DOM sau khi component view đã sẵn sàng, thực hiện trong ngAfterViewInit/ngAfterViewChecked tuỳ trường hợp.

