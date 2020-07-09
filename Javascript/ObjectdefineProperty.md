# Object.defineProperty

~~~javascript
Object.defineProperty(object, propertyname, discriptor)
~~~
`object` : 속성을 정의할 객체     
`propertyname` : 새로 정의할 속성 키       
`discriptor` : 새로 정의할 속성을 기술하는 객체     
`discriptor`에 기술하는 속성으로는 아래와 같다. 

- configurable        
- enumerable      
- value       
- writable        
- get     
- set     

(자세한 내용 MDN 참고)

configurable, enumerable, value, writable, get, set 키 값을 사용하여 
객체의 속성을 좀 더 정밀하게 수정할 수 있다. 

EX
~~~javascript 
const Ryu = {team : "LA"};

Ryu.team = "TORONTO"
console.log(Ryu.team) //"TORONTO"
~~~
보통 이렇게 player 객체 내부에 team 필드 값을 바꾸면 바꾼 값이 나타나게 된다. Object.defineProperty의 writable 키 값을 조정하면 필드값을 못바꾸게 할 수 있다. 

~~~javascript
const Ryu = {};

Object.defineProperty(Ryu, "team", {
  value : "LA",
  writable : false
});
Ryu.team = "TORONTO";
console.log(Ryu.team); //"LA"
~~~

[Reference]
- https://undefine.me/posts/6       
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty
