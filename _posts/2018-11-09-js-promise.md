---
title: "Promise in javascript"
date: 2018-11-09
categories: _post
comments: true
---

Promises in javascript.(es6)

Promise는 연기된(비동기적으로) 계산의 최종결과에 대한 견본처럼 사용되는 객체이다.

Promise는 코드를 ‘동기식’으로 보이게 하고 콜백과 관련된 문제(예: 콜백지옥)을 피함으로써, 비동기 프로그래밍을 단순화 하는 패턴이다.

Promise를 만드는 것.

우리는 promise를 작업의 완료시간이 불확실하거나 길때 사용한다.
예를 들어, 네트워크 요청은 어느 곳에서나 연결속도에 따라 10ms에서 200ms 걸릴 수 있다.
하지만 우리는 데이터를 가져오는 시간을 기다릴 필요가 없다. 
promise는 이러한 유형의 비동기를 쉽고 간단하게 만드는 것에 관한 것이다.

새로운 promise는 Promise 생성자를 사용하여 만들어진다. 아래의 코드를 보자.

```javascript
const myPromise = new Promise((resolve, reject) => {
    if (Math.random() * 100 <= 90) {
        resolve(‘Hello, Promises!’);
    }
    reject(new Error(‘In 10% of the cases, I fail. Miserably.’));
}) 
```

위의 예제를 보면, Promise 생성자는 두 개의 함수 파라미터를 받고 있다. 이 함수는 실행자 함수라고 불리며 수행할 계산을 표현한다. 저 두 파라미터들은 일반적으로 resolve 또는 reject라고 불리며 각각 실행자 함수의 최종결과에 대한 성공과 실패를 표시한다.

Promise의 사용

위의 예제에서 우리는 myPromise 내에 우리가 만들었던 promise를 저장했다. 그렇다면 우리는 어떻게 resolve 또는 reject함수에 의해서 넘겨진 값에 접근할 수 있을까?
모든 Promise 인스턴스들은 then() 메소드를 갖는다.

```javascript
const myPromise = new Promise((resolve, reject) => {
    if (Math.random() * 100 < 90) {
        console.log(‘resolving the promise…’);
        resolve(‘Hello, Promises!’);
    }
    reject(new Error(‘In 10% of the cases, I fail. Miserably.’));
});
```

```javascript
// 두 함수
const onResolved = (resolvedValue) => console.log(resolvedValue);
const onRejected = (error) => console.log(error);

myPromise.then(onResolved, onRejected);
// 상단의 세 줄을 간결하게 쓸 수도 있다.
myPromise.then((resolvedValue) => {
    console.log(resolvedValue);
}, (error) => {
    console.log(error);
});
```

.then() 메소드는 두 개의 콜백함수를 받는다. 첫번째 콜백은 promise가 resolved인 상태일 때 실행된다. 두번째 콜백은 promise 가 rejected 상태일 때 실행된다.

위의 코드에서 중요한 몇 가지.
우리가 만들었던 promise인 myPromise 에 대해서, then 메소드를 두 번 붙여서 실행시켰다. 그 두 번의 실행은 같은 기능을하지만, 다른 핸들러로 취급된다.

promise 객체의 실행의 결과는 오직 성공이거나 오직 실패이다. 성공 또는 실패를 두 번 할 수 없으며, 성공했던 결과로 실패로 바뀌거나, 실패했던 결과가 성공으로 바뀔 수 없다.
만약, promise 객체의 실행이 성공했거나 실패했을때 이후에 성공/실패 콜백(즉 .then)을 추가하면 올바른 콜백이 호출되어질 것이며, 이벤트가 일찍 발생했음에도 불구하고 올바른 콜백이 호출된다.
위 두가지 말의 의미는 promise 가 마지막 상태에 한번 가까워지면, 그 상태는 .then 메소드가 여러번 붙여져도 바뀌지 않을 것이다. (계산은 다시는 하지 않는다.)

에러핸들링
실행자 함수 내에 에러가 발생했을때 발생하는 것을 살펴본다.
에러가 발생했을때, then() 메소드의 두번째 인자가 실행된다.

아래의 코드는 90%의 확률로 myPromise 의 reject 함수가 실행된다.

```javascript
const myPromise = new Promise((resolve, reject) => {
    if (Math.random() * 100 < 90) {
        reject(new Error(‘reject 함수에 의해 이 promise 는 rejected 됨.’));
    } else {
        throw new Error(‘이 promise 는 throw 키워드에 의해 에러가 발생됐고 rejected 됨.’)
    }
});

myPromise.then(
    () => {console.log(‘resolve’)},
    (error) => {console.log(error.message)}
);
```

위의 코드에서 10, 11번 줄에서 resolve와 reject 콜백들을 정의했다. 참고로, reject 콜백은 에러가 발생했을때 실행될 것이다. 위와 같이 에러를 핸들링 하는 방법도 있지만, .catch 메소드를 promise 에 붙여서 에러를 핸들링 할 수도 있다.

연쇄 promise

.then() 메소드와 .catch() 메소드는 항상 promise를 반환한다. 그러므로 다수의 then 메소드를 함께 연쇄하여 호출할 수 있다.

예를 들어, 다음 코드의 promise는 명시된 시간 이후에 resolve를 반환할 것이다.

```javascript
const delay = ms => new Promise(
    (resolve) => setTimeout(resolve, ms)
);

delay(3000)
  .then(() => 10 / 2)
  .then(a => a/2)
  .then(b => b/2)
  .then(c => console.log(c)) // 3초 뒤에 5가 출력될 것이다.
```



