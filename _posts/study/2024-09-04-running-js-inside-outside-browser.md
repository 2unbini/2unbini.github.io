---
title: "JavaScript를 브라우저 환경 밖에서 쓸 수 있게 한 Node.js"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - study
tags:
    - JavaScript
    - Node.js
last_modified_at: 2024-09-04
---

## 너무 당연하게 사용해서 몰랐던 이야기

> Node.js는 크로스플랫폼 오픈소스 자바스크립트 런타임 환경으로 윈도우, 리눅스, macOS 등을 지원한다. Node.js는 V8 자바스크립트 엔진으로 구동되며, 웹 브라우저 바깥에서 자바스크립트 코드를 실행할 수 있다.<br>
> *위키백과, Node.js*

다른건 그렇다 치고, `웹 브라우저 바깥에서 자바스크립트 코드를 실행할 수 있다`는 말이 무슨 뜻인지 의문스러워졌다.

??? 자바스크립트 코드는 웹 브라우저 밖에서 실행 못해???

ㅇㅇ Node.js 나오기 전까진 못했음

??!!

## Running JS Inside Browser

지금이야 서버 사이드로도 사용할 수 있지만, 태초의 자바스크립트는 웹 브라우저에서 구동하는 `스크립트`언어였다.

> Originally JavaScript had a similar purpose, with the run-time environment that it targeted being the browser. <br>
> *[How does JS work under the hood? - Answer 1](https://qr.ae/p22Qt4)*

웹 브라우저마다 가지고 있는 `엔진`을 통해 자바스크립트 코드를 읽어 실행시키는 것이 JavaScript가 브라우저 내부에서 동작하는 방식이다.

브라우저가 가지고 있는 엔진이 자바스크립트 코드를 파싱하고, 실행 스택에 추가하고, 스택에서 하나씩 빼내어 실제로 구동하는 것.

구글 크롬에서 사용되는 V8, Firefox - SpiderMonkey, Safari - JavaScriptCore (aka Nitro) 등이 자바스크립트 엔진에 해당한다.

## Running JS Outside Browser

> 그럼 결국 자바스크립트는 브라우저 내에서만 실행 가능한거 아닌가?

Node.js가 자바스크립트 엔진(V8)을 가지고 있으면서 독자적으로 코드를 읽고, 파싱하고, 실행시켜 브라우저 밖에서 실행 가능하도록 만들었다.

> 엥? 엔진은 브라우저 내부에 있다면서?

Node.js가 가지고 있는 엔진인 V8은 현재 구글 크롬에서 사용되고 있고, 구글에서 만들었으며, C++ 코드로 구성되어있다.

> Interestingly, V8 is not limited to browsers; it can also be run independently and embedded into any C++ application. <br>
> *[The V8 JavaScript Engine, Medium](https://medium.com/@manikmudholkar831995/the-v8-javascript-engine-d1434ca77c96)*

이 엔진의 특이점이 있다면, 브라우저에 독립적이다. 여러 아티클, 도큐먼트에서 V8을 두고 `powered by google chrome`이라고 해서 크롬 브라우저에 종속된 엔진이라고 생각했는데 C++ 어플리케이션에까지 추가해 쓸 수 있는 범용성 있는 엔진이었다.

[Node.js 코드](https://github.com/nodejs/node/tree/main/deps/v8)를 보면 dependency로 보이는 부분에 v8 코드가 추가되어있다. C++로 되어 있는 것을 확인할 수 있다.

## JavaScript와 Node.js

막연히 JavaScript로 서버사이드 개발을 할 수 있는 프레임워크로 Node.js를 생각해왔다. 근데 Express는 왜 쓰지? 라는 생각이 문득 들었고, JavaScript - Node.js - Express 세 친구들의 관계를 추적하다보니 비하인드 스토리를 알 수 있었다.

브라우저에서 자동으로 읽고 컴파일해주니까 JavaScript 내부적으로 컴파일하는 방식이 있다고 착각한 것..!

결국 JavaScript는 브라우저나 Node.js와 같은 프레임워크가 없으면 그냥 코드 조각에 불과하네? 개발을 할수록, 이런 부분들을 찾아볼수록 언어마다 특징이 다 다른게 재미있다.

## 참고 자료
- [Node.js/Node Github](https://github.com/nodejs/node/tree/main)
- [Node.js Docs - Difference between Node.js and the Browser](https://nodejs.org/en/learn/getting-started/differences-between-nodejs-and-the-browser)
- [Medium - The V8 JavaScript Engine](https://medium.com/@manikmudholkar831995/the-v8-javascript-engine-d1434ca77c96)
- [Quora - What exactly does running JavaScript inside a browser and outside a browser mean](https://www.quora.com/What-exactly-does-running-JavaScript-inside-a-browser-and-outside-of-a-browser-mean)
- [Quora - How does JavaScript work under the hood? - Answer 1](https://qr.ae/p22Qt4)
- [Quora - How does JavaScript work under the hood? - Answer 2](https://qr.ae/p22QKP)