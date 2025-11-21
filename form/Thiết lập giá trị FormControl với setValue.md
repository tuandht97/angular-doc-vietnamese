# Thiết lập giá trị FormControl với setValue
Phương thức này thiết lập giá trị mới cho control form.
```ts
setValue(value: any, 
    options: { 
        onlySelf?: boolean; 
        emitEvent?: boolean; 
        emitModelToViewChange?: boolean; 
        emitViewToModelChange?: boolean; 
    } = {}
): void
```
Các tùy chọn này xác định cách thức lan truyền thay đổi và phát sự kiện khi giá trị thay đổi. Các tùy chọn này được truyền đến phương thức `updateValueAndValidity`.

- onlySelf: Nếu là `true`, mỗi thay đổi chỉ ảnh hưởng đến chính control này, không ảnh hưởng đến control cha. Mặc định là `false`.

- emitEvent: Nếu là `true` hoặc không được cung cấp (mặc định), các Observable statusChanges và valueChanges sẽ phát ra sự kiện với trạng thái và giá trị mới nhất khi giá trị control thay đổi. Nếu là `false`, sẽ không có sự kiện nào được phát ra.

- emitModelToViewChange: Nếu là `true` hoặc không được cung cấp (mặc định), mỗi thay đổi sẽ kích hoạt sự kiện onChange để cập nhật view.

- emitViewToModelChange: Nếu là `true` hoặc không được cung cấp (mặc định), mỗi thay đổi sẽ kích hoạt sự kiện ngModelChange để cập nhật model. Khi gán giá trị cho dropdown mà không muốn kích hoạt ngModelChange, có thể đặt tùy chọn này thành `false`.