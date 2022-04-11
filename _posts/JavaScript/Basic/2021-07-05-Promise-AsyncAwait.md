---
layout: post
title: "Promise & Async/Await"
date: 2021-07-05 00:17:00 +0900
categories: [javascript]
tags: [javascript]
---

## Callback & Asynchronous

- 콜백함수  
  다른 `코드`의 인자로 넘겨지는 함수. 넘겨받은 `코드`는 특정 조건이 만족하게되면 이 함수를 호출(call back)하게 됨.

synchronous callback은 즉시 호출이 되고, asynchronous callback의 경우 특정 작업이 완료된 후 호출 된다.

```jsx
//synchronous callback
function printImmediately(print) {
  print();
}
printImmediately(() => {
  console.log("hello sync");
});

//asynchronous callback
function printWithDelay(print, timeout) {
  setTimeout(print, timeout);
}
printWithDelay(() => console.log("hello async"), 1000);
```

- 비동기 처리를 하다보면 콜백함수에 콜백함수가 연결되는 CallbackChain이 발생할 수 있음. chain이 깊어지면 아래와 같은 문제가 생김
  1. 떨어지는 가독성
  2. 유지보수 및 디버깅 어려움
  3. Callback Hell - 콜백에 콜백이 꼬리를 무는 문제

## Promise

Promise는 비동기적 작업을 위한 객체로 함수에 콜백을 전달하는게 아닌, 콜백을 첨부하는 방식으로 동작.
이를 사용하여 Callback chain의 문제를 해결할수 있음.

- 기존 callback 방식  
  Success, fail에 해당하는 함수를 만들어 async함수의 인자로 넘겨주게됨.  
  async함수는 동작 결과에 따라 callback 호출

```jsx
function successCallback(result) {
  console.log("Audio file ready at URL: " + result);
}

function failureCallback(error) {
  console.log("Error generating audio file: " + error);
}

createAudioFileAsync(audioSettings, successCallback, failureCallback);
```

- Promise 방식  
  async함수는 Promise객체를 리턴하고, callback함수를 리턴된 Promise객체에 붙여 사용할 수 있음.

```jsx
createAudioFileAsync(audioSettings).then(successCallback, failureCallback);
```

**Promise의 State : pending -> fulfilled or rejected**

### 구성

- Producer vs Consumer

  Producer - Promise 객체  
   생성 되는 순간 바로 execute 메소드 동작

  ```jsx
  //작업이 성공하는 경우(fulfilled) resolve를, 실패하는 경우(rejected) resject를 호출한다.
  const promise = new Promise((resolve, reject) => {
    //doing some heavy work
    console.log("doing");
    setTimeout(() => {
      //resolve("test success");
      reject(new Error("no network"));
    }, 2000);
  });
  ```

  Consumer - Promise객체의 콜백 함수들  
   then, catch, finally로 동작 처리  
   then() - 작업이 성공하는 경우에 대해 정의(두번째 인자로 실패하는 경우에 대해 정의 가능)  
   catch() - 작업이 실패한 경우에 대해 정의  
   finally() - 성공 실패 상관없이 항상 처리되야 하는 경우에 대해 정의

  ```jsx
  promise
    .then((value) => {
      console.log(value);
    })
    .catch((error) => console.log(error))
    .finally(() => console.log("final!"));
  ```

- Error Handling

  catch로 error 처리 가능. 또는 then의 두번째 인자

  ```jsx
  const getHen = () =>
      new Promise((resolve, reject) => {
      setTimeout(() => resolve("hen"), 1000);
      });
  const getEgg = (hen) =>
      new Promise((resolve, reject) => {
      setTimeout(() => reject(new Error(`${hen} => egg`)), 1000);


  const cook = (egg) =>
      new Promise((resolve, reject) => {
      setTimeout(() => resolve(`${egg} => fried egg`), 1000);
      });

  getHen()
      .then((hen) => getEgg(hen))
      .catch((error) => {
      return "bread";
      }) //앞의 then에서 발생한 에러 처리
      .then((egg) => cook(egg))
      .then((meal) => console.log(meal));
  ```

### Promise Chaining

then/catch함수는 새로운 promise를 리턴한다. 이를 통해 chaining이 가능하며, 이전 비동기 작업이 끝난후 다른 비동기 작업을 순차실행할 수 있음.

1. then에서 프로미스가 리턴되는 경우, Promise는 그 다음 then에 의해 처리됨
2. Promise에서 그냥 값을 리턴하면 Promise.resolve()를 리턴하는것과 같음

   ```jsx
   Promise.resolve(10)
     // 1. Promise를 리턴하므로, 다음 then은 이 프로미스의 작업 완료를 기다린 후 처리됨
     .then((val) => {
       return new Promise((resolve, reject) =>
         setTimeout(() => resolve(val - 1), 1000)
       );
     })
     .then((val) => console.log(val)); //9

   Promise.resolve(10)
     //1. Promise가 아닌 값이 리턴되므로, 다음 then은 바로 값을 처리하려하여 다른 값이 출력됨
     .then((val) => {
       return setTimeout(() => val - 1, 1000);
     })
     .then((val) => console.log(val)); //이상한 값
   ```

### Promise method

- all : 모든 Promise의 처리를 기다림

```jsx
function pickAllFruits() {
  return Promise.all([getApple(), getBanana()]).then((fruits) =>
    fruits.join(" + ")
  );
}

pickAllFruits().then(console.log);
```

- race : 어느 한개의 Promise 작업만 끝나도 리턴

```jsx
function pickFirstOne() {
  return Promise.race([getApple(), getBanana()]);
}

pickFirstOne().then(console.log);
```

## Async - Await

Promise역시 반복사용되면 가독성이 떨어짐

Async - Await을 사용하면 기존 Sequential 코드 흐름과 유사하게 비동기 코드 작성 가능

Promise의 Syntactic sugar

### Async

- Promise 방식

  ```jsx
  function fetchUser() {
    return new Promise((resolve, reject) => {
      // return `name`; // pending
      resolve(`name`); // fulfilled
      // reject(new Error(`error`)); // rejected
    });
  }

  const user = fetchUser();
  user.then(console.log);
  ```

- Async 방식

  Async를 사용하면 코드 블록이 Promise로 리턴됨

  ```jsx
  //function declaration
  async function fetchUser() {
    return `ellie`; //==return Promise.resolve(`ellie`)
  }

  //function expression
  const fetchUser = async function () {
    return `ellie`;
  };

  //arrow function
  const fetchUser = async () => {
    return `ellie`;
  };

  const user = fetchUser();
  user.then(console.log);
  ```

### Await

async가 있는 경우에만 사용이 가능함

함수의 작업이 끝날때까지(fulfilled 또는 rejected) 대기

async-await을 사용하여 기존 언어와 동일한 방식으로 코드 작성가능

에러처리 역시 try-catch를 사용해야함

- Promise 방식

  ```jsx
  function delay(ms) {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }

  function getApple() {
    return delay(1000).then(() => `🍎`);
  }
  function getBanana() {
    return delay(1000).then(() => `🍌`);
  }

  function pickFruits() {
    return getApple().then((apple) => {
      return getBanana().then((banana) => `${apple} + ${banana}`);
    });
  }
  pickFruits().then((result) => console.log(result));
  ```

- Await 방식

  ```jsx
  function delay(ms) {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }
  async function getApple() {
    await delay(1000);
    return "apple";
  }

  async function getBanana() {
    await delay(2000);
    return "banana";
  }

  async function pickFruits() {
    const apple = await getApple();
    const banana = await getBanana();
    return `${apple}+${banana}`;
  }

  pickFruits().then(console.log);
  ```

출처 :
[wiki-Callback](<https://en.wikipedia.org/wiki/Callback_(computer_programming)>),[mozilla-Promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/),[youtube-Promise](https://youtu.be/JB_yU6Oe2eE)
