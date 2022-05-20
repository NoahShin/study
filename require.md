코드를 보다가  
```
require('./config/db')
```

이 코드를 봤다. 

보통 
```
const a = require('경로')
```
이렇게 하지 않나? 

그래서 require 를 좀 더 공부하고자.


### require 개요
- require 문법은 CommonJs. 즉, Node.js의 환경에서 작동하는 문법이다.

- Node.js 환경이란 결국 브라우저가 아닌 런타임(보통 서버사이드)에서 실행된다는 것을 의미한다.
- 그래서 브라우저에서는 require가 아닌 import라는 ES6에서 새롭게 도입된 문법을 사용한다.


## 예시
Node.js에서는 module.exports와 exports를 통해 모듈을 바깥으로 내보낼 수 있다.
```
// export-ex.js

class Foo {
  console.log('예시 모듈 실행!');
}
module.exports = Foo;
```


```
// import-ex.js

const foo = require('./export-ex.js');

foo(); // '예시 모듈 실행!'
```
이건 알고 있었는데..


































참조 : https://velog.io/@josworks27/%EB%AA%A8%EB%93%88-%EC%82%AC%EC%9A%A9%EC%9D%84-%EC%9C%84%ED%95%9C-require-%EB%AC%B8%EB%B2%95-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC


