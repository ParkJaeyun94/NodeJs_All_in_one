### Node.js 핵심개념 정리

#### 01. require와 모듈, 모듈의 레졸루션

https://nodejs.org/dist/latest-v14.x/docs/api/

1. module
- 각 파일이 별개의 모듈로 취급이 된다. 
- 노드에서는 각 파일 하나 하나가 모듈이다.


2. require
- 모듈을 가져오는 메소드, 함수

3. 함수를 가져오는 함수
- CommonJS: require
- ECMAScript: export, import
- 확장자만 잘 써주면 import를 잘 할 수 있다.
- ECMA에서 CommonJS 방식도 가능하며, ECMA 방식도 가능하다
- frontend와도 모듈을 공유하는 방식을 사용할 수 있음. ECMA방식을 통해 node와 frontend와도 잘 동작하는 코드를 작성할 수 있음

4. require는 몇 번 가능할까?

```node.js
console.log(animalsA === animalsB)
console.log(animalsA === animalsC)
```

결과
```
true
true
```

결국 한 번만 불러온다는 것을 알 수 있음. 

5. 경로
CommonJS: require
 - node standard library에 있는 모듈은 절대경로를 지정해 가져온다.
 - 이 프로젝트 내의 다른 파일은 상대경로를 지정해 가져온다.
 - 절대경로를 지정하면 module.paths의 경로들을 순서대로 검사하여 하나에서 해당 모듈이 있으면 가장 첫 번째 것을 검사해 가져온다.

#### 02. npm, yarn 등 패키지 매니저와 package.json(1)

1. package.json과 package-lock.json
- package.json은 대략적 버전을 사용
  - ^5.0.0
- package-lock.json은 정확한 버전을 사용
  - 실제 설치 버전 (여러 개발자와 협업하게 될 경우, lock파일은 꼭 commit해두어야 함!!)
  - production, local에서도 똑같은 버전을 써줘야, 환경에 따른 실행과 실행이 안되는 것 막을 수 있음.

2. module이 어떻게 사용되는가?
- package 안에 있는 index.html를 활용하는 것과 같다. 
  - https://example.com/index.html
  - https://example.com/
  - 두 링크가 동일한 역할을 한다는 것
  - require('decamelize')
  - require('decamleize/index')
  - 두 require가 동일한 역할을 한다.
  
3. --save-dev
 - 개발하는 환경에서만 필요한 package라는 뜻
   - "dependencies": 실행하는데 필요한 package
   - "devDependencies": 개발하는 환경에서만 필요 ex. eslint 
   - production server에서 필요하지 않은데 module이 많아져서 용량이 커지는 것을 막기 위함. 
   - npm install --production : dependencies package만
   - npm install : package 전부
 
 4. Semantic Versioning (npm쓰고 있음)
 - MAJOR: * or x
 - MINOR: 1 or 1.x or ^1.0.4
 - PATCH: 1.0 or 1.x or ~1.0.4 (버그픽스)
 
 https://velog.io/@slaslaya/Semantic-Versioning-2.0.0-MAJOR-MINOR-PATCH%EC%99%80-%EB%AA%85%EC%84%B8%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC
 
 ![image](https://user-images.githubusercontent.com/69338643/128680138-20637cec-9508-424f-8c6b-b3c6950952f0.png)
 
- MAJOR Version: 기존 api가 변경 / 삭제 되었기 때문에 update 하면 동작하지 않을 수 있다는 경고의 의미
- MINOR Version: 이전 버전과 호환되는 방식으로 API가 추가되었으니 살펴보라는 의미
- PATCH Version: 이전 버전과 호환되는 버그 수정을 했을 경우

```node.js
npm install 000@3.1.0
```
@version

```node.js
npm update ^000
```
마이너의 가장 최신 버전으로 업데이트 해줌

```node.js
npm update ~000
```
패치의 가장 최신 버전으로 업데이트 해줌

- 기존의 기능성을 깨지 않으면서 최대한 올리도록 하는 것이 기호의 의미이다. 
- 
#### 03. npm, yarn 등 패키지 매니저와 package.json(2)

![image](https://user-images.githubusercontent.com/69338643/128809050-52adc4c9-1701-480f-a019-d21e69b0b576.png)

#### 04. Node.js 내장 객체들

!! node 공식문서 

https://nodejs.org/dist/latest-v14.x/docs/api/

1. __dirname, __filename
- __dirname: 실행되는 파일의 디렉토리 이름
- __filename: 실행되는 파일의 이름

2. process
- stdin, stdout : stream관련 입출력

```node.js
process.stdin.setEncoding('utf-8')
process.stdin.on('data', (data) ={
 console.log(data, data.length)
 })
```

> cat .gitignore| node src/main.js

치면 콘솔로 바로 결과가 나옴.

- pipe: 한 스트림의 결과를 다른 스트림에 넣어줌

```node.js

process.stdin.pip(process.stdout)

```
> cat .gitignore| node src/main.js

- exit

- argv: 명령줄 인자를 파싱해서 인자로 가짐 -> cri 객체

```node.js
console.log(process.argv)
```

> node src/main.js --input 2


3. setInterval, setTimeout

```node.js
setInterval(() => {
 console.log('Interval')
}, 1000)
```
1초 단위로 해라

```node.js
setTimeout(()  => {
 console.log('timeout')
}, 1000)
```
1초 뒤에 해라

4. clearInterval, clearTimeout

```node.js
let count = 0
const handle = setInterval(() => {
 console.log('Interval')
 count += 1
 
 if (count === 4){
  console.log('done!')
  clearInterval(handle)
 }
}, 1000)
```
5초뒤에 인터벌 멈춤

```node.js
const timeoutHandle = setTimeout(()  => {
 console.log('timeout')
}, 1000)

clearTimeout(timeoutHandle)
```
1초 뒤 실행하고 멈춤

5. console


#### 05. 스탠다드 라이브러리
1. OS (운영체제에 대한 정보 얻어올 수 있음) 

```node.js
const os = require('os')
```
메소드 다양함
- arc: 아키텍처 (ex. x64)
- platform: ex. linux
- cpus: cpu 정보

2. fs

3. Child processes

4. DNS: 도메인
5. Path
```node.js
const path = require('path')
const fs = require('fs')
// 파일 경로를 절대경로로 만들어줌
const filePath = path.resolve(__dirname, './text.txt')
const fileContent = fs.readFileSync(filePath, 'utf-8')
console.log(fileContent)
```

6. HTTP:
- createServer
- get

7. HTTP2: 새로운 버전의 프로토콜
8. HTTPS: 보안모듈도 사용가능
9. Net: 저수준 TCP 통신을 위해 사용
- HTTP를 사용하지 않고 저수준 프로토콜 사용하는 경우
- byte단위로 메세지 송수신 가능
