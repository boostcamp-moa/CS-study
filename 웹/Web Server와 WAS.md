# Web Server와 WAS

## Static Pages와 Dynamic Pages

<img src="https://gmlwjd9405.github.io/images/web/static-vs-dynamic.png" />

### Static Pages

- Web Server는 파일 경로 이름을 받아 경로와 일치하는 file contents를 반환한다.
- 항상 동일한 페이지를 반환한다.
- Ex) image, html, css, javascript 파일과 같이 컴퓨터에 저장되어 있는 파일들

### Dynamic Pages

- 인자의 내용에 맞게 동적인 contents를 반환한다.
- 즉, 웹 서버에 의해서 실행되는 프로그램(Servlet)을 통해서 만들어진 결과물
  - Servlet : WAS 위에서 돌아가는 Java Program
- 개발자는 Servlet에 doGet()을 구현한다.

<br>

## Web Server와 WAS의 차이

<div>
    <img src="https://gmlwjd9405.github.io/images/web/webserver-vs-was1.png" />
</div>

<br>

### Web Server

- Web Server의 개념

  - `소프트웨어`와 `하드웨어`로 구분된다.
  - 하드웨어
    - Web Server가 설치되어 있는 컴퓨터
  - 소프트웨어
    - 클라이언트(웹 브라우저)로부터 HTTP 요청을 받아 `정적인 컨텐츠(.html, .jpeg, .css 등)`를 제공하는 컴퓨터 프로그램

- Web Server의 기능

  - HTTP 프로토콜을 기반으로 하여 클라이언트(웹 브라우저 또는 웹 크롤러)의 요청을 서비스(반환)하는 기능을 담당한다.
  - 요청에 따라 아래의 두 가지 기능 중 적절하게 선택하여 수행한다.
    - 기능 1
      - 정적인 컨텐츠 제공
      - WAS를 거치지 않고 바로 클라이언트에게 자원을 제공
    - 기능 2
      - 동적인 컨텐츠 제공을 위한 요청 전달
      - 클라이언트(웹 브라우저)의 요청(`request`)을 WAS에 보내고, WAS가 처리한 결과를 클라이언트에게 전달(`response`)한다.

- Web Server의 예
  - `Apache Server`, `Nginx`, `IIS(Windows 전용 Web Server)` 등

<br>

### WAS (Web Application Server)

- WAS의 개념

  - DB 조회나 다양한 로직 처리를 요구하는 `동적인 컨텐츠`를 제공하기 위해 만들어진 Application Server
  - HTTP를 통해 컴퓨터나 장치에 애플리케이션을 수행해주는 미들웨어이다.
  - `웹 컨테이너(Web Container)` 혹은 `서블릿 컨테이너(Servlet Container`라고도 불린다.
    - `Container`란 JSP, Servlet을 실행시킬 수 있는 소프트웨어를 말한다.
    - 즉, WAS는 JSP, Servlet 구동 환경을 제공한다.

- WAS의 역할

  - WAS = `Web Server` + `Web Container`
  - Web Server 기능들을 구조적으로 분리하여 처리하고자 하는 목적으로 제시되었다.
    - 분산 트랜잭션, 보안, 메시징, 쓰레드 처리 등의 기능을 처리하는 분산 환경에서 사용된다.
    - 주로 DB 서버와 같이 수행된다.
  - 현재는 WAS가 가지고 있는 Web Server도 정적인 컨텐츠를 처리하는데 있어서 성능상 큰 차이가 없다.

- WAS의 주요 기능
  - 프로그램 실행 환경과 DB 접속 기능 제공
  - 여러 개의 트랜잭션 관리 기능
  - 업무를 처리하는 비즈니스 로직 수행
- WAS의 예
  - `Tomcat`, `JBoss`, `Jeus`, `Web Sphere` 등

<br>

## Web Server와 WAS를 구분하는 이유

<img src="https://gmlwjd9405.github.io/images/web/webserver-vs-was2.png" />

- Web Server가 필요한 이유

  - 클라이언트(웹 브라우저)에 정적 컨텐츠(이미지 파일)를 보내는 과정을 생각해보자.
    - 이미지 파일과 같은 정적인 파일들은 웹 문서(HTML 문서)가 클라이언트로 보내질 때 함께 가는 것이 아니다.
    - 클라이언트는 HTML 문서를 먼저 받고 그에 맞게 필요한 이미지 파일들을 다시 서버로 요청하면 그때서야 이미지 파일을 받아온다.
    - Web Serer를 통해 정적인 파일들을 Application Server까지 가지 않고 앞단에서 빠르게 보내줄 수 있다.
  - 따라서 Web Server에서는 `정적 컨텐츠`만 처리하도록 기능을 분배하여 서버의 부담을 줄일 수 있다.

- WAS가 필요한 이유

  - 웹 페이지는 `정적 컨텐츠`와 `동적 컨텐츠` 모두 존재한다.
    - 사용자의 요청에 맞게 적절한 `동적 컨텐츠`를 만들어서 제공해야 한다.
    - 이때, Web Server만을 이용한다면 사용자가 원하는 요청에 대한 결과값을 모두 미리 만들어 놓고 서비스(반환)를 해야 한다.
    - 하지만 이렇게 수행하기에는 자원이 절대적으로 부족하다.
  - 따라서 WAS를 통해 요청에 맞는 데이터를 DB에서 가져와서 비즈니스 로직에 맞게 상황에 따라 결과를 만들어서 제공함으로써 자원을 효율적으로 사용할 수 있다.

- WAS가 Web Server의 기능도 모두 수행하지 않는 이유
  - 기능을 분리하여 서버 부하 방지

    - WAS는 DB 조회나 다양한 로직을 처리하느라 바쁘기 때문에 단순한 정적 컨텐츠는 Web Server에서 빠르게 클라이언트에 제공하는 것이 좋다.
    - WAS는 기본적으로 동적 컨텐츠를 제공하기 위해 존재하는 서버이다.
    - 만약 정적 컨텐츠 요청까지 WAS가 처리한다면 정적 데이터 처리로 인해 부하가 커지게 되고, 동적 컨텐츠의 처리가 지연됨에 따라 수행 속도가 느려진다.
    - 즉, 이로 인해 페이지 노출 시간이 늘어나게 될 것이다.

  - 물리적으로 분리하여 보안 강화
    - SSL에 대한 암호화, 복호화 처리에 Web Server를 사용
  - 여러 대의 WAS를 연결 가능

    - Load Balancing(트래픽 분산)을 위해서 Web Server를 사용
    - fail over(장애 극복), fail back 처리에 유리
    - 특히 대용량 웹 어플리케이션(여러 개의 서버 사용)의 경우, Web Server와 WAS를 분리하여 무중단 운영을 위한 장애 극복에 쉽게 대응할 수 있다.
    - 예를 들어, 앞 단의 Web Server에서 오류가 발생한 WAS를 이용하지 못하도록 한 후 WAS를 재시작함으로써 사용자는 오류를 느끼지 못하고 이용할 수 있다.

  - 여러 웹 어플리케이션 서비스 가능
    - 하나의 서버에서 PHP Application과 Java Application을 함께 사용하는 경우
  - 기타
    - 접근 허용 IP 관리, 2대 이상의 서버에서의 세션 관리 등도 Web Server에서 처리하면 효율적이다.

> 즉, 자원 이용의 효율성 및 장애 극복, 배포 및 유지보수의 편의성을 위해 Web Server와 WAS를 분리한다.

> Web Server를 WAS 앞에 두고 필요한 WAS들을 Web Server에 플러그인 형태로 설정하면 더욱 효율적인 분산 처리가 가능하다.

<br>

## Web Service Architecture

- 다음과 같이 다양한 구조를 가질 수 있다.
  - `Client` -> `Web Server` -> `DB`
  - `Client` -> `WAS` -> `DB`
  - `Client` -> `Web Server` -> `WAS` -> `DB`

<br>

<div>
    <img src="https://gmlwjd9405.github.io/images/web/web-service-architecture.png" />
</div>

<br>

- `Client` -> `Web Server` -> `WAS` -> `DB` 구조의 동작 과정

  1. 사용자의 요청
     1.1 사용자가 웹서버에 요청을 전달
     1.2 만약 Servlet에 대한 요청인 경우 WAS에 요청을 전달

  2. Web Container에서는 사용자의 요청에 대한 `HttpServletRequest` 객체와 `HttpServletResponse` 객체를 생성

  3. 사용자 요청처리
     3.1 Web Container에서 `web.xml`(배포서술자)를 참조하여 사용자가 요청한 URL이 어떤 Servlet에 mapping되는지를 찾음
     3.2 사용자가 요청한 Servlet이 이전에 요청된적이 없다면 객체를 생성하고, 해당 Servlet의 `init()` 메소드를 호출하고, 메모리에 로드 시킨다. 만약 이전에 생성한 적이 있다면 객체를 다시 생성하지 않는다.
     3.3 웹 컨테이너에서 사용자의 요청을 처리할 Thread를 생성한다.
     3.4 Thread가 생성되었으면 해당 Thread를 통해 Servlet의 `service()`메소드를 실행한다.
     3.5 `service()` 메소드에서는 사용자의 요청 메소드(get, post)에 따라 `doGet()`, 또는 `doPost()` 메소드가 호출된다. 이때 앞서 생성한 `HttpServletRequest`, `HttpServletResponse` 객체를 톰캣에 의해 인수로 전달 받는다.

  4. 응답
     4.1 `doGet()`, 또는 `doPost()`가 호출되어 사용자 요청에 따른 페이지를 생성한 뒤 그것이 담긴 `response` 객체를 Web Container에서 HTTP Response로 바꾸어 Web Server에 전송한다.

  5. 소멸
     5.1 `request`, `response` 객체를 소멸시킨다.
     5.2 Thread를 종료한다.

<br>
<br>

## reference

- https://gmlwjd9405.github.io/2018/10/27/webserver-vs-was.html Web Server와 WAS의 차이와 웹 서비스 구조
- https://galid1.tistory.com/487 Servlet 개념
