# Giải pháp khi vừa kéo (drag) vừa click cùng kích hoạt

Vấn đề: Khi kéo một phần tử (drag), sự kiện click vẫn có thể bị kích hoạt. Ta cần phân biệt đâu là click thật, đâu là hành vi kéo.

Nguyên lý:
- Click thông thường dùng `click`, nhưng để phân biệt kéo vs click ta nên dùng `mousedown` và `mouseup`.
- Khi kéo chuột sẽ phát sinh chuỗi sự kiện: `mousedown`, `mousemove`, `mouseup`.
- Ý tưởng: ghi lại vị trí chuột lúc `mousedown` và lúc `mouseup`. Nếu hai vị trí giống nhau (hoặc khác nhau trong một ngưỡng rất nhỏ) thì đó là click; nếu khác nhiều thì đó là kéo.

Ví dụ HTML:
```html
<div
  cdkDrag
  (mousedown)="onMousedown($event)"
  (mouseup)="onMouseup($event)"
>
</div>
```

Ví dụ TypeScript:
```ts
dowmPosi: number[] = [];
upPosi: number[] = [];

onMousedown(event: MouseEvent) {
  const { pageX, pageY } = event;
  this.dowmPosi.push(pageX, pageY);
}

onMouseup(event: MouseEvent) {
  const { pageX, pageY } = event;
  this.upPosi.push(pageX, pageY);

  // Dùng _.isEqual từ lodash để so sánh mảng [x,y]
  if (_.isEqual(this.dowmPosi, this.upPosi)) {
    // Đây là click, thực hiện logic click ở đây
  }

  this.resetMouseFlag();
}

resetMouseFlag() {
  this.dowmPosi = [];
  this.upPosi = [];
}
```

Ghi chú / cải tiến:
- So sánh vị trí tuyệt đối có thể quá chặt; bạn có thể so sánh khoảng cách Euclid giữa hai điểm và cho phép một ngưỡng (ví dụ <= 3px) để tránh trường hợp chuột rung nhẹ khi click.
- Nếu dùng cdkDrag (Angular CDK), bạn cũng có thể lắng nghe các sự kiện drag của CDK (ví dụ `cdkDragMoved`, `cdkDragStarted`, `cdkDragEnded`) để quyết định chính xác hơn.
- Nhớ tránh ghi đè nhiều lần vào mảng `dowmPosi/upPosi`; đơn giản hơn là lưu một object {x,y} cho mỗi lần mousedown/mouseup.