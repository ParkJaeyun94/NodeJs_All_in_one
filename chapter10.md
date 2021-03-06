## NoSQL 데이터베이스의 특징

- Not Only SQL: 스키마 없이 데이터를 표현하는 것이 주된 특징인 일련의 데이터베이스들을 의미함.
- 예시: DOC based
JSON 형태이더라도 충분이 데이터베이스의 기능을 한다.
쿼리에서 CRUD가 모두 가능하다면 데이터베이스의 조건 만족.

#### 1. 일반적인 특징

* 정해진 스키마가 없음 (데이터가 들어갈 틀이 없음)
* 데이터베이스의 종류에 따라 그 특성이 매우 다름.

#### 2. 장점

* 높은 수평 확장성
* 초기 개발의 용이성
* 스키마 설계의 유연성

###### 1) 수직 확장 

* 한 인스턴스의 가용 자원(CPU, memory, storage)을 키워 더 큰 로드를 감당
* 어디까지나 한 인스턴스를 키우는 것이기 떄문에 확장이 제한적.
* ex. 기존 1만명의 접속 유저가 2만명으로 늘어나면 문제가 생김. cpu, memory를 2배로 늘리면 될 것. 
* 만약 10만 명으로 늘어난다면, 한 인스턴스를 키우는 것은 확장에 제한. 언젠가는 수평확장이 일어나야 함.

###### 2) 수평 확장

* 더 많은 인스턴스를 만들어 더 큰 로드를 감당함.
* 수평 확장이 가능한 구조, 운영 비용만 감당할 수 있다면 이론적으로 얼마나 많은 로드라도 받아낼 수 있음.
* ex. 서버를 10대로 늘리면 됨. 따라서 수평확장이 확장성이 가장 높은 것임.
* NoSQL의 장점이 수평확장이 가능하다는 것. 문서는 모든 곳에서 일정한 구조를 유지할 필요가 없음. 어디에 들어가든, 어떤 모양이든 상관이 없음.
* 여러 인스턴스에 띄우는 샤딩이 훨씬 수월함.

#### 3. 단점?

* 표준의 부재
* SQL에 비해 약한 query capability
* data consistency를 어플리케이션 레벨에서 보장해야 함.
  * 새로운 property가 생기면 모든 유저에 추가를 해줘야 함. migration를 프로그래머가 직접 관리해줘야 함. 
  * RDBMS에서는 ALTER COLUMN이면 되는데, 직접 추가할시 휴먼 에러들 -> 버그로 이어질 확률 높아짐.

## NoSQL 데이터베이스의 현재

#### 1. 종류

###### 1) Key-Value
* Redis, AWS DynamoDB
* 모든 레코드는 key-value의 페어
* value는 어떤 값이든 될 수 있음.
* NoSQL 데이터베이스의 가장 단순한 형태임.

###### 2) Document-based
* DynamoDB, CouchDB
* 각 레코드가 하나의 문서가 됨.
* 문서는 데이터베이스에 따라 XML, YAML, JSON, BSON 등을 사용함.
* 문서의 내부적 구조를 통한 쿼리 최적화, 활용성 높은 API 등이 제공됨.
* Semi-structured.

###### 3) Graph-based
* Neo4j, AWS Neptune
* 그래프 이론을 바탕으로, 데ㅣ터베이스를 그래프로 표현함.
* 그래프는 node(객체)와 edge(관계), 그리고 property(객체의 속성)로 이루어짐.
* 관계가 first-class citizen이기 때문에 관계 기반 문제(실시간 추천 등)에 유리함.
  * 관계도 직접 다룰 수 있는 대상으로 봄. 


## MongoDB 모델링

```node
const { MongoClient } = require('mongodb');

const uri = "mongodb+srv://<username>:<password>@cluster0.8t5vr.mongodb.net/myFirstDatabase?retryWrites=true&w=majority";
const client = new MongoClient(uri,
  { useNewUrlParser: true,
    useUnifiedTopology: true
  });
```

> npm install mongodb


###### 1) mongoDB 사용

```node
const { MongoClient } = require('mongodb');

const uri = `mongodb+srv://dbUser:qlalfqjsgh1@cluster0.8t5vr.mongodb.net/myFirstDatabase?retryWrites=true&w=majority`;

const client = new MongoClient(uri,
  { useNewUrlParser: true,
    useUnifiedTopology: true
  });
```

###### 2) mongoDB 함수 사용

```node
async function main() {
  await client.connect()
  await client.close()
 }

main()
```
###### 3) collection 정의

```node
const users = client.db('fc21').collection('users')
```

###### 4) Reset
```node
  await users.deleteMany({})
```

###### 5) Insert
```node
  await users.insertMany([
    {
      name: 'Foo',
      birthYear: 2000,
      contacts: [
        {
          type: 'phone',
          number: '+82100000111'
        },
        {
          type: 'home',
          number: '+8210223311'
        },
      ],
      city: '서울',
    },
    {
      name: 'Bar',
      birthYear: 1995,
      contacts: [
        {
          type: 'phone',
        },
      ],
      city: '부산',
    },
    {
      name: 'Baz',
      birthYear: 1990,
      city: '부산',
    },
    {
      name: 'Poo',
      birthYear: 1993,
      city: '서울',
    }
  ])
```

###### 6) Update

```node
  await users.updateOne(
    {
      name: 'Baz'
    },
    {
      $set: {
        name: 'Boo'
      }
    }
  )
```

###### 7) Delete

```node
await users.deleteOne({
    name: 'Baz',
  })
```

###### 8) Find

특정값 이상 필터, 정렬
```node
const cursor = users.find(
    birthYear: {
      $gte: 1990,
    },
    {
      sort: {
        birthYear: -1,
      }
    }
  )
  await cursor.forEach(console.log)
```
nesting된 데이터로 찾을 경우
```node
const cursor = users.find(
{
  'contacts.type': 'phone'
}
await cursor.forEach(console.log)
```

###### 9) 합치기, 필터링 조건 2개 이상일 경우, count하기

```node
const cursor = users.aggregate([
    {
      $lookup: {
        from: 'cities',
        localField: 'city',
        foreignField: 'name',
        as: 'city_info',
      },
    },
    {
      $match: {
        $and: [
          {
            'city_info.population': {
              $gte: 500
            },
          },
          {
            birthYear: {
              $gte: 1995,
            }
          }
        ]
      }
    }
    ,
    {
      $count: 'num_users'
    }
  ])
```

### 참고하자

https://docs.mongodb.com/drivers/node/v3.6/usage-examples/

