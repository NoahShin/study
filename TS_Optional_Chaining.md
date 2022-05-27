# Optional Chaining

타입스크립트 3.7이 릴리즈 되면서 새롭게 사용할 수 있게 된 문법 중에 하나가 바로 "옵셔널 체이닝(Optional Chaining)"이다.

null이나 undefined인 값이 반환되면, 즉시 중단하고 undefined를 반환하는 문법이다.

코드가 즉시 중단 되는 것은 꽤 멋진 것이, 보통의 경우, null이나 undefined에 접근하여 함수를 실행시키는 경우에는 오류가 발생하지만,

 이 경우에는 오류 없이 바로 undefined를 반환 한다는 것이다.

```
const apple = garden?.tree.getApple();  
```

위 코드는 아래와 같다.

 
```
const apple = (garden === nulll || garden === undefined).tree.getApple();
 ```

위의 코드가 헐씬 간결한 것을 알 수 있다.

이제 아래와 같은 코드를 작성하지 않아도 된다.

 
```
let apple = null;

if(garden && garden.tree){
   apple = garden.tree.getApple();
}

if(apple === null) console.log('apple is null');
```

위 코드는 아래의 코드로 대치할 수 있다.

 
```
const apple = garden?.tree?.getApple();

if(!apple) console.log('apple is undefined!');
```




출처: https://jaeheon.kr/155





