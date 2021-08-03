## Ch 02. 든든한 개발환경 설정하기

### 1. Node version 관리 

- local과 server의 버전이 맞지 않아 돌아가지 않는 경우가 있어서 버전관리 패키지 존재
- 종류: NVM, tj\n(mac만 가능)

- nvm 설치 (설정 관리가 어렵다는 단점)

https://seunghyun90.tistory.com/52

### 2. npm, 포매터, 린팅

1) npm
- 이 작업은 npm으로 작동하는 프로젝트이다.
 -> package.json이 있어야함
 
 ```
 npm init -y
 ```
 
 - -y는 yes의 준말
 - scripts: 주로 사용하는 파트

2) prettier

```
npm install --save-dev prettier
```

- package-lock.json도 같이 git에 넣어주면 좋음(다운로드 출처, 정확한 package 버전)

 (1) .prettierrc 
 
 (2) .settings.json : local setting을 모아두는 곳. 이 project에만 쓰이는 setting 
 
  - vscode는 이렇게 사용.
  - https://success206.tistory.com/153  : intellij에서는 Alt+Shift+P 키를 눌러 prettier 사용

3) eslint

```
npm install --save-dev eslint
```

 (1) .eslintrc.js
 
 ```node
 module.exports = {}
 ```
 
 - 처음엔 module이 오지 않음. npm에 깔려있는 package를 intellij가 처음엔 disabled 시켜놓았기 때문에, enable 시켜야 함.

 (2) eslint 규칙의 plug in 많음 (물론 개별적 설정 가능)

- https://github.com/airbnb/javascript
- plug in 설치

```
npm install --save-dev eslint-config-airbnb-base eslint-plugin-import
```

- prettier의 공존을 위한 plug 설치

```
npm install --save-dev eslint-config-prettier
```

```node
module.exports = {
  extends: ['airbnb-base', 'prettier'],
}
```
* prettier는 항상 마지막으로 두어야함
- intellij 안에서 eslint 설정 필요 https://kjwsx23.tistory.com/391

(3) eslint 막기
```
/* eslint-disable-next-line */
```
한 기능만 막고싶다면
```
/* eslint-disable-next-line no-console */
```

(4) node 전용 plugin

```
npm install --save-dev eslint-plugin-node
```
.eslintrc.js에 추가
```node
module.exports = {
  extends: ['airbnb-base', 'plugin:node/recommended','prettier'],
}
```

### 3. 타입 체킹

- javascript는 동적이기 때문에 type error가 실행된 다음에 일어남
- 다른 언어는 미리 compile을 하기 때문에 이런 에러가 잘 나지 않지만 js는 빈번
- typescript를 사용: type정의만 빼면 javascript와 동일

```
npm install --save-dev typescript
```
// @ts-check

*** intellij에서 찾는 방법을 아직 찾기 못함


## Ch 03. JavaScript 기초 이론 다지기

### 01. call stack, non-blocking IO, event loop
