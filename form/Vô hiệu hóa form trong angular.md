# Cách vô hiệu hóa form trong angular
Cách 1: Thiết lập trực tiếp là false
```ts
export class AppComponent {
  form: FormGroup;

  ngOnInit() {
    this.form = new FormGroup({
      input: new FormControl(false),
    });
  }
}
```
Cách 2: Thiết lập disabled là false
```ts
export class AppComponent {
  form: FormGroup;

  ngOnInit() {
    this.form = new FormGroup({
      input: new FormControl({ disabled: true, value: '' }, []),
    });
  }
}
```
Cách 3: Sử dụng directive

Directive ts
```ts
import { NgControl } from '@angular/forms';

@Directive({
  selector: '[disableControl]'
})
export class DisableControlDirective {

  @Input() set disableControl( condition : boolean ) {
    const action = condition ? 'disable' : 'enable';
    this.ngControl.control[action]();
  }

  constructor( private ngControl : NgControl ) {
  }

}
```
Sử dụng trong html với [disableControl], có thể truyền biến vào
```html
<form [formGroup]="form">
  <input formControlName="input1" placeholder="Input 1" [disableControl]="true">
  <input formControlName="input2" placeholder="Input 2">
</form>
```