# Component với selector dạng attribute
Chỉ cần viết selector của component con dưới dạng attribute selector, việc truyền giá trị vẫn thực hiện giống như truyền giá trị thông thường.

Component cha
```ts
import {Component } from '@angular/core';
@Component({
  selector: 'app',
  template: `
  <table>
    <tr myTd *ngFor="let animal of animals" [animal]="animal"></tr>
  </table>
  `
})
export class AppComponent {
  animals = [ 'Mèo', 'Chó' ];
}
```
Component con
```ts
import {Component, Input} from '@angular/core';

@Component({
  selector: '[myTd]',
  template: `<td>{{animal}}</td>`
})
export class MyTdComponent {
  @Input() animal;
}
```