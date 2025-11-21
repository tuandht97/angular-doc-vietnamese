# Angular Hot Module Replacement (CLI & webpack)

Webpack hỗ trợ hot module replacement (HMR) sẵn và rất dễ bật: thêm tham số --hot cho webpack-dev-server:

```bash
webpack-dev-server --port=4200 --hot
```

Tham số --hot sẽ thêm plugin HotModuleReplacementPlugin vào cấu hình webpack và bật HMR.

Nếu dùng Angular-CLI, có thể bật HMR cho dev server bằng tùy chọn --hmr:

```bash
ng serve --hmr
```

Tuy nhiên, bật HMR ở dev server thôi vẫn chưa đủ — ứng dụng Angular của bạn cần thêm vài xử lý để chấp nhận cập nhật. Thay đổi mã khởi tạo ứng dụng như sau:

```ts
declare var module: any;
if (module.hot) { // nếu webpack bật HMR
  // chấp nhận cập nhật module và ngăn sự kiện tiếp tục (theo tài liệu webpack)
  module.hot.accept();
}
platformBrowserDynamic().bootstrapModule(AppModule)
```

Với cấu hình này, khi chỉnh sửa component, trang sẽ không reload toàn bộ mà webpack sẽ thay module và render lại cây component (thay DOM).

Một vấn đề thường gặp: trạng thái dữ liệu lưu trong component sẽ mất khi module được thay — trải nghiệm kém. Một cách giải quyết là tách trạng thái ra ngoài và lưu/khôi phục trạng thái khi HMR xảy ra. Ví dụ:

```ts
if (module.hot) {
  module.hot.accept();
  restoreState(module.hot.data.state); // khôi phục trạng thái nếu có

  // trước khi module bị hủy, lưu trạng thái vào module.hot.data
  module.hot.dispose(() => {
    module.hot.data.state = getState(); // lấy trạng thái hiện tại và lưu lại
  });
}
```

Lưu ý: khi HMR bật, module.hot.data là một object dùng để truyền dữ liệu giữa module cũ và module mới.

Ý tưởng trên tương tự cách quản lý trạng thái của Redux — trong hệ sinh thái Angular có các thư viện như ngrx để quản lý trạng thái tập trung; dùng một store bên ngoài giúp giữ trạng thái khi HMR thay đổi component. Tuy nhiên HMR không bắt buộc phải dùng ngrx — bạn có thể tự quản lý trạng thái để lưu/khôi phục.

Một vấn đề khác: sau khi re-render, một số thẻ style cũ (nhất là style do Angular tạo, chứa chuỗi _ng) có thể bị giữ lại, làm tích tụ nhiều style tag trong head. Có thể dọn dẹp chúng trong dispose:

```ts
if (module.hot) {
  module.hot.accept();
  module.hot.dispose(() => {
    const styles = document.head.querySelectorAll('style');
    for (let i = 0, len = styles.length; i < len; i++) {
      if (styles[i].innerText.indexOf('_ng') >= 0) {
        styles[i].remove();
      }
    }
  });
}
```

Kết luận ngắn:
- Bật HMR trên dev server (webpack --hot hoặc ng serve --hmr).
- Thêm module.hot.accept() để chấp nhận cập nhật.
- Lưu/khôi phục trạng thái qua module.hot.data trong module.hot.dispose()/accept() để tránh mất dữ liệu component.
- Dọn dẹp style tag cũ nếu cần.
- Sử dụng store (ví dụ ngrx) giúp giữ trạng thái nhất quán qua HMR nhưng không bắt buộc.
