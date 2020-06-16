# 구조분해할당 

배열 혹은 객체의 값들을 분해하여 개별 변수를 할당하는 방식이다. 

~~~javascript
let sports = ['baseball', 'basketball', 'soccer'];

let [,,축구] = sports;

console.log(축구) // output : soccer
console.log(sports)
~~~

sports라는 배열에 세번째 값이 `soccer`값만 필요할 경우 위와 같은 예시로 기존 `sports`배열을 분해하여 개별변수를 
선언할 수 있다. 
여기서 기존 `sports` 배열은 사라지거나 파괴되지 않는다. 기존 배열의 값은 유지하면서 배열의 값만 복사하듯이 새로운 객체 혹은 배열을 만들어준다. 

~~~javascript
const soccer = {
    player : "손흥민",
    position : "공격수",
    team : "토트넘"
}

let {position} = soccer;

console.log(position) // 공격수 
~~~

배열은 인덱스 순서에 따른 구조분해 방식이라면 객체 구조분해는 Key값 기준으로 구조분해 할당이 이루어진걸 볼 수 있다. 

~~~javascript
const sports = [
  {
    "title": "soccer",
    "name" : "손흥민",
    "position" : "공격수",
    "information" : [
        "윙포워드도 가능 ",
        "쉐도우 스트라이크 가능"
    ]
  },
  {
    "title": "baseball",
    "name" : "류현진",
    "position" : "투수",
    "information" : [
        "씽커",
        "포크볼",
        "포심"
    ]
  }  
];

let [,baseball] = sports;
console.log(baseball) //{title: "baseball", name: "류현진", position: "투수", information: Array(3)}

let {title, information} = baseball;
console.log(title, information); //baseball (3) ["씽커", "포크볼", "포심"]
~~~


