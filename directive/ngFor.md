# Sử dụng *ngFor

## Cách viết ngắn gọn

```html
<div *ngFor="let book of books;
    let i = index;
    let isFirst = first;
    let isLast = last;
    let isEven = even;
    let isOdd = odd;
    trackBy: trackByFunction">
</div>
```

- `let book of books`: lặp qua mảng `books`, mỗi phần tử gán cho `book`.  
- `let i = index`: `index` là chỉ số hiện tại, gán cho biến `i`.  
- `first`: có phải phần tử đầu tiên hay không (gán cho `isFirst`).  
- `last`: có phải phần tử cuối cùng hay không.  
- `even`: có phải phần tử chẵn hay không.  
- `odd`: có phải phần tử lẻ hay không.

`trackByFunction` là một hàm có hai tham số: index và phần tử hiện tại; hàm phải trả về một giá trị duy nhất (thường là id). Angular dùng giá trị này để so sánh và quyết định phần tử nào cần tái render — giúp nâng cao hiệu năng.

```ts
trackByFunction(index, book) {
  return book.id;
}
```

Nếu `id` không thay đổi thì Angular sẽ giữ phần tử DOM đó, tránh render lại không cần thiết.

## Cách viết khác

Bạn có thể dùng cú pháp `as` để gán tên cho các biến nội tại:

```html
<div *ngFor="let book of books;
    index as i;
    first as isFirst;
    last as isLast;
    even as isEven;
    odd as isOdd;">
</div>
```

Hoặc viết bằng `ng-template` (chỉ dùng được trên `ng-template`):

```html
<ng-template ngFor let-book [ngForOf]="books" let-i="index">
  {{ book }}
</ng-template>
```

Ghi chú: cách dùng `ng-template` hữu ích khi bạn cần linh hoạt hơn trong cấu trúc template hoặc khi muốn tách logic template ra ngoài.