# OSI 7 Layer
네트워크의 가장 기본적인 OSI 7계층에 대해 정리한다. 

### 1. OSI 7계층 개념 
 OSI 7계층이 나오기 이전에는 각 사별로 네트워크 통신방법 규격이 제 각각이여서 이기종간 통신이 굉장히 어려웠다고 한다.

업계 표준화를 위해 `OSI(Open Systems Interconnection)`는 어플리케이션간의 네트워크 통신방법에 대한 표준화된 모델이다.  

![OSI7계층구조](/Network/img/OSI7.png)

프로그래밍에서 `Layer`란 용어가 굉장히 자주 등장한다. 각 계층별로 맡은 역할이 존재하고, 상위의 계층이 하위의 계층에 의존되도록 설계되어 있다. 

### 2. OSI 계층간 역할

 - **Physical Layer(L1)**  
 이름에서 알 수 있듯이 장치간 실제 물리적인 연결 역할을 하며, 컴퓨터의 원초적인 언어가 0,1로 이루어져 있듯이 물리계층은 Bit정보를 포함한다.
 (물리적인 세부 사항들을 정의하는 부분)

 - **Data Link Layer(L2)**  
 Node간 데이터 전송을 제공하는 역할을 담당. 두 장치간의 물리적인 연결 설정과 종료하는 프로토콜을 정의한다.  
 데이터 링크 계층은 세부적으로 두 개의 부계층이 존재한다.   
 첫번째, `MAC Layer`는 노드간의 통신을 할 수 있게 주소 지정과 채널 엑세스를 제어하는 역할을 담당  
 두번째, `LLC Layer`는 통신 링크 관리 및 프레임 트래픽 처리를 담당


 - **NetWork Layer(L3)**  
 네트워크 계층(Network layer)은 주소부여(IP)와 경로설정(Route) 역할을 담당한다. 데이터링크계층에 MAC 주소가 기기의 변하지않는 물리적인 주소를 뜻한다면, 네트워크 계층의 IP 주소는 논리적인 주소를 의미한다.
 
 
 




### 정리

[Reference]  
 [TCP-IP관련](https://evan-moon.github.io/categories/programming/)  
 [계층역할](https://www.geeksforgeeks.org/layers-of-osi-model/)
 [위키피디아](https://ko.wikipedia.org/wiki/OSI_%EB%AA%A8%ED%98%95)

