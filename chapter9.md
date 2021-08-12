### Express

#### 1. express 기본 개념

```node.js
app.use('/', (req, res, next) => {
  console.log('Middleware 1')
  next()
})

app.use((req, res) => {
  console.log('Middleware 2')
  res.send('Hello, express!')
})
```

- next를 써주기 전까지는 끝나지 않았다고 생각한다.
- 미들웨어는 위에서 정의된 것부터 실행한다. 

http://expressjs.com/ko/api.html#express

- node의 request와 express의 request는 다름. express가 살을 더 얹힌 형태임
- express에서 node의 메소드에 접근이 가능함.

```node.js
app.use(
  '/',
  (req, res, next) => {
    console.log('Middleware 1-1')

    setTimeout(() => {
      next()
    }, 1000)
  },
  (req, res, next) => {
    console.log('Middleware 1-2')
    next()
  }
)
```
- 미들웨어는 , 로 연결하여서 쓸 수 있음.
- 연속으로 이어지는 함수임
- 미들웨어 간에 선언한 변수를 같이 쓸 수 있음.

#### 2. REST API 라우팅하기

##### 1. 주소 규칙

1. /ab?cd
- 'a' or 'b' +cd일 경우 가능

2. /ab+cd 
- 'b'가 여러개 가능, 하나도 없으면 불가능 + cd

3. /ab*cd
- * 자리에 무엇이든 들어 올 수 있음. 

4. /a(bc)?d'
- bc가 있거나 없거나. (b,c중 하나만 있는 것은 안 됨)

5. /abcd/
- abcd의 패턴이 꼭 있어야 함.
- abcde 가능

6. /abcd$/
- abcd가 끝에 만 있으면 가능
- eeeabcd 가능

7. /^\/abcd$/
- abcd로 시작해야 가능

8. ['/abc', '/xyz']
- array도 가능
- array 안에서 요소들이 서로 다른 타입이어도 가능

> path를 매치시켜 어떤 것을 통과시킬지 정할 수 있음.

##### 2. Router

1. router 설정
```node.js
const userRouter = express.Router()

const app = express()

userRouter.get('/', (req, res) => {
  res.send('User list')
})

userRouter.get('/:id', (req, res) => {
  res.send('User info with ID')
})

userRouter.post('/', (req, res) => {
  // Register user
})

app.use('/users', userRouter)
```

- router 간에 prefix를 공유할 수 있음.
- 마지막 코드를 통해 /users가 있을 때 사용 가능하다고 말할 수 있음. 
- router를 통해 하나로 묶을 수 있음

2. 파라미터 설정
```node.js
userRouter.param('id', (req, res, next, value) => {
  console.log(value)
  next()
})

userRouter.get('/:id', (req, res) => {
  res.send('User info with ID')
})
```
- id의 파라미터 처리를 정해줄 수 있음.

3. 업데이트
- express는 body를 parse하라고 되어있지는 않음

