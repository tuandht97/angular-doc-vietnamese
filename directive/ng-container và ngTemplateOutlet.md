# ng-container vs ngTemplateOutlet

`<ng-container>` là một phần tử nhóm (grouping element) không làm ảnh hưởng đến kiểu hoặc bố cục; Angular sẽ không đưa nó vào DOM.

### Kết hợp với structural directive
Ví dụ `ngFor`

```html
<ng-container *ngFor="let book of books">
    <div> {{ book }} </div>
</ng-container>
```

### Kết hợp với ngTemplateOutlet

ngTemplateOutlet có thể chèn các phần tử DOM được bao bọc bởi `ng-template` vào vị trí khác, và có thể truyền tham số vào.

```html
<ng-template #cat >
  <div>Mèo</div>
</ng-template>

<ng-container *ngTemplateOutlet="cat"> </ng-container>
```

**Vậy làm sao để truyền tham số? Sử dụng `context`**~

ts định nghĩa biến tham số

```typescript
age = '2 tuổi';
height: "10cm";
```

html truyền tham số

```html
<ng-container
    *ngTemplateOutlet="cat; context: { $implicit: age, height: height }">
</ng-container>

<ng-template #cat let-data let-height='height'>
  <div>Mèo</div>
  {{ data }} 
  {{ height }}
</ng-template>
```

`context` nhận một đối tượng, ví dụ `context: { $implicit: age }`. `$implicit` truyền giá trị mặc định; trong `ng-template` dùng `let-data` để nhận, biến `data` chính là giá trị được truyền — đây là cú pháp cố định.

Bạn cũng có thể truyền nhiều tham số hơn, dạng `context: { height: height }`, về bản chất là `{ key: parameter }`. Trong `ng-template` lấy giá trị bằng `let-<biến>=<key>`; biến đó sẽ chứa tham số đã truyền.