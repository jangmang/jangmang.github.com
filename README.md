## ngOnInit
- 디렉티브나 컴포넌트는 인스턴스가 생성되고 입력 프로퍼티를 통해 초기값이 지정된 이후에 화면에 표시되는데, 디렉티브나 컴포넌트를 초기화하는 로직이 더 있을 때 사용합니다. ngOnChanges()가 처음 실행된 이후에 한 번만 실행됩니다.
- Constructer의 용도와 비교하여 설명하면 constructer는 자바스크립트 엔진에 의해 초기화되지만, ngOninit은 angular가 컴포넌트를 초기화를 완료했다는 점을 전달하기 위해 존재한다.
- 이단계는 컴포넌트에 프로퍼티를 지정하고 첫 변경감지가 되는 범위까지 포함된다.
- 라이프싸이클후킹 https://angular.kr/guide/lifecycle-hooks

<hr/>

## safe pipe
출처 : https://medium.com/@swarnakishore/angular-safe-pipe-implementation-to-bypass-domsanitizer-stripping-out-content-c1bf0f1cc36b
```
ng generate pipe safe
```

safe.pipe.ts 에 덮어씌우기
```
import { Pipe, PipeTransform } from '@angular/core';
import { DomSanitizer, SafeHtml, SafeStyle, SafeScript, SafeUrl, SafeResourceUrl } from '@angular/platform-browser';

@Pipe({
  name: 'safe'
})
export class SafePipe implements PipeTransform {

  constructor(protected sanitizer: DomSanitizer) {}
 
 public transform(value: any, type: string): SafeHtml | SafeStyle | SafeScript | SafeUrl | SafeResourceUrl {
    switch (type) {
			case 'html': return this.sanitizer.bypassSecurityTrustHtml(value);
			case 'style': return this.sanitizer.bypassSecurityTrustStyle(value);
			case 'script': return this.sanitizer.bypassSecurityTrustScript(value);
			case 'url': return this.sanitizer.bypassSecurityTrustUrl(value);
			case 'resourceUrl': return this.sanitizer.bypassSecurityTrustResourceUrl(value);
			default: throw new Error(`Invalid safe type specified: ${type}`);
		}
  }
}
```

Markup
```
<div [innerHtml]="키값 | safe: 'html'"></div>
<div [style]="키값 | safe: 'style'"></div>
```

## inline style unsafe해지하기
```
<TS>
import { DomSanitizer } from '@angular/platform-browser';
private sanitizer: DomSanitizer,
getStyle(taskType){
    return this.sanitizer.bypassSecurityTrustStyle(taskType);
}

<HTML>
[style]="getStyle(button.attr.style)"
```

## HTTP
Angular Essentials(서재425page~)
1. json-server를 사용하여 REST API Mock서버 구축
```
npm install -g json-server
```
2 db.json작성
```
{
  "todos": [
    {
      "content": "동해물과",
      "completed": false,
      "id": 1
    },
    {
      "content": "백두산이",
      "completed": false,
      "id": 2
    }
  ]
}
```
3. 아래의 명령어로 json-server를 실행한다. 생성한 db.json파일의 변경을 감시하도록 옵션을 지정
```
json-server --watch db.json
```
4. http://localhost:3000 로 접근이 가능해짐
5. service 생성을 통해 서버에 요청하고 통합관리해준다.
6. 구현할 컴포넌트에서 'subscribe' 메소드를 통해 옵저버블을 구독하여 서비스내용들을 받아온다. 

<hr/>

## 리액티브 폼
- 반응형 폼 화면에 있는 입력 필드의 값이 변경될 때마다 원하는 로직이 실행하게 하는 모델-드리븐 방식의 폼입니다.
- 리액티브 폼은 컴포넌트 클래스에서 폼 요소의 값 및 유효성 검증 상태를 관리하는 자바스크립트 객체인 폼 모델(FormGroup, FormControl, FormArray)을 직접 정의/생성한다.
- form* 접두사가 붙은 디렉티브(formGroup, formGroupName, formControlName, formArrayName)를 사용하여 템플릿의 폼 요소와 컴포넌트 클래스의 홈 모델을 프로퍼티 바인딩으로 연결한다. 

개념설명 : https://angular.io/guide/reactive-forms<br>
예제소스 : https://stackblitz.com/edit/reactive-form-exam

<hr/>

## 제이쿼리 
https://poiemaweb.com/angular-integrate-jquery-plugin

```
$ npm install jquery //제이쿼리설치

import * as $ from 'jquery'; //사용하고자하는 컴포넌트에 임포트

$ npm install --save-dev @types/jquery //코드에디터의 인텔리센스 기능을 사용하기 위해 제이쿼리 타입정의 파일 설치

$ npm install slick-carousel //slick 설치
```

- slick플러그인의 css를 글로벌로 설치하기 위해 angular.json에 등록
```
...
  "styles": [
    "src/styles.css",
    "./node_modules/slick-carousel/slick/slick.css",
    "./node_modules/slick-carousel/slick/slick-theme.css"
  ],
...
```

- slick플러그인 임포트 및 사용
```
// app.component.ts
import { Component, OnInit, ViewChild, ElementRef } from '@angular/core';
import * as $ from 'jquery';
import 'slick-carousel';

@Component({
  selector: 'app-root',
  template: `
    <h2 class="title">Integrate jQuery plugin</h2>

    <div class="carousel-container" #carousel>
      <div class="carousel-item" *ngFor="let item of carouselItems">
        {{ item }}
      </div>
    </div>
  `,
  styles: [`
    /* Slick Custom Theme */
    .carousel-container .carousel-item {
      position: relative;
      color: white;
      background-color: #3498db;
      min-height: 250px;
      text-align: center;
    }

    .carousel-container {
      width: 500px;
      margin: 0 auto;
    }

    .carousel-item {
      font-size: 10em;
      line-height: 250px;
    }
  `]
})
export class AppComponent implements OnInit {
  carouselItems: string[] = ['1', '2', '3'];
  @ViewChild('carousel') carousel: ElementRef;

  ngOnInit() {
    $(this.carousel.nativeElement).slick();
  }
}
```

- slick 플러그인 타입 정의 파일 설치
```
$ npm install --save-dev @types/slick-carousel
```

- 템플릿의 컴파일이 완료되어 DOM이 완성되기 이전에 slick 메소드를 호출하였기 때문이다. ngOnInit 생명주기 훅 메소드에서 호출하던 slick 메소드를 ngAfterViewInit 생명주기 훅 메소드에서 호출하도록 수정
```
// app.component.ts
import { Component, AfterViewInit, ViewChild, ElementRef } from '@angular/core';

...

export class AppComponent implements AfterViewInit {
  carouselItems: string[] = ['1', '2', '3'];
  @ViewChild('carousel') carousel: ElementRef;

  ngAfterViewInit() {
    $(this.carousel.nativeElement).slick();
  }
}
```
- css로 버튼위치를 조절한다.
```
import { Component, ViewEncapsulation, ViewChild, ElementRef, AfterViewInit } from '@angular/core';

  styles: [`
    ...

    .slick-prev {
      left: 10px;
      z-index: 99;
    }

    .slick-next {
      right: 10px;
      z-index: 99;
    }
  `],
  encapsulation: ViewEncapsulation.None
})
```
하지만 CSS 룰셋이 정상적으로 적용되지 않았다. 그 이유는 slick 플러그인이 동적으로 생성하는 HTML 요소는 템플릿이 컴파일된 이후에 DOM에 추가되므로 Angular가 캡슐화를 위해 자동 생성하는 _ngcontent-c0와 같은 어트리뷰트가 추가되지 않았기 때문에 이들 HTML 요소에는 컴포넌트에서 지정한 CSS 룰셋이 적용되지 않는다. :host ::ng-deep mySelector 선택자를 사용하여 회피할 수 있으나 ::ng-deep 선택자의 경우, 브라우저의 Shadow DOM 지원이 대중화되는 시점에 폐지될 예정이기 때문에 사용하지 않는 편이 좋다.<br>

현재 위 문제를 회피할 수 있는 방법 중에 가장 권장할 수 있는 방법은 뷰 캡슐화 전략을 ViewEncapsulation.None으로 변경하는 것이다. 이 뷰 캡슐화 전략을 사용하면 컴포넌트에서 지정한 스타일은 글로벌 스타일이 되어 다른 다른 컴포넌트에 영향을 주므로 주의해야 한다.

출처 : https://poiemaweb.com/angular-integrate-jquery-plugin

<hr/>

## Router - canActivate
1. canActivate사용을 위한 guard생성
```
ng generate guard auth/auth
```
2. /auth/auth.guard.ts 
```
import { Injectable } from '@angular/core';
import { Router, CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';

@Injectable({
    providedIn: 'root'
})
export class AuthGuard implements CanActivate {

    isLogin = false;

    constructor(private router: Router) { }

    canActivate() {
        if(!this.isLogin) {
            this.router.navigate(['list']); //강제이동될 페이지
            alert('로그인을 해주세요.');
            return false;
        }
        return true;
    }
}
```
3. app-routing.module.ts
```
import { AuthGuard } from './auth/auth.guard'; //canActivate
...
{path:'detail', component:DetailComponent, data: {animation: 'hero'}, canActivate: [AuthGuard]}, //canActivate
```


