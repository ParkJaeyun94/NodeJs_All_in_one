### 이미지 리사이징

###### 1) unsplash
https://unsplash.com/
https://unsplash.com/developers
https://github.com/unsplash/unsplash-js

api도 사용 가능함.

> npm install unsplash-js node-fetch

###### 2) sharp
https://www.npmjs.com/package/sharp

> npm install sharp


#### 1. 이미지를 서버에 그대로 내리기

```node
// const fs = require('fs')
const http = require('http')
const { createApi } = require('unsplash-js')
const { default: fetch } = require('node-fetch')

const unsplash = createApi({
  accessKey: 'sK2JLgvvF5CWGdqHHpc7rZyznO6s0QMYLT9dUdkpdCY',
  fetch,
});

/**
 * @param {string} query
 */

async function searchImage(query) {
  const result = await unsplash.search.getPhotos({query})
  console.log(result.response?.results)

  if (!result.response){
    throw new Error('Failed to search image.')
  }

  const image = result.response.results[0]

  if (!image) {
    throw new Error('No image found.')
  }
  return{
    description: image.description || image.alt_description,
    url: image.urls.regular
  }
}

const server = http.createServer((req, res)=>{
  async function main(){
    const result = await searchImage('mountain')
    const resp = await fetch(result.url)
    resp.body.pipe(res)
  }
  main()
})

const PORT = 5000

server.listen(PORT, ()=>{
  console.log('The server is listening at port', PORT)
})

```

#### 2.

> npm install image-size










