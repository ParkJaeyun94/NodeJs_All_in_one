### Github 관리 CLI 프로그램 만들기

#### 1. 요구사항 설정과 설계

> https://github.com/tj/commander.js/

> https://github.com/octokit/octokit.js

---

#### 2. commander로 CLI 뼈대 구축하기

###### 1) tj/commander 설치

> node src/main.js add -p src/*

```
[
  'C:\\Program Files\\nodejs\\node.exe',
  'C:\\Users\\HOME\\WebstormProjects\\node_pj\\src\\main.js',
  'add',
  '-p',
  'scr/main.js'
]

```

> npm install commander

###### 2) option 넣기

```node
const { program } = require('commander');

program.version('0.0.1');
```

```node
program
  .option('-d, --debug', 'output extra debugging')
  .option('-s, --small', 'small pizza size')
  .option('-p, --pizza-type <type>', 'flavour of pizza');

program.parse(process.argv);

const options = program.opts();
if (options.debug) console.log(options);
console.log('pizza details:');
if (options.small) console.log('- small pizza size');
if (options.pizzaType) console.log(`- ${options.pizzaType}`);
```

###### 3) 명령어 넣기

```node
program
  .command('list-bugs')
  .description('List issues with bug label')
  .action(()=>{
    console.log('List bugs!')
  })

program.parse()
```

###### 4) 응용

```node
program
  .command('list-bugs')
  .description('List issues with bug label')
  .action(async ()=>{
    const result = await fs.promises.readFile('../.prettierrc')
    console.log('readFile result: ', result)
    console.log('List bugs!')
  })

program
  .command('check-prs')
  .description('Check pull request status')
  .action(async ()=>{
    console.log('Check PRs!')
  })
program.parse()
```
- 2개 이상의 command
- async 가능

---

#### 3. 토큰 발급받고 dotenv를 통해 프로젝트에서 사용하기



#### 4. octokit 활용해 저장소에 접근해보기

#### 5. 커스텀 규칙으로 저장소 관리하기 
