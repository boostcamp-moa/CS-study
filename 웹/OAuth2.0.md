## OAuth 란?
`OAuth`는 다양한 플랫폼 환경에서 인증과 권한 부여를 제공하기 위한 개방형 표준 프로토콜 이다. 

OAuth를 사용하면 사용자 비밀번호를 공유하지 않아도 서드파티 어플리케이션에게 사용자의 제한된 자원에 접근할 수 있도록 허용할 수 있다.

서드 파티 어플리케이션에서 네이버 또는 구글 로그인을 통해 해당 서비스의 자원 (네이버 API 사용권한, 네이버 이메일 등) 에 대한 사용 권한을 얻는 것이 그 예시다. 

### 구성

- Resource Server: 자원을 제공하는 서버 (네이버의 리소스 저장 서버)

- Authorization Server: Resource Server 와 같은 서버이지만 인증과 관련된 부분을 전담하는 서버 (네이버의 인증 담당 서버)

- Resource Owner: 사용자 (자원의 소유자)

- Client: Resource Server 에서 제공하는 자원을 사용하는 애플리케이션 (우리가 만든 서비스)

<br>

### 추상적인 흐름

<img src="https://images.velog.io/images/mu1616/post/e8312e03-f2b9-4746-8eac-f877c682a14c/image.png" width="600" />

1. Client는 보호된 자원으로 접근을 위해 Resource Owner에게 인가 승인을 요청한다.

2. Resource Owner가 인가 요청을 승인하면 Client는 승인 허가를 받는다.

3. Client는 Authorization Server로 Access Token 발급을 요청한다. 이 때 인증을 위한 ID와 제공받을 권한을 함께 전달한다.

4. 인증 정보와 제공할 권한 정보가 유효하면 Authorization Server는 Client 에게 Acceess Token을 전달한다.

5. Client는 Resource Server에게 인증을 위한 Access token을 전달 하면서 필요한 자원을 요청한다.

6. Resource Server는 Access token이 유효하면 해당 자원을 제공한다.

## Grant type

Oauth2.0은 다양한 클라이언트 환경에 적합한 인증 및 권한의 위임 방식을 제공한다.

- Authorization Code

- Implicit

- Resource Owner Password Credentials

- Client Credentials

### Authorization Code

클라이언트가 백엔드를 제공하는 웹 애플리케이션이면 이 방식을 채택한다. 

0. `Client`는 `Resource Server`의 API를 사용한다고 `Resource Server`에 등록하고 `Client ID`와 `Client Secret`을 발급받는다.

<img src="https://images.velog.io/images/mu1616/post/2d906d0f-6a6d-4954-9c6b-7d82b7689cbd/image.png" width="600" />

- Client Id: 우리가 만든 서비스의 식별자 (노출되도 됨)
- Client Secret: Client Id에 대한 비밀번호 (노출되면 안됨)

1. `Client`에서 `Resource Owner` 에게 특정 서비스 이용을 하기위해서는 인증이 필요하다고 말함

![](https://images.velog.io/images/mu1616/post/ee3fefb4-d960-40ec-b455-7e124d4816ed/image.png)

ex)  
`${OAuth_URL}?response_type=code&client_id=${CLIENT_ID}&redirect_uri=${CALLBACK_URL}&scope=read`

- redirect_uri: 인증 승인 이후 User-Agent가 리다이렉트 될 URL

- response_type: 사용을 원하는 grant type을 지정
  - code: Authorizaction Code
  - token: Implicit
  
- scope: 접근 권한 (어디까지 접근을 허용할 것인지)

2. `ReSource Owner`는 `Client` 에게 `Resource Server`에 있는 Owner의 Resource 접근에 대한 권한 부여를 수락하고 그에 대한 증명으로 Authorizaion code 를 redirect URL을 통해 전달

![](https://images.velog.io/images/mu1616/post/ef7a48b2-dbdd-4ca7-8572-c79f511a974f/image.png)

3. `Client`는 2번 과정에서 획득한 `Authorizacion code`를 통해 `Authorization Server`에 `Access token`을 발급을 요청한다.

 ![](https://images.velog.io/images/mu1616/post/71de13b6-4e30-48c1-9a1c-67e59b3f22b1/image.png)
 
4. `Authorization Server`는 자기가 가지고 있는 Client ID, Client Secret, Authorization code 를 전달받은 정보와 비교하여 값이 동일할 때에만 Access token을 전달한다.

5. `Client`는 `Resource Server`에게 인증을 위한 Access token을 전달 하면서 필요한 자원을 요청한다.

6. `Resource Server`는 Access token이 유효하면 해당 자원을 제공한다.
 
### Implicit

- Authorization Code와 같이 인증 과정에서 사용자의 로그인 및 권한 동의를 요구하는 타입이다.

- 웹 서버가 아닌 브라우저 기반의 JS 애플리케이션 (SPA)에 가장 적합하도록 설계되었다. 

- authorization_code와의 차이점이라면 code 아닌 token을 반환한다는 것이다. 사용자가 로그인 및 권한 위임에 동의하면 redirect_uri에 token 파라미터를 통해 access_token을 바로 내려준다. 
따라서 Access token이 외부에 노출되기 때문에 주의해야 한다.
ex) {Redirect_URL}?token={ACCESS_TOKEN}

- 유효한 클라이언트인지 확인할 방법은 Redirect URL 뿐이다. 절대 사외부에 노출되지 않아야 할 client_secret은 등록 및 인증 과정에서 쓰이지 않는다.

- refresh_token 또한 노출되지 않아야 하기 때문에 발급되지 않는다. 따라서 access_token이 만료되면 같은 인증 과정을 반복해야 한다. 

흐름은 다음과 같다.

1. `Client`가 인증 서버에게 사용자 로그인 및 권한 동의 웹 페이지를 요청한다.

2. 권한 동의 이후 Redirect URL로 Authorizacion Code가 아니라 Access token을 전달한다. 이 시점에서 `Client`는 Access token을 획득하게 된다.

3. 획득한 Access token으로 `Resource Server`에 API 요청을 보낸다.

### Resource Owner Password Credentials

`Client`는 `Resource Owner`의 아이디, 패스워드를 이용해서 직접 Access token을 발급받는다.

<img src="https://images.velog.io/images/mu1616/post/7dae4fd9-42a2-4b59-83b3-13e4bda540f0/image.png" width="600" />

1. `Resource Owner`는 인증정보 (아이디, 패스워드)를 `Client`에게 직접 전달한다.

2. `Client`는 앞서 받은 인증 정보를 `Authorization Server`로 전송하여 Access token을 발급받는다.

3. 획득한 Access token으로 `Resource Server`에 API 요청을 보낸다.

이 방식은 `Resource Owner`의 아이디, 패스워드가 `Client`에게 그대로 노출되므로 `Client`와 `Oauth Provider`가 같은 도메인/솔루션 내에 존재하여 서로 신뢰할 수 있는 경우 이 Grant Type을 사용한다.

### Client Credentials

`Client`는 오직 Client Credential 정보 (Client ID, Client Secret) 만을 사용하여 Access token을 요청한다.

이 타입은 `Resource Owner`에게 권한을 위임받아 제한된 리소스에 접근하는 것이 아니라 자신이 애플리케이션을 사용할 목적으로 사용하는 것이 일반적이다.



<br>

레퍼런스

https://hyunto.github.io/2019/01/15/introduction-to-oauth2.0/#%EA%B7%B8%EB%9F%AC%EB%A9%B4-%EB%82%98%EB%8A%94-%EC%96%B4%EB%96%A4-Grant-%ED%83%80%EC%9E%85%EC%9D%84-%EC%84%A0%ED%83%9D%ED%95%B4%EC%95%BC-%ED%95%A0%EA%B9%8C

https://jsonobject.tistory.com/369

https://cheese10yun.github.io/spring-oauth2-provider/#null
