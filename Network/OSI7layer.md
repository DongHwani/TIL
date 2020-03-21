# OSI 7 Layer


### 학습목표
OSI 7계층에 대한 개념과 계층별 역할을 이해한다.  
전체적인 네트워크 플로우 흐름을 이해를 목표로 하며, 글로 정리 할 수 있어야 한다.
   

### OSI 7계층 개념 

`OSI(Open Systems Interconnection)`는 어플리케이션이 네트워크 통신방법에 대한 모델이다. 

![OSI7계층구조](/Network/img/OSI7.png)

맨 아래의 1계층인 물리계층부터 제일 상단의 어플리케이션 계층을 보면, 저수준에서 고수준으로 나누어 캡슐화된 상태의 네트워크 모델을 제공하고 있다. 

각각의 레이어 별로 역할에 해당되는 기능들을 구현햐고 있고, 고수준에서 저수준의 기능을 사용할 수 없다. 프로그래밍에서  추상클래스처럼 부모클래스가 확장되고 추가된 자식클래스를 사용할 수 없는 것처럼 계층 단위가 올라갈 수록 추상도가 더 높아짐을 뜻한다. 

그래서 우리는 Application Layer 영역에서 손쉽게 Http 프로토콜을 사용할 수 있는 것이다.

### OSI 계층간 역할

 - Physical Layer(물리계층)  
 이름에서 알 수 있듯이 장치간 실제 물리적인 연결 역할을 하며, 컴퓨터의 원초적인 언어가 0,1로 이루어져 있듯이 물리계층은 Bit정보를 포함하며, 다음 계층인 Data Link 계층으로 데이터를 전송한다.

 - Data Link Layer(데이터링크 계층)
 



### 정리



[Reference]  
 [TCP-IP관련](https://evan-moon.github.io/categories/programming/)  
 [계층역할](https://www.geeksforgeeks.org/layers-of-osi-model/)

