레퍼런스

https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html

https://meetup.toast.com/posts/92

https://www.youtube.com/watch?v=iOueE9AXDQQ&ab_channel=%EC%96%84%ED%8C%8D%ED%95%9C%EC%BD%94%EB%94%A9%EC%82%AC%EC%A0%84

https://stackoverflow.com/questions/34130036/how-to-understand-restful-api-is-stateless

https://stackoverflow.com/questions/319530/restful-authentication

# REST API
> Representational State Transfer API

REST 아키텍처 제약 조건을 만족하는 API를 의미한다.

## REST API 구성 요소

|요소|HTTP|
|------|---|
|자원(RESOURCE)|URI|
|행위(VERB)|HTTP METHOD|
|표현(REPRESENTATIONS)|JSON,XML|

- 자원
    - 서버의 자원을 주고 받을 수 있어야 한다.
    - 서버의 자원은 URI을 통해서 접근한다.
- 행위
    - 자원에 대해서 리퀘스트를 요청할 수 있다.
    - 리퀘스트는 HTTP METHOD을 통해서 표현할 수 있다.
- 표현
    - 리퀘스트에 대한 응답으로 여러 자원을 줄 수 있다.
    - 대표적으로 JSON,XML이 쓰인다. (이미지,비디오 여러가지 많다!)

## REST API 특징

### Uniform (유니폼 인터페이스)
- URI을 통해서 자원을 조작하는 스타일을 의미한다.

### Stateless (무 상태성)
- 무 상태성을 가지고 있어야 한다. Client에 대한 상태를 Server가 저장하고 있으면 안된다. (쿠키,세션 포함)
    - 인증이 필요하다면 토큰을 사용하거나 open API 처럼 API Key을 발급받아 사용해야한다.
- 단순히 Client의 요청을 처리한다.
    - 상태에 대한 의존도가 없어지기 때문에 구현이 단순해진다.

### Cacheable (캐시 가능)
- HTTP 프로토콜을 그대로 사용하기 때문에 HTTP의 캐싱 기능도 적용이 가능하다.

### Client-Server 구조
- 자원은 Server 쪽에 존재하고 Client는 URI을 통해 자원에 접근한다.
- Client 상태는 모두 Client에게 책임이 있다.

### Layered System (계층 구조)
- Proxy, GateWay, Load Balance와 같은 중간 매체를 두는것이 가능하다.

## REST API 설계 규칙

- resource에는 동사보다는 명사를 사용해야하며 소문자를 사용해야한다.
    -  /user/delete/1 (X)
    -  /FOOD/DELETE/1 (X)
- 올바른 HTTP Method을 사용해야한다.
    - [GET] /user/fetch/1 (X)
    - [FETCH] /food/1 (O)
- _보다는 -을 사용한다.
- / 을 이용하여 계층 구조를 나타낸다.
    - /animals/dog

- URI 마지막에 /을 포함하지 않는다.
    - /animals/dog/1/ (X)
    - /animals/dig/1 (O)

- 파일확장자를 URI에 포함하지 않는다.
    - /animals/dog/1/mydog.jpg (X)

- Collection은 복수로 Document는 단수로한다.
    - Document는 단순한 문서이고 Collection은 Document들의 집합이다.
        - /animals/dog에서 dog가 Document이고 동물들의 집합인 animals가 Collection이다.

- 리소스간 관계가 있을 경우 <strong>리소스명/리소스ID/다른 관계 리소스</strong>로 표현한다.
    - /users/{userid}/devices
