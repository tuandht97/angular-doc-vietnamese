# routerLink và routerLinkActive — truyền tham số động

Giả sử một kịch bản: bạn làm một menu, có những nút khi click sẽ hiện sub-items, một số nút thì click sẽ điều hướng đến route.  
Yêu cầu: nếu là link thì nhấp sẽ điều hướng, nếu không phải link thì không điều hướng.

Ví dụ TypeScript:
```ts
isLink: boolean = false;
```

HTML ban đầu:
```html
<div
  [routerLink]="isLink ? data.url : null"
  routerLinkActive="node-active">
</div>
```

CSS:
```css
.node-active {
  background: #006189;
}
```

Cách trên có thể điều hướng bình thường, nhưng khi giá trị `routerLink` là `null` thì phần tử vẫn có thể bị coi là “active” (kích hoạt). Do đó cần kiểm tra thêm `routerLinkActive`.

Một cách xử lý là gắn điều kiện cho `routerLink` và cho `routerLinkActive`:
```html
<div
  [routerLink]="isLink ? data.url : null"
  [routerLinkActive]="isLink ? 'node-active' : null">
</div>
```

(Ghi chú: bản gốc có câu `isLink ? null : data.url` — hãy chú ý thứ tự điều kiện sao cho phù hợp logic của bạn.)

Gợi ý bổ sung:
- Thay vì trả về `null` cho `[routerLink]`, bạn có thể hoàn thiện UI bằng cách thêm class hoặc disabled style khi không phải là link, ví dụ `[class.disabled]="!isLink"`.
- Nếu vẫn gặp trường hợp routerLinkActive đánh dấu sai, có thể dùng `[routerLinkActiveOptions]="{ exact: true }"` hoặc tự kiểm tra route hiện tại để set class active:
```html
<div
  [routerLink]="isLink ? data.url : null"
  [class.node-active]="isLink && isCurrentRoute(data.url)">
</div>
```
và định nghĩa `isCurrentRoute()` trong component để kiểm tra route hiện tại nếu cần.
