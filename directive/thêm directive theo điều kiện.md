# Làm sao để quyết định có thêm directive theo điều kiện

Câu hỏi: Có một directive, và đôi khi mình muốn nó được thêm vào, đôi khi lại không — có thể truyền một giá trị boolean để quyết định được không?

Không thể. Không có cách trực tiếp để điều kiện hóa việc thêm/loại bỏ một directive bằng một biểu thức trong template. 

Thay vào đó, bạn tách thành hai phần: một phần có directive và một phần không có, rồi dùng điều kiện để hiển thị phần tương ứng.

Để tránh lặp mã, bạn có thể tách phần chung ra thành một ng-template:

```html
<ng-template #shared>
  <div>Đây là một nội dung dùng chung</div>
</ng-template>
```

Phần cần thêm directive:

```html
<div *ngIf="show" yourDirective>
  <ng-container *ngTemplateOutlet="shared"></ng-container>
</div>
```

Phần không cần thêm directive:

```html
<div *ngIf="!show">
  <ng-container *ngTemplateOutlet="shared"></ng-container>
</div>
```