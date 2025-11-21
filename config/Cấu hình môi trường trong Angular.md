# Cấu hình môi trường trong Angular

> Giả sử có ba môi trường: development (dev), testing (test) và production (prod). Khi build ta có thể dùng tham số --environment để chỉ định môi trường; CLI sẽ thay thế file cấu hình tương ứng.

1. Cấu hình trong .angular-cli.json
```json
"environmentSource": "environments/environment.ts",
"environments": {
  "dev": "environments/environment.ts",
  "test": "environments/environment.test.ts",
  "prod": "environments/environment.prod.ts"
}
```

2. Thư mục environments (ví dụ)
```
environments/
  ├─ common.json
  ├─ environment.ts
  ├─ development.json
  ├─ environment.test.ts
  ├─ test.json
  ├─ environment.prod.ts
  └─ production.json
```

- Dùng common.json để chứa cấu hình chung:
```json
{
  "title": "",
  "question": {
    "url": ""
  },
  "list": {
    "pageSize": 10
  }
}
```

- Development (môi trường dev)
environment.ts:
```ts
export const environment = Object.assign({}, require('./common.json'), require('./development.json'), {
  production: false,
  envName: 'dev'
});
```

development.json:
```json
{
  "baseUrl": "/",
  "env": "dev",
  "api": {
    "host": "http://localhost:4200"
  }
}
```

- Testing (môi trường test)
environment.test.ts:
```ts
export const environment = Object.assign({}, require('./common.json'), require('./test.json'), {
  production: false,
  envName: 'test'
});
```

test.json:
```json
{
  "baseUrl": "/",
  "env": "test",
  "api": {
    "host": "testurl"
  }
}
```

- Production (môi trường prod)
environment.prod.ts:
```ts
export const environment = Object.assign({}, require('./common.json'), require('./production.json'), {
  production: true,
  envName: 'prod'
});
```

production.json:
```json
{
  "baseUrl": "/",
  "env": "prod",
  "api": {
    "host": "produrl"
  }
}
```

3. Sử dụng trong service
Nhập environment tương ứng và dùng trong các request:
```ts
import { Injectable } from '@angular/core';
import { Http } from '@angular/http';
import { Observable } from 'rxjs';
import { environment } from '../environments/environment';

@Injectable()
export class LoginService {
  constructor(private http: Http) {}

  public Login(): Observable<any> {
    // ví dụ sử dụng environment.api.host
    return this.http.get(`${environment.api.host}/login`).map(response => response.json());
  }
}
```

Ghi chú:
- Khi build: `ng build --env=dev` hoặc `ng build --env=prod` (tuỳ CLI/version, cú pháp có thể khác: `--configuration=production` trong Angular CLI mới).
- Cách này tách rõ cấu hình cho từng môi trường, dễ quản lý và tránh hardcode URL/biến môi trường trong code.