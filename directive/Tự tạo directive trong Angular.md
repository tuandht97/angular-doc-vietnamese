# Tự tạo directive trong Angular

Ví dụ: Tạo hiệu ứng nhấp vào hình ảnh để phóng to toàn màn hình, nhấp lại để thu nhỏ.

Về cơ bản là thêm/loại bỏ class cho phần tử một cách động.

HTML
```html
<div imageEnlarged class="image-default">
  <div class="img-wrap">
    <img src="../../../../assets/images/demo.jpg" alt="demo">
  </div>
</div>
```

CSS
```css
/* Nhỏ */
.image-default {
  position: relative;
  width: 500px;
  margin: 2.4em 1em;
  img {
    width: 100%;
  }
  transition: all .3s ease-out;
}

/* Toàn màn hình, căn giữa ngang dọc */
.image-enlarged {
  height: 100%;
  width: 100%;
  background: #222222;

  position: fixed;
  z-index: 99999;
  left: 0;
  top: 0;

  display: table;
  overflow: hidden;

  transition: all .3s ease-in;

  .img-wrap {
    width: 100%;
    height: 100%;

    text-align: center;
    display: table-cell;
    vertical-align: middle;
  }
  img {
    max-height: 80%;
    max-width: 80%;
  }
}
```

Cách 1: thao tác class thông qua host
```typescript
import { Directive } from '@angular/core';

@Directive({
  selector: '[imageEnlarged]',
  host: {
    '[class.image-enlarged]': 'toggle',
    '[class.image-default]': '!toggle',
    '(click)': 'clickImg()'
  }
})
export class ImageDirective {
  toggle: boolean = false;
  clickImg() {
    this.toggle = !this.toggle;
  }
}
```

Cách 2: thao tác DOM bằng Renderer2
```typescript
import { Directive, ElementRef, Renderer2 } from '@angular/core';

@Directive({
  selector: '[imageEnlarged]',
  host: {
    '(click)': 'clickImg()'
  }
})
export class ImageDirective {
  toggle: boolean = false;
  classArr = ['image-enlarged', 'image-default'];

  constructor(private el: ElementRef, private renderer: Renderer2) {}

  clickImg() {
    this.toggle = !this.toggle;
    this.toggle
      ? this.toggleClass(this.classArr[0], this.classArr[1])
      : this.toggleClass(this.classArr[1], this.classArr[0]);
  }

  toggleClass(increase: string, decrease: string) {
    this.renderer.addClass(this.el.nativeElement, increase);
    this.renderer.removeClass(this.el.nativeElement, decrease);
  }
}
```

Cách truyền tham số vào directive

HTML
```html
<div myDirective [disabled]="true"></div>
```

Directive
```typescript
import { Directive, Input } from '@angular/core';

@Directive({
  selector: '[myDirective]',
})
export class BtnToggleStatusDirective {
  @Input() disabled: boolean;
}
```