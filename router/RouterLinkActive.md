# RouterLinkActive (always-on)

### RouterLinkActive là gì? Lợi ích ra sao?  
Khi làm một hệ thống quản trị (admin), thường có một menu điều hướng bên trái. Khi bạn nhấp vào mục, trình duyệt chuyển sang trang tương ứng — ta muốn phần tử DOM tương ứng được gán một class để hiển thị vị trí hiện tại. Tuy nhiên sau khi refresh trang, nếu chỉ dùng class tĩnh thì class đó sẽ mất.  

Giải pháp: dùng **RouterLinkActive** — khi route được kích hoạt, directive này sẽ tự động thêm class lên phần tử DOM chứa router; class chỉ bị gỡ khi URL thay đổi.  

> Một route "được kích hoạt" khi URL của trang khớp với router của phần tử DOM.

```ts
// Tóm tắt API
@Directive({
    selector: '[routerLinkActive]',
    exportAs: 'routerLinkActive'
})
class RouterLinkActive implements OnChanges, OnDestroy, AfterContentInit {
  constructor(router: Router, element: ElementRef, renderer: Renderer2, cdr: ChangeDetectorRef)
  links: QueryList<RouterLink>
  linksWithHrefs: QueryList<RouterLinkWithHref>
  get isActive: boolean
  routerLinkActiveOptions: {...}
  set routerLinkActive: string[] | string
  ngAfterContentInit(): void
  ngOnChanges(changes: SimpleChanges): void
  ngOnDestroy(): void
}
```

#### Ví dụ đơn giản

CSS:
```css
.red{
    color: red;
}
```

Template:
```html
<a routerLink="/user/login" routerLinkActive="red">login</a>
```

Khi URL là `.../user` hoặc `.../user/login`, thẻ `<a>` sẽ được thêm class `red`. Khi URL đổi sang route khác, class `red` sẽ bị loại bỏ.

#### Thêm nhiều class
Bạn có thể thêm nhiều class bằng cách:
```html
<a routerLink="/user/login" routerLinkActive="class1 class2">login</a>
```
hoặc dưới dạng binding:
```html
<a routerLink="/user/login" [routerLinkActive]="['class1','class2']">login</a>
```

#### Cấu hình `routerLinkActiveOptions`
Bạn có thể truyền options, ví dụ `{ exact: true }` để chỉ đánh dấu khi route khớp chính xác:
```html
<a routerLink="/user/login" routerLinkActive="red" [routerLinkActiveOptions]="{ exact: true }">login</a>
```

#### Kiểm tra trạng thái kích hoạt bằng `isActive`
Bạn có thể lấy instance của directive bằng `exportAs`:
```html
<a routerLink="/user/login" routerLinkActive #rla="routerLinkActive">
  login {{ rla.isActive ? 'đang kích hoạt' : 'không kích hoạt' }}
</a>
```
Nếu đang ở route kích hoạt sẽ hiển thị: `login đang kích hoạt`.

> `rla` là tên biến bạn tự đặt (alias), có thể đặt tên khác nếu muốn.

#### Trường hợp parent menu cũng muốn sáng khi con được chọn
Nếu bạn có menu có nhiều item con, muốn khi item con được chọn thì parent (ví dụ `<div>`) cũng được đánh dấu, đặt `routerLinkActive` trên phần tử cha:
```html
<div routerLinkActive="red" [routerLinkActiveOptions]="{ exact: true }">
  <a routerLink="/user/login">login</a>
  <a routerLink="/user/reset">reset</a>
</div>
```
- Khi route là `/user/login` hoặc `/user/reset`, phần tử cha sẽ được thêm class `red`.
- Lưu ý: nếu không dùng `exact: true`, khi URL là `/user` thì phần tử cha có thể vẫn bị gắn `red` (do khớp prefix). Dùng `{ exact: true }` để tránh trường hợp này.

#### Khi `routerLink` là biến
Bạn có thể bind biến:
```html
<a [routerLink]="temp">...</a>
```
Trong đó `temp` là biến trong component chứa đường dẫn hoặc mảng commands.

---

Gợi ý thêm:
- Nếu muốn kiểm soát tinh vi hơn, có thể dùng `[class.someClass]="condition"` hoặc kiểm tra route hiện tại trong component và set class thủ công.
- Đảm bảo sử dụng `routerLinkActiveOptions` phù hợp (`exact: true`/`false`) để tránh đánh dấu nhầm cho các route cha.
