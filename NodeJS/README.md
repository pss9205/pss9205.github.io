---
sort: 1
---

# NodeJS

{% include list.liquid all=true %}


## Node.JS

> Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine.

Chrome V8을 기반으로 만들어진 자바스크립트 런타임 엔진.   
v8엔진 이외에도 libuv라는 라이브러리를 사용한다. libuv는 노드의 특성인 이벤트 기반, 논 블로킹 IO모델을 구현한다.

```note
런타임 : 특정 언어로 만든 프로그램들을 실행할 수 있는 환경
```

### Event-Driven
이벤트가 발생할 때 미리 지정해둔 작업을 수행하는 방식
NodeJS는 Event-Driven 방식으로 동작하며, 이벤트가 발생하면 이벤트 리스너에 등록해둔 콜백함수를 호출한다.
```
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```
이벤트 루프는 이벤트 발생 시 호출할 콜백 함수들을 관리하고, 호출된 콜백함수의 실행 순서를 결정.   
위의 그림처럼, 여러 개의 내부 단계를 가지고 있으며 각 단계마다 큐가 존재한다. 각 큐에는 이벤트 발생 후 백그라운드에서 넘어온 타이머나 이벤트 리스너의 콜백함수들이 위치함. 이벤트 루프는 단계들을 RR방식으로 순회하며 큐에 있는 것들을 처리한다.

### Blocking I/O vs NonBlocking I/O
NodeJS는 I/O를 논블로킹 방식으로 처리한다. I/O작업은 libuv로 넘겨지고 자신의 스레드 풀이나, OS의 커널에게 요청하게 된다.
작업이 완료되면 이벤트 루프에 알려주고 콜백함수로 등록된다.

#### Blocking 
현재의 I/O가 완료될때까지 추가 Javascript 작업이 수행되지 않고 대기 상태에 들어간다.
Blocking작업이 수행되는 중에는 이벤트 루프가 다음 작업을 계속할수 없음.
NodeJS에서 제공되는 메소드중 Sync로 끝나는 메소드들이 Blocking으로 동작하는 메소드
```scss
const fs = require('fs');
const data = fs.readFileSync('/file.md'); // 파일이 다 읽을때까지 다음 메소드 실행안됨
console.log(data);
moreWork(); // console.log 출력 후 실행됨
```

#### Non-Blocking
I/O 작업 완료를 기다리지 않고 추가 JavaScript작업이 수행된다.  
해당 작업의 콜백 함수는 이벤트 루프의 큐에 추가되며, 작업이 완료되면 이벤트 루프에 의해 콜백 함수가 실행되게 된다.

```scss
function longTask(){
	//some job taking long time..
	console.log("long long..");
}

console.log('start');
setTimeout(longTask,10);
console.log('end'); //longTask의 완료를 기다리지 않고 다음 동작 수행
```

### Single Thread
NodeJS는 내부에 여러 개의 스레드를 가지지만 제어할 수 있는 것은 한개의 스레드이다.   
Non-Blocking방식을 이용해 단일 스레드에서도 여러 요청을 동시에 처리가 가능해진다.  
```tip
특정 동작(암호화,IO,압축등)을 수행할땐 멀티스레드로 동작하는데 이를 스레드 풀이라 한다.   
노드 12 버전 이상에서는 워커 스레드를 사용해 멀티 스레드로 처리하는 것도 지원된다.
```