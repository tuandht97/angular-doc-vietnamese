# Angular sequence() — Cách dùng

sequence() định nghĩa một chuỗi (sequence) các bước animation sẽ chạy tuần tự.

```ts
function sequence(
  steps: AnimationMetadata[],
  options: AnimationOptions | null = null
): AnimationSequenceMetadata;
```

- steps: Mảng các bước của chuỗi animation. Mỗi bước có thể là các lệnh style() hoặc animate(). Lời gọi style() sẽ áp dụng ngay lập tức các style được cung cấp, trong khi animate() sẽ áp dụng style sau khoảng thời gian delay/timing được chỉ định.
- options: Tham số tuỳ chọn (xem AnimationOptions).

Ví dụ:
```ts
// Một chuỗi animation
sequence([
  style({ opacity: 0 }),
  animate("1s", style({ opacity: 1 }))
])
```

Ý nghĩa của "chuỗi":
- Chuỗi là một danh sách các animation [A, B, C] được thực thi tuần tự: A chạy xong mới tới B, B xong mới tới C.
- sequence thường dùng bên trong group() hoặc transition(); nó đảm bảo mỗi bước hoàn tất rồi mới tiếp tục bước tiếp theo.

Lưu ý:
- Khi bạn truyền một mảng animation trực tiếp vào transition, Angular mặc định coi đó là một sequence. Ví dụ `[ animate(500, style({...})), animate(500) ]` sẽ chạy tuần tự.
- So sánh với group(): cả hai đều là danh sách animation, nhưng sequence chạy tuần tự, còn group chạy song song.