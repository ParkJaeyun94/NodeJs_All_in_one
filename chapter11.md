### WebSocket을 통한 실시간 인터렉션 만들기

#####  요구사항 설정과 프로젝트 설계

- websocket : 실시간 통신 가능 

##### 1. 프로젝트 들어갈 요소

1. Frontend
* Template Engine: Pug
* CSS framework: TailwindCSS

2. backend
* Web framework: koa
* Live networking: koa-websocket
* Database: MongoDB

##### 2. 프론트엔드 설계

###### 1) koa 설치
https://koajs.com/

> npm install koa

```node
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```

###### 2) koa-pug 설치

https://www.npmjs.com/package/koa-pug

> npm install koa-pug 

```node
const Koa = require('koa');
const Pug = require('koa-pug')
const path = require('path')

const app = new Koa()
new Pug({
  viewPath: path.resolve(__dirname, './views'),
  app, // Binding `ctx.render()`, equals to pug.use(app)
})

app.use(async (ctx) => {
  await ctx.render('main')

});


app.listen(5000);
```

```pug
html
    head
    body
        div Hello, Pug!
```

###### 3) TailwindCSS 

1. tailwindCSS link로 설치
https://tailwindcss.com/docs/installation

![image](https://user-images.githubusercontent.com/69338643/129861983-e7a27cd4-8089-4a83-b281-02567847c794.png)

```pug
html
    head
        link(href="https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css" rel="stylesheet")
    body
        div Hello, Pug!
```

2. jetBrain에서 tailwindCSS in
https://www.jetbrains.com/help/webstorm/tailwind-css.html#ws_css_tailwind_before_you_start


###### 4) 실시간 서버 연결 websocket

https://www.npmjs.com/package/koa-websocket

https://www.npmjs.com/package/koa-static

> npm install koa-websocket koa-route
> npm install koa-static

https://www.npmjs.com/package/koa-mount

> npm install koa-mount
