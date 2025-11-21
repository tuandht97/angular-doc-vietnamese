# NgTemplateOutlet

## Ví dụ mã

```html
<ng-container *ngTemplateOutlet="eng; context: {$implicit: 'World', detail: 'Xin chào!'}"></ng-container>

<ng-template #eng let-data let-detail="detail"><span>Hello {{data}}! {{ detail }}</span></ng-template>
```

## Chức năng
Chèn một template được bọc bởi `ng-template` vào phần tử chủ (host element).

## Tham số

- ngTemplateOutlet: TemplateRef<any> | null  
  Tham số đầu tiên là template cần truyền vào.

- ngTemplateOutletContext: Object | null  
  Tham số thứ hai là dữ liệu được truyền vào ở dạng đối tượng, ví dụ:

```
{ $implicit: 'World', detail: 'Xin chào!' }
```

Nó là một đối tượng ngữ cảnh; giá trị `$implicit` có thể được lấy trong `ng-template` bằng `let-data` (trong đó `data` là tên do người dùng tự đặt). Các giá trị khác cần lấy bằng cú pháp `let-xxx="xxx"`, ví dụ `detail` được lấy bằng `let-detail="detail"` và sử dụng `{{ detail }}` trong template.

## Cách dùng một nội dung ở hai nơi trong mã

```html
<ng-container *ngIf="show">
  <ng-container *ngTemplateOutlet="contentTpl"></ng-container>
  deepthan
</ng-container>

<ng-container *ngIf="!show">
  <ng-container *ngTemplateOutlet="contentTpl"></ng-container>
  momomo
</ng-container>

<ng-template #contentTpl>Xin chào,</ng-template>
```