레퍼런스 : 

https://developer.mozilla.org/ko/docs/Web/HTTP/Messages

https://preamtree.tistory.com/38

# HTTP
> HyperText Transfer Protocol

![](https://i.imgur.com/eZcQKTU.png)

텍스트 기반으로 통신할 수 있는 프로토콜

## HTTP 구성 요소
![](https://i.imgur.com/eyXxJou.png)

### 요청 구조

![](https://i.imgur.com/RXIjMpQ.png)

1. HTTP 요청의 첫째줄에는 <strong>HTTP 메서드</strong>와 <strong>타겟 URL</strong> HTTP 버전이 명시되어 들어간다.

2. HTTP 헤더는 대소문자를 구분하지 않는 문자열 (Host, User-Agent) : 헤더값으로 구성되어져 있다.
    - Request Header : 요청의 내용을 구체화시키는 헤더. 제약 사항을 기재하기도 한다. If-None
    - General Header : 메시지 전체에 적용되는 헤더
    - Entity Header : body에 적용되는 Header Content-Length,Content-Type이 대표적이다.

3. 요청의 본문은 헤더의 공백문자 뒤에 담아 넣을 수 있다.
    - 요청 타입은 Entity Header을 통해서 표기한다.

### 응답 구조
![](https://i.imgur.com/TExHkHB.png)

기본적으로 요청 구조와 많이 유사하지만

응답구조의 첫번째 줄에는 응답에 대한 상태코드가 같이 표기된다.

## HTTP의 문제점

HTTP는 평문으로 발송되기 때문에 도청하기 아주 쉽다.
통신 상대를 확인하지 않기 때문에 위장이 쉽다.

<strong>보안에 너무 취약하다</strong>

# HTTPS

> HTTP + Secure Socket Layer (SSL)

## 대칭키

![](https://i.imgur.com/3Z1UFGf.gif)

암호화와 복호화를 같은 키를 이용해서 할 수 있는 암호화 알고리즘.

공개키 - 개인키에 비해 속도가 빠르지만

통신하는 양측이 동일한 대칭키를 써야한다는 문제점이 있다.


## 공개키-개인키

![](https://i.imgur.com/xV2IPDF.gif)

웹상에 공개한 공개키와, 서버가 가지고 있는 개인키를 이용해서

클라이언트는 공개키를 통해서 암호화한 데이터를 주면 서버는 이를 개인키로 복호화 하여 데이터를 읽고 처리한다.

대칭키에 비해서 안전하지만, 복잡하다. 

## 2 방법을 혼합한 방식

1. A가 B의 공개키를 이용하여 대칭키를 암호화 한 후 B에게 준다.
2. B는 A가 준 데이터를 자신의 개인키로 복호화 후 대칭키를 회득한다.
3. 이후 부턴 A와 B는 대칭키를 통해서 통신한다. 


## HTTPS 방식

설명이 정말 잘되어 있다.

![](https://i.imgur.com/tWzEXTy.png)

HTTPS 등록 과정은 다음과 같다.

1. 우선 기업은 공개키와 개인키를 만든 후 <strong>믿을 수 있는 </strong> 인증기관(CA)에 돈을 주고 자신의 개인키를 관리해줄 것을 의뢰한다.

2. 인증기관(CA)은 기업을 검토 후 기업에 대한정보와, 등록된 공개키, 공개키 암호화 방법등의 정보를 담은 인증서를 만든후 해당 인증서를 인증기관의 <strong>개인키</strong>로 암호화하여 관리한다.

CA는 굉장히 신뢰할 수 있는 기관이기 때문에 CA의 <strong>공개키</strong>는 브라우저에 내장되어져 있고, 이용자는 내장된 공개키를 통해서 이를 복호화 할 수 있다.

--- 

만약 클라이언트가 HTTPS 사이트에 등록하면 흐름은 다음과 같다.

1. 클라이언트가 서버에 요청을 보낸다.
2. 서버는 CA의 개인키로 암호화된 인증서를 클라이언트에게 준다.
3. 클라이언트는 브라우저에 내장된 CA의 공개키를 통해서 인증서를 복호화 한 후, 이 후 서버와 통신할 대칭키를 생성한다.
4. 획득한 서버의 공개키로 대칭키를 암호화 하여 서버에 전송한다.
5. 서버는 개인키로 이를 복호화 하여 대칭키를 획득한다.
6. 이 후 대칭키를 이용하여 암호문을 서로 주고 받게 된다.