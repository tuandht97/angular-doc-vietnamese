# Angular group() — Cách dùng

group() được dùng để nhóm nhiều bước animation và thực thi chúng cùng lúc (song song).

```ts
function group(
  steps: AnimationMetadata[],
  options: AnimationOptions | null = null
): AnimationGroupMetadata;
```

Tham số:
- steps: Mảng các bước animation. Mỗi bước có thể là style(), animate(), keyframes(), ...  
  Gọi style() sẽ áp dụng ngay kiểu, còn animate() sẽ áp dụng kiểu sau thời gian định sẵn.
- options: Tuỳ chọn bổ sung (xem AnimationOptions).

Mục đích:
- group là một danh sách các animation chạy song song — tất cả các bước trong group bắt đầu cùng lúc (về mặt ý nghĩa), mặc dù mỗi animate có thể có thời lượng và delay khác nhau để tạo hiệu ứng tuần tự nội bộ nếu cần.
- group thường dùng khi bạn muốn nhiều thuộc tính hoặc nhiều phần tử thay đổi đồng thời nhưng vẫn quản lý được toàn bộ như một khối.

Ví dụ:
```ts
group([
  animate("1s", style({ background: "black" })),
  animate("2s", style({ color: "white" }))
])
```
Trong ví dụ trên, cả hai animate sẽ bắt đầu cùng lúc; thuộc tính background hoàn tất sau 1s, thuộc tính color sau 2s.

Ghi chú so sánh với sequence():
- sequence() chạy các bước tuần tự (A rồi B rồi C).
- group() chạy các bước song song (A và B cùng chạy).  
Cả hai có thể lồng nhau và được dùng trong transition() hoặc trigger() để điều phối hành vi phức tạp.

Ứng dụng thực tế:
- Dùng group khi bạn muốn đồng bộ nhiều animation trên cùng phần tử hoặc nhiều phần tử con, ví dụ thay đổi vị trí, kích thước và opacity cùng lúc.
- Kết hợp group với query(), sequence(), keyframes() để tạo animation phong phú và có kiểm soát.