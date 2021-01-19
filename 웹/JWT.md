# JWT (JSON Web Token)

### 개념

> JSON Web Tokens are an open, industry standard [RFC 7519](https://tools.ietf.org/html/rfc7519) method for representing claims securely between two parties.

[jwt.io](jwt.io)에 나와 있는 개념이다. 즉, JSON Web Token (JWT)은 웹표준([RFC 7519](https://tools.ietf.org/html/rfc7519))으로서 두 개체 사이에서 JSON 객체를 사용하여 가볍고 자가수용적인(self-contained) 방식으로 정보를 안전성 있게 전달해준다.

<br>

### 구성 요소

JWT는 `.`을 구분자로 세 가지의 문자열로 구성되어 있다.

> `xxxxx.yyyyy.zzzzz`

- 첫 번째 fragment `xxxxx`: 헤더 (Header)
- 두 번째 fragment `yyyyy`: 정보 (Payload)
- 세 번째 fragment `zzzzz`: 서명 (Signature)

각 구성 요소를 살펴보기 이전에, 각 구성 요소에 포함될 JSON 내용을 인코딩하는 방법인 Base64URL을 알고 싶다면 [여기](https://github.com/boostcamp-moa/CS-study/blob/main/%EC%9B%B9/Base64%2C%20Base64URL.md)를 참고하자.

<br>

#### 1. 헤더 (Header)

헤더는 일반적으로 토큰 유형인 `typ`과 사용중인 서명 알고리즘 `alg`으로 구성된다.

- 토큰 유형 `typ`
  - 토큰의 타입을 의미
  - JWT를 만드는 것이기 때문에 "JWT"로 고정
- 서명 알고리즘 `alg`
  - 서명할 때 사용하는 해싱 알고리즘을 의미
  - 일반적으로 `HMAC SHA256`, `RSA`가 사용되며, npm의 `jsonwebtoken` 모듈은default값으로 HS256(HMAC SHA256)을 사용
  - 더 자세한 알고리즘을 확인하고 싶다면 [RFC7518](https://tools.ietf.org/html/rfc7518#section-3)을 확인

> 예시

```json
{
	"alg": "HS256",
	"typ": "JWT"
}
```

이후, JSON은 Base64Url 형태로 인코딩되어 JWT의 첫 번째 부분인 헤더가 된다.

<br>

#### 2. 정보 (Payload)

이 부분에는 토큰에 담을 정보가 들어있다. 여기에 담는 정보의 한 조각을 **클레임(claim)** 이라고 부르며, 이는 **name-value** 형태로 구성되어 있다. 토큰에는 여러 개의 클레임을 넣을 수 있다.

클레임은 크게 세 종류로 구분된다.

- 등록된 클레임 (registered claim)
- 공개 클레임 (public claim)
- 비공개 클레임 (private claim)

<br>

##### (1) 등록된 클레임 (registered claim)

등록된 클레임은 서비스에서 필요한 정보가 아닌, 토큰에 대한 정보를 담기 위하여 이름이 이미 정해진 클레임이다. 등록된 클레임의 사용은 선택적(optional)이며, 이에 포함된 클레임 이름들은 다음과 같다. (자세한 내용은 [여기](https://tools.ietf.org/html/rfc7519#section-4.1)를 참고)

- `iss`: 토큰 발급자 (issuer)
- `sub`: 토큰 제목 (subject)
- `aud`: 토큰 대상자 (audience)
- `exp`: 토큰의 만료시간 (expiraton)
  - NumericDate 형식으로 되어있음
    ex) 1480849147370
  - 언제나 현재 시간보다 이후로 설정되어있어야 함
- `nbf`: "Not Before"의 약자로, 토큰이 처리되지 않아야 하는 시간
  - 토큰의 활성 날짜와 비슷한 개념으로, 이 날짜가 지나기 전까지는 토큰이 처리되지 않음
  - NumericDate 형식으로 되어있음
- `iat`: 토큰이 발급된 시간 (issued at)
  - 이 값을 사용하여 토큰의 `age` 가 얼마나 되었는지 판단할 수 있음
  - NumericDate 형식으로 되어있음
- `jti`: JWT의 고유 식별자
  - 주로 중복적인 처리를 방지하기 위하여 사용
  - 일회용 토큰에 사용하면 유용

<br>

##### (2) 공개 클레임 (public claim)

공개 클레임의 이름은 JWT를 사용하는 사용자가 마음대로 정의할 수 있지만, 충돌이 방지된(collision-resistant) 이름을 가지고 있어야 한다.

충돌을 방지하기 위한 방법으로는 아래 두 가지가 있다.

- [IANA JSON 웹 토큰 레지스트리](https://www.iana.org/assignments/jwt/jwt.xhtml)에서 정의

- 네임 스페이스를 포함하는 URI로 정의

```json
{
    "https://moa-re.kr/jwt_claims/is_admin": true
}
```

<br>

##### (3) 비공개 클레임 (private claim)

등록된 클레임도 아니고, 공개된 클레임도 아닌 클레임으로, 두 개체 간 협의하에 사용되는 이름이 key로 들어가는 클레임이다. 공개 클레임과는 달리 이름이 중복되어 충돌이 될 수 있으니 사용할 때에 유의해야 한다.

```json
{
    "userId": "1361373",
    "username": "Hyeonu"
}
```

<br>

##### 정보(Payload) 예시

```json
{
    "iss": "moa-re.kr",
    "exp": "1485270000000",
    "https://moa-re.kr/jwt_claims/is_admin": true,
    "userId": "1361373",
    "username": "Hyeonu"
}
```

위 예시는 2개의 등록된 클레임, 1개의 공개 클레임, 2개의 비공개 클레임으로 구성된 데이터이다. 해당 데이터는 Base64Url 형태로 인코딩되어 JWT의 두 번째 부분인 정보(Payload)가 된다.

<br>

#### 3. 서명 (Signature)

서명 부분을 만들려면 인코딩된 헤더, 인코딩된 정보, 비밀키인 Secret, 헤더에 지정된 알고리즘을 가져 와서 서명해야 한다. 자세한 순서는 다음과 같다.

1. 인코딩된 헤더값과 인코딩된 정보를 합치는데, 두 내용 사이에 `.`를 붙여서 합침
2. 비밀키인 Secret을 활용하여 1번에서 합친 내용을 해싱
3. 이후, 해쉬값은 Base64URL 형태로 인코딩되어 JWT의 세 번째 부분인 서명이 됨

> 예시 - HMAC SHA256 알고리즘을 사용하려는 경우의 서명

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

<br>

### JWT를 언제 사용할까?

- 회원 인증

  유저가 로그인을 하면, 서버는 유저의 정보에 기반한 JWT를 발급하여 유저에게 전달한다. 그 후, 유저가 서버에 API 요청을 할 때마다 Bearer 스키마를 사용하는 Authorization 헤더에 JWT를 포함하여 요청을 전달한다. 서버가 클라이언트로부터 요청을 받을 때마다, 요청 헤더에 담겨있는 JWT가 유효하고 인증됐는지 검증을 하고, 유저가 요청한 작업에 권한이 있는지 확인하여 이후 서비스 로직을 수행한다.

  JWT를 사용하면 서버측에서는 유저의 세션을 유지 할 필요가 없다. 즉, 유저가 로그인되어있는지 안되어있는지 신경 쓸 필요가 없어진다. 그저 유저가 요청을 했을 때 JWT의 유효성만을 검증하면 되니, 세션 관리가 필요 없어지고 그 덕분에 서버 자원을 많이 아낄 수 있게 된다.

- 정보 교류

  JWT를 활용하는 것은 두 개체 사이에서 안정성있게 정보를 교환하기에 좋은 방법이다. 정보가 서명되어 있기 때문에 정보를 보낸이가 바뀌진 않았는지, 정보가 도중에 조작되지는 않았는지 검증할 수 있기 때문이다.

<br>

### 장점

- 세션이 필요없어지므로 서버 자원을 아낄 수 있음
- 만료 시간이 내장되어 있음
- SAML(Security Assertion Markup Language Tokens)과 비교했을 때, 인코딩된 토큰의 크기가 작음
- SWT(Simple Web Tokens)와 비교했을 때, 대칭키가 아닌 공개/개인키 쌍을 사용하여 서명을 하기 때문에 보안 측면에서 더 우수
- 무상태(Stateless)라는 장점 덕분에 RESTful API를 사용하는 서비스에 적합

<br>

### 단점

- 발급된 JWT는 클라이언트에 저장되어 있으므로 서버 내 DB에서 사용자 정보가 수정되더라도 클라이언트에 있는 JWT에 수정된 내용을 반영할 수 없음
- 클레임이 추가되거나 클레임의 value가 길어질 때마다 JWT의 길이가 길어지는데, API 호출 시 매 호출마다 JWT가 헤더에 포함되어야 하므로 네트워크 대역폭 낭비가 심해질 수 있음
-  클레임에 대한 암호화는 이뤄지지 않고 base64URL 인코딩만 이루어지기 때문에, 중간에 패킷을 탈취당하거나 브라우저 상에서 JWT가 탈취당할 경우 JWT 내부에 담겨있는 클레임을 통해 사용자 정보가 누출될 수 있음

---

*레퍼런스*

1. [JWT 공식 홈페이지 - 설명](https://jwt.io/introduction)

2. [RFC7519 - JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519)

3. [RFC7518 - JSON Web Algorithms](https://tools.ietf.org/html/rfc7518)

4. [veloport 블로그, "[JWT] JSON Web Token 소개 및 구조"](https://velopert.com/2389)

5. [JSON Web Token (JWT) Signing Algorithms Overview](https://auth0.com/blog/json-web-token-signing-algorithms-overview/#Common-JWT-Signing-Algorithms)

6. [TOAST 블로그, "JWT를 소개합니다."](https://meetup.toast.com/posts/239)

7. [JWT (JSON Web Token) 이해와 활용](http://www.opennaru.com/opennaru-blog/jwt-json-web-token/)
