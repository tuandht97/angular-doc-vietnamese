# ngStyle

## Thêm biến vào ngStyle

Trong component TS:
```ts
pWidth = '100';
```

Trong template:
```html
<p [ngStyle]="{'width.px': pWidth}"></p>
```
Ở ví dụ trên, `width.px` sẽ lấy giá trị từ `pWidth` và áp dụng đơn vị `px`.

## Thêm thuộc tính tuỳ theo biến

Trong component TS:
```ts
showP = false;
```

Trong template:
```html
<p [ngStyle]="{ 'display': showP ? 'block' : 'none' }"></p>
```
Khi `showP` là `true` thì `display: block`, ngược lại `display: none`.

## Các cách "hoa mỹ" để thêm style

### 1. Trong TS (host binding) — thêm style lên phần tử gốc của component
```ts
@Component({
  selector: 'p-sample',
  host: {
    '[style.color]': `'red'`
  }
})
export class PSampleComponent {}
```
Cách này áp dụng style trực tiếp lên thẻ host của component.

### 2. Trong HTML — binding style cụ thể
```html
<p [style.color]="'red'"></p>
```
Hoặc gán theo biến:
```html
<p [style.color]="myColor"></p>
```

## Ghi chú
- `ngStyle` chấp nhận object: key là thuộc tính CSS (có thể kèm hậu tố như `.px`, `.em`), value là biểu thức trả về giá trị.
- Dùng binding `[style.xxx]` khi chỉ thay đổi một thuộc tính cụ thể; dùng `[ngStyle]` khi muốn áp dụng nhiều thuộc tính cùng lúc.
- Đảm bảo giá trị phù hợp với kiểu CSS mong muốn (chuỗi, số với hậu tố đơn vị nếu cần).