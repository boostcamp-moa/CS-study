# Blocking I/O, Non-Blocking I/O, 동기, 비동기

> I/O = Input/Output
> 네트워크에서는 socket의 read/send

> `Blocking I/O`, `Non-Blocking I/O`, `동기`, `비동기` 모두 공통적으로 I/O 작업을 User-level에서 직접 수행할 수 없고 kernel-level에서만 수행할 수 있다. 따라서, I/O를 수행하기 위해서는 결국 커널에 한 번 이상 시스템 콜을 보내야 한다.
> 시스템 콜을 보내면 커널로 제어권이 넘어가고(context-switch), 유저 프로세스 혹은 스레드는 제어권이 다시 돌아오기 전에는 `block` 된다. (유저 프로세스는 다른 작업을 하지 못한다.)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcSJgrA%2FbtqGgXciWxU%2Flwu4HGKK9nOoAUEKQZks4k%2Fimg.png)

<br>

## Blocking / Non-Blocking

> 호출되는 함수가 바로 리턴하는지의 여부

- `Blocking` : 호출된 함수가 자신의 작업을 모두 마칠 때까지 호출한 함수에게 제어권을 넘겨주지 않고 대기하게 만드는 방식
- `Non-Blocking` : 호출된 함수가 바로 리턴해서 호출한 함수에게 제어권을 넘겨주고, 호출한 함수가 다른 일을 할 수 있는 기회를 주는 방식

<br>

## synchronous / asynchronous

> 호출되는 함수의 작업 완료 여부를 신경쓰는지의 여부

- `synchronous` : 호출하는 함수가 호출되는 함수의 작업 완료 후 리턴을 기다리거나 호출되는 함수로부터 바로 리턴 받더라도 작업 완료 여부를 호출하는 함수 스스로 계속 확인하며 신경쓰는 방식
- `asynchronous` : 호출되는 함수에게 callback을 전달해서 호출되는 함수의 작업이 완료되면 호출되는 함수가 전달 받은 callback을 실행하고, 호출하는 함수는 작업 완료 여부를 신경쓰지 않는 방식

<br>

## sync Blocking I/O

![](https://limdongjin.github.io/images/sync-blocking.png)

- 시스템 콜이 들어오면, 커널은 `I/O` 작업이 완료되기전에는 응답을 하지않는다.
- 즉 `I/O` 작업이 완료되기 전에는 제어권을 커널이 갖고 있다.
- 그렇기 때문에 시스템 콜을 보낸 후에, 유저 프로세스는 응답을 받기 전에는 `block`이 되어 다른 작업을 하지 못한다. 즉 `I/O` 작업이 완료되기 전에는 다른 작업을 수행하지 못한다.

<br>

## sync Non-Blocking I/O

![](https://limdongjin.github.io/images/sync-non-blocking.png)

- 시스템 콜이 들어오면, 커널은 `I/O` 작업의 완료 여부와는 무관하게 즉시 응답을 해준다. (완료 되지않았다면 에러코드를 응답)
- 이는 커널이 시스템 콜을 받자마자 제어권을 다시 유저 프로세스에게 넘겨 준다는 것이기에, 유저 프로세스는 `I/O` 가 완료 되기 전에도 다른 작업을 할 수 있는 것이다.
- 유저 프로세스는 다른 작업들을 수행하다가 중간 중간에 시스템 콜을 보내서 `I/O`가 완료되었는지 커널에게 물어보게 된다.

<br>

# async Blocking I/O

![](https://limdongjin.github.io/images/async-blcoking.png)

- 이 모델에서 `I/O`는 `non-blocking`이지만, 통지(notification)는 `blocking` 방식으로 하도록 되어있다.
- `select()` 는 유저 프로세스를 `block` 한다.
- `select()` 는 데이터가 사용이 가능해지면, 통지를 받게 된다. 통지를 받으면 `block`을 풀게 된다.

<br>

## async Non-Blocking I/O

![](https://limdongjin.github.io/images/async-non-blocking.png)

- 시스템 콜이 들어오면, 커널은 `I/O` 작업의 완료 여부와는 무관하게 즉시 응답을 해준다.
- 유저 프로세스는 `I/O` 가 완료 되기 전에도 다른 작업을 할수있는 것이다.
- `I/O` 처리는 백그라운드에서 실행되다가, 완료되면 커널이 유저 프로세스에게 알려준다.
  - 이는 `sync Non-Blocking I/O`와의 차별점이라고 볼 수 있다.
  - `sync Non-Blocking I/O`는 유저 프로세스가 `I/O` 완료 여부를 커널에게 계속 물어봐야 했다.
  - `async Non-Blocking I/O`는 `I/O`가 완료되면 그때 커널이 유저프로세스에게 알려주는 방식이다.

<br>
<br>
  
### reference
- https://bk-investing.tistory.com/38   Blocking I/O, Syncronous Non-Blocking I/O, Asyncronous Non-Blocking I/O
- https://limdongjin.github.io/concepts/blocking-non-blocking-io.html#ibm-%EC%95%84%ED%8B%B0%ED%81%B4    blocking, non-blocking IO, 동기, 비동기 개념 정리
