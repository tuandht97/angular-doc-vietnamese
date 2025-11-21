# ng-content

## Nguyên lý
`ng-content` là một "slot" (ô cắm) cho phép truyền DOM từ component cha vào component con. Tương tự như `slot` trong Vue — nội dung bên trong thẻ component cha sẽ được chèn vào vị trí có `ng-content` trong component con.

Nội dung bên trong `ng-content` không được render ngay lập tức; chỉ khi component con có `ng-content` hoặc khi directive/logic khác chủ động chèn template thì nội dung mới xuất hiện.

---

## Ví dụ cơ bản — truyền trực tiếp

Giả sử component cha chứa một đoạn text "Xin chào" và truyền vào component con.

Parent (cha) — template:
```typescript
@Component({
  selector: 'parent',
  template: `
    <son> <p>Xin chào</p> </son>
  `,
})
export class ParentComponent {}
```

Son (con) — template:
```typescript
@Component({
  selector: 'son',
  template: `
    <ng-content></ng-content>
  `,
})
export class SonComponent {}
```

Kết quả render của `son` sẽ chèn `<p>Xin chào</p>` vào vị trí của `<ng-content>`:
```html
<p>Xin chào</p>
```

---

## Chọn nội dung truyền vào (`select`)
Bạn có thể định tuyến nội dung truyền vào bằng selector (tương tự querySelector) — Angular sẽ chèn các node phù hợp vào `ng-content` tương ứng.

1. Chọn theo thẻ (element)
Parent:
```html
<son>
  <p>Không được chọn</p>
  <p cat>Con mèo</p>
</son>
```
Son:
```html
@Component({
  selector: 'son',
  template: `
    <ng-content select="p"></ng-content>
  `,
})
export class SonComponent {}
```
`select="p"` sẽ chèn các thẻ `<p>`.

2. Chọn theo attribute
Parent:
```html
<son>
  <p cat>Mèo</p>
  <p>Chó</p>
</son>
```
Son:
```html
<ng-content select="[cat]"></ng-content>
```
`select="[cat]"` chỉ chèn các phần tử có attribute `cat`.

3. Chọn theo class
Parent:
```html
<son>
  <p class="cat">Mèo</p>
  <p>Chó</p>
</son>
```
Son:
```html
<ng-content select=".cat"></ng-content>
```

4. Chọn theo id
Parent:
```html
<son>
  <p id="cat">Mèo</p>
</son>
```
Son:
```html
<ng-content select="#cat"></ng-content>
```

---

## Ghi chú và lưu ý
- Nếu component con có nhiều `ng-content` với các `select`, Angular sẽ chèn các node phù hợp vào từng `ng-content` tương ứng; các node không khớp selector sẽ bị bỏ qua (hoặc chèn vào `ng-content` không có `select` nếu tồn tại).
- `ng-content` hữu ích khi bạn muốn tách giao diện và cho phép cha cung cấp nội dung tùy biến vào vị trí do con định nghĩa.
- Bạn cũng có thể kết hợp `ng-content` với `@ContentChild` / `@ContentChildren` trong component con để truy xuất và thao tác nội dung được truyền vào từ code TypeScript.