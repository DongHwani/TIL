# RestAPI(1) 

RestAPI의 Search검색에 대한 설계 가이드를 정리한 글이다.

### 유명 사이트 API 분석

1. Github   
~~~bash
https://github.com/search?p=2&q=dhkdhk&type=Code
~~~

2. Facebook
~~~bash
https://www.facebook.com/search/top/?q=dhkdhk

https://www.facebook.com/search/people?q=dhkdhk
~~~

3. Youtube
~~~bash
https://www.youtube.com/results?search_query=dhkdhk
~~~

유명 사이트 3개를 비교해가면서 분석해봤다. Github와 Facebook은 `search`를 하나의 상위 도메인으로 보고 `search`뒤에 쿼리스트링을 추가하여 처리하고 있다. 그리고 보통 `검색어`에 해당하는 식별값은 구현하는 곳마다 다르지만 `q`를 사용하거나 `query`라는 값을 사용하는 것을 볼 수 있다. 


여기서 가장 궁금했던건 여태껏 나는 여러개의 검색조건이 있었을 때는 쿼리스트링을 여러개 붙였는데 Github나 Naver를 참고해보니 일종의 카테고리별로 URI를 맞춰나간걸 확인 해볼 수 있었다. 

Github는 검색조건을  `type`이라는 값을 통해 Code에 해당하는 검색조건 혹은 User 아이디에 해당하는 검색조건 등 type별로 URI를 생성한다. 이렇게 할 경우 다양한 검색조건 기능이 추가되더라도 API의 확장을 쉽게 가져갈 수 있는 이점이 있다는 생각이 든다. 



