# DecimalPipe — Cách dùng

##### Cú pháp sử dụng：
```
number_expression(value) | number[:digitInfo[:locale]]
```

Ví dụ:
```
{{ e | number:'3.1-5' }}
{{ số | number :'phạm vi giới hạn' }}
```

##### Mục đích
Chuyển một số thành chuỗi theo định dạng dựa trên phạm vi được chỉ định.

- `value` là một giá trị kiểu số.
- `digitInfo` là một chuỗi có dạng:  
  `{minIntegerDigits}.{minFractionDigits}-{maxFractionDigits}`
  - `minIntegerDigits`: số chữ số tối thiểu phần nguyên. Mặc định là 1.
  - `minFractionDigits`: số chữ số tối thiểu sau dấu thập phân. Mặc định là 0.
  - `maxFractionDigits`: số chữ số tối đa sau dấu thập phân. Mặc định là 3.

- `locale` là chuỗi định nghĩa locale sẽ dùng (mặc định dùng LOCALE_ID hiện tại của ứng dụng).

##### Ví dụ
```typescript
@Component({
  selector: 'number-pipe',
  template: `<div>
    <!-- num1 = 3.14, num2 = 2.718281828459045; -->

    <!-- Không chỉ định digitInfo, dùng mặc định '1.0-3' -->
    <p>{{num1 | number}}</p> <!-- kết quả '3.14' -->
    <p>{{num2 | number}}</p> <!-- kết quả '2.718' -->

    <!-- phần nguyên ít nhất 3 chữ số, phần thập phân từ 1 đến 5 chữ số -->
    <p>{{num1 | number:'3.1-5'}}</p>  <!-- kết quả '003.14' -->
    <p>{{num2 | number:'3.1-5'}}</p>  <!-- kết quả '002.71828' -->

    <!-- phần nguyên ít nhất 4 chữ số, phần thập phân 5 chữ số -->
    <p>e (4.5-5): {{num1 | number:'4.5-5'}}</p> <!-- kết quả '0,003.14000' (phụ thuộc locale) -->
    <p>pi (4.5-5): {{num2 | number:'4.5-5'}}</p> <!-- kết quả '0,002.71828' (phụ thuộc locale) -->
    <!-- 'fr' là một ví dụ locale -->
    <p>e (french): {{num2 | number:'4.5-5':'fr'}}</p> <!-- kết quả '0 002,71828' -->
  </div>`
})
export class NumberPipeComponent {
  num1: number = 3.14;
  num2: number = 2.718281828459045;
}
```