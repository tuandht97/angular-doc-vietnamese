# Cách sử dụng Angular CLI

## 1. Angular CLI là gì
Nó là công cụ giao diện dòng lệnh (CLI) dùng để khởi tạo, phát triển, build và bảo trì các ứng dụng Angular.

## 2. Cách cài đặt
> Nếu chưa có npm, hãy cài Node.js trước: https://nodejs.org/en/download/

```bash
npm install -g @angular/cli
```

## 3. Cách sử dụng
> `new` | `n` — `n` là viết tắt của `new`, tác dụng giống nhau (tương tự cho các lệnh có alias).

### 3.1 Tạo dự án ([new | n](https://angular.cn/cli/new))
```bash
ng new my-project
```
Lệnh này tạo một project Angular cơ bản và tải về các dependency, mặc định server chạy trên cổng 4200.

<details>
<summary>Nội dung có thể cần tuỳ chỉnh khi tạo</summary>

Cách dùng dạng:
```bash
ng new my-project --xxx=x
```

> Các giá trị mặc định không được đặc biệt ghi sẽ là `false`

Tham số | Ý nghĩa
---|---
`--force=true\|false` | Nếu true sẽ ép ghi đè các file hiện có
`--routing=true\|false` | Nếu true sẽ tạo module routing ban đầu cho project
`--skipInstall=true\|false` | Nếu true sẽ không tự động cài đặt các package
`--skipTests=true\|false` | Nếu true sẽ không sinh file test `spec.ts` khi tạo
`--style=css\|scss\|sass\|less\|styl` | Phần mở rộng cho file style hoặc preprocessor, mặc định `css`
`--packageManager=npm\|yarn\|pnpm\|cnpm` | Chọn package manager để tải dependency, mặc định `npm`
`--prefix=prefix` | Tiền tố cho selector của component/directive (ví dụ `--prefix=dep` sẽ tạo selector `dep-xxx`)

> Nếu khi tạo project quên tuỳ chỉnh, có thể sửa tay trong `angular.json`.
</details>

### 3.2 Sinh file mới ([generate | g](https://angular.cn/cli/generate))
Sinh các file với template mã sẵn. Ví dụ `ng generate service demo` sẽ tạo `demo.service.ts` trong thư mục hiện tại.

Lệnh | Chức năng | Viết tắt
---|---|---
`ng generate module xx` | Tạo module mới | `ng g m xx`
`ng generate component xx` | Tạo component mới | `ng g c xx`
`ng generate directive xx` | Tạo directive mới | `ng g d xx`
`ng generate service xx` | Tạo service mới | `ng g s xx`
`ng generate pipe xx` | Tạo pipe mới | `ng g p xx`

Mẹo: Trong VS Code có extension "Angular Files" (tìm `alexiv.vscode-angular2-files`) giúp tạo file/component nhanh chóng.

### 3.3 Chạy dự án ([serve | s](https://angular.cn/cli/serve))

Tuỳ chọn | Ý nghĩa
---|---
`--host=xx` | Thiết lập địa chỉ host để ứng dụng có thể truy cập từ bên ngoài (có thể là IP hoặc `0.0.0.0`)
`--open=true\|false` | Nếu true sẽ tự mở trình duyệt sau khi khởi động; viết tắt: `ng serve -o`
`--port=xxxx` | Thiết lập cổng khởi động để tránh trùng cổng (mặc định 4200)
`--proxyConfig=xx` | Thiết lập file proxy
`--watch=true\|false` | Tự rebuild khi code thay đổi (mặc định true)
`--aot=true\|false` | Chạy với AOT (precompile) — mặc định false. Nhược điểm: thời gian khởi chạy lâu hơn; ưu điểm: giúp debug các lỗi chỉ xuất hiện khi build ở môi trường production.

Lưu ý nhỏ: Có thể muốn tự động tăng cổng nếu trùng (ví dụ +1), nhưng CLI không có sẵn tính năng đó mặc định.

### 3.4 Build / đóng gói dự án ([build | b](https://angular.cn/cli/build))

Tuỳ chọn | Ý nghĩa
---|---
`--baseHref=xx` | Đường dẫn base để index.html truy xuất tài nguyên tĩnh. Có thể cấu hình trực tiếp trong `<base href="xx">` trong index.html hoặc trong `.angular.json`.
`--aot=true\|false` | Build với AOT (mặc định false). Thường được cấu hình trong `angular.json`.
`--optimization=true\|false` | Bật/tắt tối ưu hoá đầu ra khi build.
`--configuration=xx` | Chọn cấu hình build (ví dụ production, qa, sit...)
`--prod=true\|false` | Viết tắt của `--configuration=production` — build cho môi trường production (kèm tree-shaking, tối ưu...)

<details>
<summary>Giải thích --configuration</summary>

Khi cần build nhiều môi trường khác nhau, cấu hình trong `angular.json` có thể định nghĩa các `configurations`:

```json
{
  "projects": {
    "project-name": {
      "architect": {
        "build": {
          "configurations": {
            "production": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.prod.ts"
                }
              ]
              // ...
            },
            "qa": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.qa.ts"
                }
              ]
              // ...
            },
            "sit": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.sit.ts"
                }
              ]
              // ...
            }
          }
        }
      }
    }
  }
}
```

Sau đó build theo môi trường:
- QA: `ng build --c=qa`
- SIT: `ng build --c=sit`
- Prod: `ng build --c=production`

> `--c` là viết tắt của `--configuration`.
</details>

### 3.5 Cập nhật dự án ([update](https://angular.cn/cli/update))
> Angular thường có major release khoảng mỗi nửa năm, nên cập nhật kịp thời rất quan trọng. Nếu tụt quá 1 major version, việc nâng cấp sau này có thể rất phức tạp.

Để nâng từ một major version lên major khác:
```bash
ng update @angular/cli@^<major_version> @angular/core@^<major_version>
```

Trước khi update, nên đọc hướng dẫn nâng cấp chính thức: https://update.angular.io/

### 3.6 Các lệnh khác
- `ng lint` — Chạy kiểm tra lint cho code
- `ng test` — Chạy unit tests
- `ng e2e` — Chạy end-to-end tests
- `ng add` — Thêm một thư viện bên thứ ba vào project và tự động cập nhật cấu hình cho project (ví dụ `ng add @angular/material`). Nếu muốn viết một thư viện có thể cài bằng `ng add`, tham khảo hướng dẫn về Angular Schematics: https://blog.kevinyang.net/2018/06/15/angular-schematics-ng-add/
