# Sử dụng ngClass — thay đổi class theo điều kiện

Ví dụ CSS:
```css
.class1 { color: red; }
.class2 { width: 10px; }
.class3 { height: 10px; }
.class4 { opacity: .5; }
```

## 1. Truyền một chuỗi
Gán trực tiếp tên class (các class cách nhau bằng dấu cách):
```html
<p [ngClass]="'class1 class2'">Nội dung</p>
```

## 2. Truyền một object
Key là tên class, value là biểu thức boolean — nếu true sẽ thêm class, nếu false sẽ bỏ class:
```html
<p [ngClass]="{ 'class1': true, 'class2': false, 'class3': showTitle }">Nội dung</p>
```
```ts
showTitle = true;
```
Trong ví dụ trên, `class1` và `class3` sẽ được thêm (vì `showTitle === true`), `class2` thì không.

## 3. Truyền một mảng
Danh sách các class dưới dạng mảng:
```html
<p [ngClass]="['class1', 'class2']">Nội dung</p>
```

Bạn có thể dùng toán tử 3 ngôi để chọn class theo điều kiện:
```html
<p [ngClass]="[ showDetail ? 'class1' : 'class2', showTitle ? 'class3' : 'class4' ]">Nội dung</p>
```
```ts
showDetail = true;
showTitle = false;
```
Trong ví dụ, `class1` và `class4` sẽ được áp dụng.

## Các cách "hoa mỹ" khác để thêm class

### 1. Host binding trong component (thêm class lên phần tử gốc của component)
Bạn có thể khai báo trong decorator `@Component`:
```ts
@Component({
  selector: 'p-demo',
  host: {
    '[class.yourClassName]': 'true',            // luôn thêm yourClassName
    '[class.yourClassName2]': 'showDetail'     // thêm yourClassName2 khi showDetail === true
  }
})
export class PDemoComponent {
  showDetail = true;
}
```

### 2. Dùng binding trên HTML với cú pháp `[class.xxx]`
Gán một class cụ thể theo biến boolean:
```html
<p [class.yourClassName]="showDetail">Nội dung</p>
```
```ts
showDetail = true;
```

## Gợi ý sử dụng
- Dùng object khi muốn bật/tắt nhiều class theo các điều kiện rời rạc.
- Dùng mảng hoặc chuỗi khi bạn chỉ muốn áp dụng danh sách class tĩnh hoặc kết hợp điều kiện đơn giản.
- Tránh gán class trực tiếp bằng thao tác DOM nếu có thể — ưu tiên dùng binding của Angular để giữ reactivity và dễ bảo trì.
