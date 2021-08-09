
tip

1. --save-dev : 개발용으로만 설치. 개발할 때만 사용하는 패키지라는 뜻

2. server에서 수정이 일어나면 자동으로 바꿔주는 유틸리티-> npm install nodemon

3. java script에서 정규식

https://beomy.tistory.com/21

- 문자열의 패턴을 정의
- ^: 시작, $: 끝
- +: 여러개 있다.
- \: 이스케이프(뒤에 오는 문자는 특수문자 자체로 쓴다, 구문적 의미가 없다는 것을 알리는 것)
- 

4. test함수
- 정의한 정규식 패턴이 존재하는지 검사하는 함수 

5.분기처리
분기란 선택적으로 코드를 실행시킬 수 있는 것을 의미한다. 분기 명령에는 참/거짓의 여부에 따라서 두 가지 다른 코드를 실행할 수 있는 if와, 변수의 값에 따라서 여러가지 다른 코드들 중 하나를 실행할 수 있는 switch / case 가 있다.

6.
```node.js
else if (req.url && POSTS_ID_REGEX.test(req.url)) {
    console.log(POSTS_ID_REGEX.exec(req.url))
```
test -> boolean
exec -> 맞았는데 구체적으로 어떤 부분이 맞았느냐의 정보까지 제공

7. 정규식의 capture group 기능
const POSTS_ID_REGEX = /^\/posts\/([a-zA-Z0-9-_]+)$/
- ( )를 통해서 캡쳐뜰 부분 설정할 수 있음.

8. type 정의 = JSDoc

```node.js
/**
 *
 * @typedef Post 
 * @property {string} id
 * @property {string} title
 * @property {string} content
 */

/** @type {Post[]}  */
 ```
 
 - intellij는 typescirpt가 자동으로 된다.

https://jaimemin.tistory.com/1549

9. httpie 설치

- pip 설치 
 https://archmond.net/archives/10976
 
```node.js
py -m pip install httpie
```

- httpie 사용
```
py -m httpie localhost:4000
```

10. 글 posting

```
localhost:4000/posts/my_second_posts title=foo content=bar --print=hHbB
```
--print=hHbB (받은 것만 보려면 HB만 쓰기)

11. 정규식 활용
```node.js
replace(/\s/g, '_')
```

\s: 공백에 해당하는
/g: 모든 것을
'_': _로 대체하라

12. 로직의 유지보수성을 높인다
= 추상화
= 같은 로직을 묶는다는 것
= 코드의 규격화

13. 코드의 추상화

```node.js
/**
 * @typedef Route
 * @property {string} url
 * @property {string} method
 * @property {() => string} callback
 */
 ```
 callback은 ()인자를 받지 않고, string으로 출력
 
 ```node.js
  * @property {() => Promise<*>} callback
  ```
  callback이 () 인자를 받지않고, Promise로 결과물을 돌려준다.
  
  14. module.exports
  
  - 모든 js파일은 모듈이다
  - 모듈에서 내보내는 것이 {} 안에 담긴 것이다. 
