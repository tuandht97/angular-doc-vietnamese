# Angular keyframes() — Cách dùng

keyframes() dùng để định nghĩa các khung quan trọng (keyframes) cho một animation.

```ts
function keyframes(
  steps: AnimationStyleMetadata[]
): AnimationKeyframesSequenceMetadata;
```

- Tham số là một mảng các style() — mỗi phần tử biểu diễn một bước (keyframe) trong chuỗi animation.
- Mỗi style trong keyframes có thể kèm thuộc tính offset (tương đương phần trăm trong CSS @keyframes) — offset là số trong khoảng 0..1, biểu thị vị trí thời gian của keyframe:
  - offset: 0 nghĩa 0%
  - offset: 1 nghĩa 100%
  - offset: 0.2 nghĩa 20% thời lượng animation

Ví dụ với offset rõ ràng:
```ts
animate("5s", keyframes([
  style({ backgroundColor: "red", offset: 0 }),    // 0%
  style({ backgroundColor: "blue", offset: 0.2 }), // 20%
  style({ backgroundColor: "orange", offset: 0.3 }), // 30%
  style({ backgroundColor: "black", offset: 1 })   // 100%
]))
```

Nếu không cung cấp offset, Angular sẽ tự động phân bố offset theo thứ tự các bước:
```ts
animate("5s", keyframes([
  style({ backgroundColor: "red" }),    // offset = 0
  style({ backgroundColor: "blue" }),   // offset ≈ 0.33
  style({ backgroundColor: "orange" }), // offset ≈ 0.66
  style({ backgroundColor: "black" })   // offset = 1
]))
```

Sử dụng keyframes thường kết hợp với animate() trong transition hoặc trigger để tạo các animation phức tạp, tương tự như CSS keyframes nhưng khai báo trong metadata animation của Angular.