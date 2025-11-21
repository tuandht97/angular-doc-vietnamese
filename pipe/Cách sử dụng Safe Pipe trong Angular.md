# Cách sử dụng Safe Pipe trong Angular

Cách viết Pipe
```ts
import { Pipe, PipeTransform } from '@angular/core';
import { DomSanitizer, SafeHtml, SafeResourceUrl, SafeScript, SafeStyle, SafeUrl } from '@angular/platform-browser';

@Pipe({
  name: 'safe',
})
export class SafePipe implements PipeTransform {
  constructor(protected sanitizer: DomSanitizer) {}

  public transform(value: any, type: string): SafeHtml | SafeStyle | SafeScript | SafeUrl | SafeResourceUrl {
    switch (type) {
      case 'html':
        return this.sanitizer.bypassSecurityTrustHtml(value);
      case 'style':
        return this.sanitizer.bypassSecurityTrustStyle(value);
      case 'script':
        return this.sanitizer.bypassSecurityTrustScript(value);
      case 'url':
        return this.sanitizer.bypassSecurityTrustUrl(value);
      case 'resourceUrl':
        return this.sanitizer.bypassSecurityTrustResourceUrl(value);
      default:
        throw new Error(`Invalid safe type specified: ${type}`);
    }
  }
}
```
Cách sử dụng Pipe
```ts
<span [outerHTML]="data | safe: 'html'"></span>
```
Sử dụng safeStyle trong JavaScript
```ts
import { DomSanitizer, SafeStyle } from '@angular/platform-browser';

export class xxx {
    img = "http://www.baidu.com/images/01";
    imgTransform: SafeStyle
    
    constructor(private sanitizer: DomSanitizer) {
     this.imgTransform = this.transformSafeStyle(this.img);
    }
    
    transformSafeStyle(url) {
        return this.sanitizer.bypassSecurityTrustStyle('url(' + url + ')');
    }
}
```