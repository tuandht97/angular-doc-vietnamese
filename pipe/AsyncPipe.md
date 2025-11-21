# AsyncPipe

Cách dùng cơ bản
```
{{ obj_expression | async }}
```

AsyncPipe (dấu ống bất đồng bộ) sẽ lắng nghe một Observable hoặc Promise và trả về giá trị mới nhất mà nguồn đó phát ra. AsyncPipe giúp component tự động phát hiện thay đổi khi dữ liệu bất đồng bộ cập nhật. Khi component bị huỷ, AsyncPipe sẽ tự động huỷ đăng ký (unsubscribe) để tránh rò rỉ bộ nhớ.

- Hoạt động với:
  - Observable<T>
  - Promise<T>

- Lợi ích:
  - Tự động subscribe/unsubscribe.
  - Kết hợp tốt với change detection để cập nhật template khi dữ liệu đến.

Ví dụ với Promise:
```typescript
greeting: Promise<string> | null = null;

private resolve: Function | null = null;

this.greeting = new Promise<string>((resolve, reject) => {
  this.resolve = resolve;
});
```

Template:
```html
<span>{{ greeting | async }}</span>
```

Ví dụ với Observable (RxJS):
```typescript
items$: Observable<Item[]> = this.http.get<Item[]>('/api/items');
```

Template:
```html
<ul>
  <li *ngFor="let item of items$ | async">{{ item.name }}</li>
</ul>
```