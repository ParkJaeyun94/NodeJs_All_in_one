## STREAM

#### stream이란?

- file, network 스트림이 있을 수 있음.
- stream은 스트림 가능한 소스로부터 데이터를 작은 청크로 쪼개 처리할 수 있게 함
- 큰 데이터를 처리해야 하거나, 비동기적으로만 얻을 수 있는 데이터를 처리해야 할 때 유용
- 입, 출력 동일하게 청크로 처리
- ex. 5GB의 파일을 올릴 경우, RAM에 5GB 할당하고 압축하다보니 다른 작업을 할 수 없음, stream을 할 경우 메모리를 가볍게 할당

#### stream의 일반적인 구현 형태

```node.js
const fs = require('fs')
const rs = fs.createReadStream('file.txt')

rs.on('data', data => {
  // Do something with data..
})

rs.on('error', error => { /* ... */})

rs.on('end', () => { /* ... */ })
```
- data 형태를 지정하지 않으면 buffer가 됨

```node.js
const fs = require('fs')
const rs = fs.createReadStream('file.txt', {
  encoding: 'utf-8',
})

rs.on('data', data => { /* ... */ })
```
인코딩을 지정하면 data가 string으로 돌아오게 됨.



#### stream의 종류

##### 1. Readable

- 스트림으로부터 읽을 수 있음.
- fs.createReadStream
- process.stdin
- 서버 입장의 HTTP 요청
- 클라이언트 입장의 HTTP 응답

##### 2. Writable

- 스트림에 출력할 수 있음.
- fs.createWriteStream
- process.stdout
- 클라이언트 입장의 HTTP 요청
- 서버 입장의 HTTP 응답

##### 3.Duplex

- 이 스트림에 입력을 받을 수도 있고, 출력을 보낼 수도 있음.
- TCP sockets
- zlib streams
- crypto streams

##### 4. Transform

- 입력받은 스트림을 변환해 새로운 스트림을 만듦
- zlib streams
- crypto streams : 암복호화 후 결과가 다름


#### Stream 실습

```node.js
const  rs = fs.createReadStream('../local/big-file',{
  encoding: 'utf-8',
  highWaterMark: 65536 * 2
})
```
highWaterMark 기능을 통해 roof 횟수를 줄인다. 

#### buffer 퍼포먼스 차이 확인해보기

- 25배의 차이
- 25배나 많은 메모리를 쓰는 것
- 메모리에 한 덩어리를 올려두는 것
- stream은 chunk 단위기 때문에 한순간 처리해야할 사이즈는 chunk단위
- 메모리 측면에서 stream단위로 해야 유리하게 작동

#### stream 사용시와 구현시의 주의점들

##### 1. JSON 스트림 처리기 예시

- json의 chunk가 어디서 잘릴지 예상할 수 없음

```json
{"data": 4}
{"data": 63}
{"data": 22}
{"data": }
{"data": 34}
```

##### 2. pipeline과 promise

```node.js
stream.pipeline(
  fs.createReadStream('input'),
  zlib.createGzip(),
  fs.createWriteStream('compressed.gz'),
```
pipeline은 transform stream을 쉽게 활용하게 도와줌

```node.js
const fs = require('fs')
const stream = require('stream')
const zlib = require('zlib')
const util = require('util')

async function gzip() {
  return util.promisify(stream.pipeline)(
    fs.createReadStream('../local/big-file'),
    zlib.createGzip(),
    fs.createWriteStream('../local/big-file.gz'),
  )
}

async function gunzip() {
  return util.promisify(stream.pipeline)(
    fs.createReadStream('../local/big-file.gz'),
    zlib.createGunzip(),
    fs.createWriteStream('../local/big-file.unzipped'),
  )
}

async function main(){
  await gzip()
  await gunzip()
}

main()
```

- pipeline을 통해 stream연결



