# SSH(Secure Shell)


네트워크 상의 다른 컴퓨터에 로그인하거나 원격 시스템에서 명령을 실행하고 다른 시스템으로 파일을 복사할 수 있도록 해 주는 응용 프로그램 또는 그 프로토콜을 가리킨다.

SSH 프로토콜은 `암호화` 기법을 사용하기 때문에 외부로부터 노출되더라도 암호화된 상태로 보인다. (기본포트 22)

 예를 들어 Client 컴퓨터(현재 내가 사용하는 컴퓨터)에서 Server 컴퓨터에 로그인하여 어떤 것을 수행하고자 할 때 이 SSH 프로토콜을 사용하게 된다. 

![SSH](/Network/img/SSH.png)

EX)  
전제조건 
 - Client 컴퓨터에 SSH Client가 구축되어있어야 한다.  
 - Server 컴퓨터에 SSH Server가 구축되어있어야 한다.  

1. rm, ls 등 원하는 명령어을 입력하면 SSH 프로토콜을 통해 request로 전달  
2. SSH Server가 해당 명령어를 수신 후 Server 컴퓨터에게 수신된 명령어를 수행하도록 전달 
3. Server 컴퓨터가 해당 명령어에 대한 결과물을 SSH Server에게 전달
4. SSH Server는 response값으로 명령어에 대한 결과물을 SSH Client에게 전달 

[Reference]  

[생활코딩](https://www.youtube.com/watch?v=Bxz-1EgyA7w&list=PLuHgQVnccGMBT57a9dvEtd6OuWpugF9SH&index=52)  
[위키백과](https://ko.wikipedia.org/wiki/%EC%8B%9C%ED%81%90%EC%96%B4_%EC%85%B8)