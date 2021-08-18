---
title: Publish-Subscribe 패턴에 대해
author: Chan Ho Han
date: 2021-08-18 12:10:00 +0900
categories: [디자인패턴]
tags: [옵저버패턴, 발행구독패턴]
comments : true
---

## 💡 Publish–subscribe pattern 이란?
발행-구독 모델은 비동기 메시징 패러다임입니다.
<br>
유튜브 구독으로 생각해 보겠습니다. 유튜브 사용자 X가 유튜버 Y를 구독하겠다고 신청을 하게 되면, 구독이 되는 것을 생각하시면 됩니다. 유튜버 Y가 자신의 구독자들에게 알림을 보낸다면, 중간에 Message Broker가 유튜버 Y의 구독자들에게 알림을 보내기 때문에, 유튜버 Y는 구독자들이 아닌 Message Broker와만 관계를 맺으면 됩니다.

![image](https://user-images.githubusercontent.com/46598292/129820554-fb2e9d5d-ab96-4ce5-9657-c28b8cc0c8dd.png)
이미지 출처 : [microsoft docs](https://docs.microsoft.com/ko-kr/dotnet/architecture/dapr-for-net-developers/publish-subscribe)


## 💡 Observer Pattern과 비교
[Observer Pattern](https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4)과 유사하지만, Observer Pattern의 경우에는 observer와 subject가 직접적인 관계를 갖고 있는 것에 반해,  pub-sub 패턴의 경우 publisher와 subscriber가 서로를 전혀 몰라도 상관 없다는 차이가 있습니다.


## 💡 이걸 왜 알아야 해?
아무리 모듈화를 잘 해놓았다고 하더라도 웹 애플리케이션(웹 앱)은 선형적으로 동작하는 일이 거의 없기 때문에 “비동기” 와 “경합 상태”를 항상 고려해야합니다. 이 pub-sub 패턴은 여러 모듈들 사이에서 어떤 식으로 데이터를 주고받을지를 고민할 때 많이 쓰는 패턴입니다. 

![image](https://user-images.githubusercontent.com/46598292/129821475-2e31938f-a125-41c1-8c2b-213231ad4253.png)
이미지 출처 : [here](https://rinae.dev/posts/why-every-beginner-front-end-developer-should-know-publish-subscribe-pattern-kr)

## 💡 구현을 위해 EventEmitter 간단 설명

EventEmitter의 동작원리와 사용방법은 Node개발의 핵심 중 하나입니다. EventEmitter는 이벤트의 비동기처리를 가능하게 합니다. 핵심 구조는 아래 코드를 통해 살펴보겠습니다.

```javascript
const eventEmitter = require('events').EventEmitter;
const _emitter = new eventEmitter();
let counter = 0;

setInterval(function() { _emitter.emit('timed', counter++);}, 3000);
_emitter.on('timed', function(data){
  console.log('tiemd '+data);
})
```
_emitter에 새로운 EventEmitter 객체를 참조시켜 놓음으로써 이벤트를 등록할 수 있게 됩니다. setInterval을 통해서 3초마다 timed 이벤트를 발생시키고, _emitter.on을 통해서 timed 이벤트에 대한 익명 함수를 핸들러로 등록시킵니다. 이런 식으로 이벤트를 등록하고, 사용할 수 있습니다.


## 💡 EventEmitter를 통해 pub-sub 구현해보기
```javascript
class Youtuber{ // 유튜버 클래스
    constructor(name) {
        this.name = name;
    }
    pubAlert(name) {
        console.log(`${this.getName}님 ! ${name}님 께서 구독을 하셨답니다 !!`)
    }
    get getName(){
        return this.name;
    }
}

class Sub{ // 구독자 클래스
    constructor(name, age) {
        this.name = name;
        this.age = age
    }
    subAlert(name) {
        console.log(`${this.name}님 ! 유튜버 ${name}에게 알림이 왔어요 !!`)
    }
    get getName(){
        return this.name;
    }
}

class MessageBroker{
    constructor() {
        this.pubList = {};
    }
    addSub(pub, sub){
        if (!(pub.getName in this.pubList)) {
            console.log("말도 안되는 일이지만, 유튜버가 없습니다 .. (?)")
            return undefined;
        }
        this.pubList[pub.getName].push(sub); // 구독자 객체 추가
    }
    subscribers(pub) {
        return this.pubList[pub.getName];
    }
}

const youtuber = new Youtuber("이지금");
const subA = new Sub("장그래", 26);
const subB = new Sub("장백기", 26);
const subC = new Sub("안영이", 26);
const messageBroker = new MessageBroker();

messageBroker.pubList[youtuber.getName] = []; // 유튜버 추가

const eventEmitter = require('events').EventEmitter;
const event = new eventEmitter();
event.on('subscribe', function(youtuberA, subscriber, messageBroker) { // 'subscribe'라는 이벤트 등록. 구독자 객체를 받음
    messageBroker.addSub(youtuberA, subscriber);
    youtuberA.pubAlert(subscriber.getName);
});

event.on('youtuber_alert', function(youtuberA){
    let sub = messageBroker.subscribers(youtuberA); // youtuber의 구독자 배열을 받아옴
    for (let subObject of sub) {
        subObject.subAlert(youtuberA.getName);
    }
})

// 구독해보기
console.log('------- 구독 -------')
event.emit('subscribe', youtuber, subA, messageBroker);
event.emit('subscribe', youtuber, subB, messageBroker);
event.emit('subscribe', youtuber, subC, messageBroker);
console.log('------- 구독 완료 -------')

console.log('-------구독자들에게 알림 보내기-------')
// 3초에 한 번씩 알림 보내기
setInterval(function(){
    event.emit('youtuber_alert', youtuber);
}, 3000);
// 1초에 한 번씩 출력
setInterval(function(){
    console.log('이 출력은 비동기임을 보여주기 위함입니다 !');
}, 1000);
```

- ## 코드 설명
1. 처음에 youtuber, 구독자 객체, messageBroker를 각각 1개, 3개, 1개를 만듭니다. 
2. 구독에 해당하는 'subscribe'와 유튜버가 알림을 보내는 'youtuber_alert' 이벤트를 각각 등록해줍니다.
3. 구독자들은 유튜버를 구독하게 되고, 유튜버는 구독자가 구독했음을 알게 됩니다.
4. setInterval를 통해서 3초에 한 번씩 유튜버에게 알림을 받게됩니다. 이벤트의 익명함수는 객체를 받게 되는데, messageBroker에 등록된 유튜버와 그에 대응되는 구독자들 객체에 구현되어있는 함수를 호출함으로써 각 객체가 알림을 받았음을 알게 됩니다.
5. 모든 작업이 비동기로 처리되고 있음을 보여주기 위해 1초에 한 번씩 의미없는 문구를 출력해줍니다.


- ## 실행 결과

```
➜  pub-sub git:(master) ✗ node test.js
------- 구독 -------
이지금님 ! 장그래님 께서 구독을 하셨답니다 !!
이지금님 ! 장백기님 께서 구독을 하셨답니다 !!
이지금님 ! 안영이님 께서 구독을 하셨답니다 !!
------- 구독 완료 -------
-------구독자들에게 알림 보내기-------
(0) 이 출력은 비동기임을 보여주기 위함입니다 !
(1) 이 출력은 비동기임을 보여주기 위함입니다 !
장그래님 ! 유튜버 이지금에게 알림이 왔어요 !!
장백기님 ! 유튜버 이지금에게 알림이 왔어요 !!
안영이님 ! 유튜버 이지금에게 알림이 왔어요 !!
(2) 이 출력은 비동기임을 보여주기 위함입니다 !
(3) 이 출력은 비동기임을 보여주기 위함입니다 !
(4) 이 출력은 비동기임을 보여주기 위함입니다 !
장그래님 ! 유튜버 이지금에게 알림이 왔어요 !!
장백기님 ! 유튜버 이지금에게 알림이 왔어요 !!
안영이님 ! 유튜버 이지금에게 알림이 왔어요 !!
(5) 이 출력은 비동기임을 보여주기 위함입니다 !
(6) 이 출력은 비동기임을 보여주기 위함입니다 !
(7) 이 출력은 비동기임을 보여주기 위함입니다 !
장그래님 ! 유튜버 이지금에게 알림이 왔어요 !!
장백기님 ! 유튜버 이지금에게 알림이 왔어요 !!
안영이님 ! 유튜버 이지금에게 알림이 왔어요 !!
```


## 💡 참고자료


 깊게 이해하기 좋은 [링크](https://rinae.dev/posts/why-every-beginner-front-end-developer-should-know-publish-subscribe-pattern-kr) 

https://arclife.tistory.com/7  
https://semtax.tistory.com/24
