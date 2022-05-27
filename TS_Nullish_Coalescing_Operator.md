
# Nullish Coalescing

Nullish Coalescing Operator(NCO) 는 새로운 ECMAScript 의 기능 
기존의 (||) 연산자와 비슷한 기능을 하는데

물음표 두 개(??) 를 통해 왼쪽의 겂이 null 혹은 undefined 인지 확인. 

만약 왼쪽의 값이 null 혹은 undefined 라면 NCO 의 오른쪽에 위치한 값이 반환됩니다.  


```
const foo = undefined;
const bar = 'test';
const res = foo ?? bar;
// res === 'test'
```








출처: https://boxfoxs.tistory.com/427 [박스여우 - BoxFox:티스토리]