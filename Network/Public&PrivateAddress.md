# Public Private Address

해당 글은 [생활코딩](https://www.youtube.com/watch?v=SZjlQbQJOWY&list=PLuHgQVnccGMBT57a9dvEtd6OuWpugF9SH&index=47) 학습한 내용을 정리한 글 

자신의 컴퓨터의 IP주소를 확인해보자. 리눅스환경에서 `ip addr` 명령어를 수행하면 ip를 확인 해볼 수 있다.

![ipaddr](/Network/img/ipaddr.png)

또 다른 방법으로 `curl ipinfo.io/ip` 에 명령어를 수행하면. ipinfo.io/ip는 접속하면 자신의 ip 주소를 알려준다.

![curl](/Network/img/curl.png)

`172.17.0.3`과 `13.125.107.216` 결과가 다른걸 알 수 있다. 
ip addr 명령어는 실제 물리적으로 해당 `컴퓨터 기기에 부착된 IP 주소를 읽는다.` 반면에 ipinfo.io/ip 는 해당 `url에 접속한 ip주소를 읽는다.`

여기서 Public Address와 Private Address 개념이 등장하는데, 

컴퓨터 기기에 부착된 IP 주소는 쉽게 이해가 가지만 url에 접속한 ip주소는 쉽사리 이해가 가지 않는다. 


우선, 보통 일반 가정집에서는 하나의 회선을 사용한다. (KT, SKT, LG...) 우리집은 KT를 사용하는데 이 우리집 KT 회선에 대한 IP주소가 `13.125.107.216`에 해당(Public Adress)된다. 
이 KT 하나의 회선을 여러 기기가 인터넷을 사용할 수 있도록 공유기(Router)를 사용한다. 

그러면 하나의 회선인 `13.125.107.216`(Public Address)를 공유기를 통해 여러 회선에서 사용 할 수 있는 것이다. 

그래서 외부에서 공개된 IP주소를 `Public Address`라 부르며, 기기별로 가진 IP는 외부에 공개되지 않았기 때문에 `Private Address`라 부른다. 


핸드폰과 노트북으로 ipinfo.io/ip 로 접속하면 동일한 IP를 얻는 반면에 노트북과 핸드폰 기기별 IP를 확인해보면 아예 다른걸 확인할 수 있다. 


