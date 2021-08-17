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

##### 3. Pug로 템플릿 그려보기

```node

npm install pug

```

```node
// 원하는 폴더로 views 폴더를 옮길 수 있음.
app.set('views', 'chap09/views')
app.set('view engine', 'pug')
```

> localhost:5000/users/15 Accept: text/html  --print=hHbB

> localhost:5000/users/15 Accept: application/json  --print=hHbB

##### 4. 스태틱 파일 서빙

```node
app.use(express.static('chap09/public'))
```

```pug
html
    head
        link(rel="stylesheet" href="/index.css")
    body
```

static 파일이 있는 폴더의 루트를 지정,
pug에서 css의 링크를 가져올 때, public에 있는 곳에서 가져오게 됨.

* 문제가 있음!
미들웨어(express)는 위에서 아래로 읽게 됨.
만약에 

```node
app.use(express.json())
app.use(express.static('chap09/public'))
// 원하는 폴더로 views 폴더를 옮길 수 있음.
app.set('views', 'chap09/views')
app.set('view engine', 'pug')
...
const USERS = {
  15: {
    nickname: 'foo',
  },
}
```
이렇게 되어 있는데, public 디렉토리 안에 public/users/15 라고 불러오고자 하는 id와 동일한 이름이 있는 것임.
미들웨어는 위에서 아래로 읽다보니, 가장 먼저 타고가는건 static폴더의 public을 먼저 찾게 되면서
users가 아닌 파일 스택팅이 되게 됨.

이것을 해결하는 방법은 두가지
1. static 불러오는 명령문을 맨 아래에 둔다.
2. prefix를 건다.
```node
app.use('/public', express.static('chap09/public'))
```

```pug
html
    head
        link(rel="stylesheet" href="/public/index.css")
    body
```

/public이라는 prefix를 따라야 static을 실행시켜준다.

##### 5. 에러 핸들링

1. id가 없을때 error

```node
router.param('id', (req, res, next, value) => {
    const user = USERS[value]
    if (!user) {
      const err = new Error('User not found.')
      err.statusCode = 404
      throw err
    }
    req.user = user
    next()
})
```
```node
// express 4개 인자를 받으면 error 핸들링이란걸 알고있음.
app.use((err, req, res, next) => {
res.statusCode = err.statusCode || 500
  res.send(err.message)
})
```
status code 까지 넣어서 보내줄 것

2. async일 경우 error 어떻게 내보냄?

```node
router.param('id', async (req, res, next, value) => {
  try {
    const user = USERS[value]
    if (!user) {
      const err = new Error('User not found.')
      err.statusCode = 404
      throw err
    }
    req.user = user
    next()
  } catch (err) {
    next(err)
  }
})
```
try catch를 넣어서 내보낼 것

##### 6. Jest를 활용한 API 테스팅

- API가 계속 복잡해짐.
- 자동으로 시나리오를 짜서 테스트 해주는 tool

- Jest: javascript를 테스트해주는 것 
- supertest

> npm install --save-dev jest supertest

##### 7. 이미지 업로드 핸들링해보기

> npm install multer

app.js
```node
app.use('/uploads', express.static('uploads'))
```

user.js
```node
const multer = require('multer')
const upload = multer({ dest: 'uploads/' })

router.get('/:id', (req, res) => {
  // req.accepts: string array 혹은 string을 받음, 받을 수 있는 것들을 리스트로 넣어주면 가장 잘 매치되는 것을 돌려줌.
  const resMimeType = req.accepts(['json', 'html'])

  if (resMimeType === 'json') {
    res.send(req.user)
  } else if (resMimeType === 'html') {
    res.render('user-profile', {
      nickname: req.user.nickname,
      userId: req.params.id,
      profileImageURL: `/uploads/${req.user.profileImageKey}`,
    })
  }
})
```

```node
router.post('/:id/profile', upload.single('profile'), (req, res) => {
  const { user } = req
  const { filename } = req.file
  user.profileImageKey = filename

  res.send(`User profile image uploaded: ${filename}`)
})
```

user-profile.pug
```pug
html
    head
        link(rel="stylesheet" href="/public/index.css")
    body
      h1 User profile page
      h2 Nickname
      div= nickname

      h2 Profile
      img(src=profileImageURL).profileImage
      form(action=`/users/${userId}/profile` method="post" enctype="multipart/form-data")
          input(type="file" name="profile")
          button Upload Profile Picture
```
