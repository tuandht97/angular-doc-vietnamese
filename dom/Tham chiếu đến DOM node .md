# Tham chiếu đến DOM node
```html
<input #user type='text'>
<button (click)='onClick(user.value)'></button>
```
Chúng ta thêm #user vào thẻ input, đây được gọi là tham chiếu.

Nó tham chiếu đến đối tượng input, vì vậy khi dùng `user.value` chúng ta sẽ lấy được giá trị trong input.
