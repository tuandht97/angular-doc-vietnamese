# Angular — Cách chủ động huỷ (destroy) một component

Nếu bạn trực tiếp xoá phần tử DOM bằng Element.remove(), Angular sẽ không tự chạy lifecycle hook `ngOnDestroy()` cho component. Do đó nếu dùng cách này bạn phải tự gọi `ngOnDestroy()` nếu cần dọn dẹp tài nguyên.

Ví dụ (không khuyến nghị, chỉ minh hoạ):
```typescript
constructor(private element: ElementRef) {}

triggerDestroy() {
  // xoá phần tử DOM
  this.element.nativeElement.remove();

  // thủ công gọi ngOnDestroy để dọn dẹp (nếu component có logic cần thực hiện trong ngOnDestroy)
  this.ngOnDestroy();
}
```

Lưu ý và phương pháp khuyến nghị:
- Không nên chỉ xoá DOM bằng `remove()` nếu component được Angular quản lý — làm vậy có thể dẫn tới rò rỉ bộ nhớ nếu subscription / listener không được huỷ.
- Nếu component được tạo động bằng API (ví dụ `ViewContainerRef.createComponent()`), hãy giữ lại `ComponentRef` và gọi `componentRef.destroy()` — cách này sẽ đảm bảo lifecycle được thực thi đúng:
```typescript
// khi tạo động
const componentRef = viewContainerRef.createComponent(SomeComponent);

// khi muốn huỷ
componentRef.destroy(); // sẽ gọi ngOnDestroy() tự động và dọn dẹp
```
- Nếu bạn chèn component bằng `ViewContainerRef` (vd. `createComponent`), có thể dùng `viewContainerRef.remove(index)` hoặc `viewContainerRef.clear()` để huỷ các view/component do container tạo ra — Angular sẽ chạy lifecycle tương ứng.
- Chỉ dùng `element.nativeElement.remove()` khi bạn chắc chắn component không được Angular quản lý (ví dụ phần tử DOM thuần túy) và bạn tự đảm bảo dọn dẹp mọi subscription/handler.

Tóm lại: tránh tự xoá DOM rồi gọi `ngOnDestroy()` thủ công khi có thể — ưu tiên dùng `ComponentRef.destroy()` hoặc `ViewContainerRef` API để huỷ component theo kiểu "Angular-friendly".