![](https://i.imgur.com/Rw3OefB.png)
> 마스터 크롱님이 만드신

# FE 개발자에게 하고싶은 질문

1. 브라우저의 렌더링 동작과정을 짧게 설명해보세요

https://github.com/boostcamp-moa/CS-study/blob/main/%EC%9B%B9/%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%20%ED%81%AC%EB%A6%AC%ED%8B%B0%EC%BB%AC%20%EB%A0%8C%EB%8D%94%EB%A7%81%20%ED%8C%A8%EC%8A%A4.md

2. Object.create의 역할은 무엇인가요?
```js=
Object.create(proto[,propertiesObject ])
```

객체를 인자로 받아 인자 객체와 prototype으로 연결된 새로운 객체를 만들어 내는 함수

```js=
const A={
  method:()=>{
    console.log('A');
  },
  value:3,
}

const B = Object.create(A);

B.method(); // A print
console.log(B.value) // 3 print
console.log(B===A) // false;
console.log(B.__proto__ === A); // true
```

이렇게 Object.create() 메소드는 자동으로 prototype과 연결된 객체를 만들어 주기 때문에
주로 상속을 구현할때 많이 사용된다.

```js=
// prototype,constructor은
// https://velog.io/@wmc1415/Javascript-Prototype%EC%9D%B4%EB%9E%80 이곳 참고
function dog(gender='male',type='리트리버'){
  this.gender=gender;
  this.type=type;
}

dog.prototype.bark = function(){
  console.log('멍멍');
}

function myDog(like='meat'){
  dog.call(this,'male','똥개');
  this.like=like;
}

myDog.prototype = Object.create(dog.prototype);

myDog.prototype.play=function(){
  console.log('왈왈');
}

const dogs = new myDog('과자');
dogs.bark(); //멍멍
dogs.play(); //왈왈
console.log(dogs.type); // '똥개';
console.log(dogs.like); // '과자';
```

3. 자바스크립트에서 모듈내의 private한 속성을 만드는 방법을 아는대로 쓰세요.
- 변수나 메소드 앞에 _ 을 붙인다. (관습적인)
```js=
class Private
  constructor(number=0){
    this._number=number;
  }
}
```
- 클로저를 이용해서 접근을 막는 방법
```js=
const Private = (function(){
  let number;
  return {
    getNumber(){
      return number;
    },
    setNumber(num){
      number = num;
    }
  }
})();
```


4. JS에서 재귀호출로 인한 stack overflow를 막을 수 있는방법은?

```js=
// A code
let run=10000000000000;
function overFlow(){
  if(run!==0){
    run--;
    overFlow();
  }
}
```
to
```js=
// B code
let run = 10000000000000;
function overFlow(){
  if(run!==0){
    run--;
    setTimeout(()=>{
      overFlow();
    })
  }
}
```

- 아이디어

![](https://i.imgur.com/QlBMGXt.png)

A 코드와 같이 JS의 일반 함수는 호출을 하면 콜스택에 쌓이게 되어 결국 overflow가 발생하게 된다.

하지만 Ajax,Timer와 같이 WebAPI에 정의된 함수들은 실행시 곧바로 콜스택에 쌓이는것이 아닌 태스크 큐에 먼저 쌓이고 이후 이벤트 루프에 의해서 실행되게 된다.

위의 B 코드는 그것에 아이디어를 착안한 코드, setTimeout을 이용해서 overFlow 함수를 호출하면, 이전에 호출한 overFlow 함수는 콜스택에서 사라지고 다음 호출할 overFlow 함수는 태스크 큐에 머문 후 이벤트 루프에 의해서 실행되게 된다.

하지만 overflow가 발생하지 않을 뿐 실행 속도가 매우 낮아진다.

5. closure 와 스코프관계를 설명해보세요.
6. 본인이 경험한 OOP관점에서의 객체분리를 설명하고, 느낀 장점을 말해보세요.
7. == 보다, === 를 써야할때는?
8. DFS, BFS를 통한 트리탐색방법 중 본인이 경험(사용)했던 방식은 무엇이고, 동작원리를 짧게 설명해보세요.
9. ES6의 Class extends 내부 동작원리에 대해서 설명해보세요.
10. 객체를 탐색하는 방법에 대해서 2가지를 작성해보세요.
11. NodeList 타입을, Array에 있는 reduce메서드를 사용하는 방법은?
12. arrow 함수의 this가 결정되는 방식을 설명해보세요.
13. immutable과 mutable은 무엇이 다른것인가요?
14. undefined와 null의 차이점을 설명하세요.
15. 아래처럼 동작하는 flatten함수를 reduce를 활용해서 만들어보세요.

```js
const arr = [
  [1, 2],
  [3, 4],
  [5, 6],
];
const flattenedArray = flatten(arr);
console.log(flattenedArray); //[1, 2, 3, 4, 5, 6];
```

16. 객체를 복사해서 새로운 객체를 만들고 싶습니다. 코드를 구현해보세요. (객체의 깊이는 1단계만 있다고 가정)
17. Array.from 이 모든 브라우저에서 동작하도록 polyfill코드를 만들어보세요.
18. 프로그래밍 요구사항을 받았을때, 구현하기 전까지 어떤 과정을 거치시나요?
19. prototype 의 동작방식에 대해서 설명해보세요.
20. 순환되는 캐로셀UI의 구현 원리에 대해서 설명해보세요.
21. Event 객체에 대해서 설명해보세요.
22. 웹사이트의 초기 로딩속도를 더 빠르게 하기 위해서 무엇을 해야 할까요?
23. 최근 가장 깊게 공부하고 있는 부분은 무엇인가요? 그 부분에 대해서 간단하게 설명해보세요.
24. Array.from 이 모든 브라우저에서 동작하도록 polyfill코드를 만들어보세요.
25. 브라우저의 렌더링 동작과정을 짧게 설명해보세요.
26. arrow 함수의 this가 결정되는 방식을 설명해보세요.
27. 비동기의 장점을 설명해보세요.
28. 본인이 즐겨하는 디버깅 방법을 설명해보세요.
29. bind 가 필요한 상황을 간단한 코드로 보여주세요.
30. CommonJS 스펙에 대해 설명해보세요.
31. node의 middleware의 동작방식을 설명해보세요.
32. 본인이 생각하는 좋은 객체지향프로그래밍에 대해서 설명해보세요.
33. 클로저로 동작되는 상황을 예시코드로 보여주세요.
34. React의 virtual DOM 은 뭐에요?
35. React의 렌더링 방식은 무엇인가요?
36. React의 초기화면 느린 부분은 어떻게 해결해야해요?
37. SSR은 무엇인가요? 어떻게 구현하죠?