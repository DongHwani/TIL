# Call By Name & Call By Value 

scala에서 함수호출 방식은 call by name과 call by value 두 가지 방식의 평가전략이 있다. 

**call by value**
- 함수를 호출하기 전에 전달된 표현식 혹은 인수 값을 한번 계산한다.

~~~scala
// Syntax
 def map(x: Int) = x * x
~~~
~~~scala
 def map(x: Int) = x * x

 def mapXY(x: Int, y: Int) = map(x)+ map(y)

mapXY(5, 3+3)
~~~

mapXY(5, 3+3) call by value로 표현하면

① mapXY(5, 3+3)    
② mapXY(5, 6)    
③ map(5) + map(6)        
④ 5 * 5 + map(6)     
⑤ 25 + map(6)    
⑥ 25 + 6 * 6    
⑦ 25 + 36      
⑧ 61   

위의 과정이 이루어진다

 **call by name** 
 - 함수의 인자값이 사용될 때만 평가된다. 

~~~scala
// Syntax
 def map(x: => Int) = x * x
~~~

① mapXY(5, 3+3)    
② map(5) + map(3+3)  
③ 5 * 5 + map(3+3)        
④ 25 + map(3+3)      
⑤ 25 + (3+3) * (3+3)   
⑥ 25 + 6 * (3+3)   
⑦ 25 + 6 * 6     
⑧ 61 

call by name으로 평가시에 위와 같은 결과가 나오는데 연산 총 횟수는 call by value와 똑같지만 내용을 보면 **연산관계의 인자값이 사용될 때에만 평가**가 되는 걸 확인 할 수 있다. 

또 다른 예제를 확인해보고 어떻게 동작하는지 이해해보자. 

~~~scala 

object Strategy extends App{

  def time() = {
    println("Entered time() ...")
    System.nanoTime
  }

  def callByValue(t: Long) = {
    println("Entered exec, calling t ...")
    println("t = " + t)
    println("Calling t again ...")
    t
  }

  def callByName(t: => Long) = {
    println("Entered exec, calling t ...")
    println("t = " + t)
    println("Calling t again ...")
    t
  }

  //어떻게 나올까?
  println(callByValue(time()))
  println("=================")
  println(callByName(time()))

}

~~~
위의 코드의 공통점과 평가전략에 따른 차이점을 분석 해보면, 

[공통점]    
두 함수 모두 time()함수가 인자값으로 던져지고 있다. 
두 함수의 인자의 데이터 타입은 Long이다. 

[평가전략에 따른 분석]  
 함수호출 전 연산수행 , 사용되는 시점


[결과]
~~~bash
Entered time() ... 
Entered exec, calling t ...
t = 13224942084300
Calling t again ...
13224942084300
=================
Entered exec, calling t ...
Entered time() ...
t = 13224950909200
Calling t again ...
Entered time() ...
13224951167800
~~~
 
callByValue 함수로 던져진 time()를 먼저 수행한 후, 나머지 함수를 수행한다는 것을 알 수 있다.  
callByValue는 프로그래밍을 조금이라도 한 사람이라면 쉽게 이해 할 수가 있다. 

문제는 callByName인데 평소 함수형 프로그래밍을 접해보지 않는 사람(나같은 사람)이라면 좀 이해하기가 어렵다라는 생각이 든다. 

callByName의 출력값을 살펴보면, callByName()함수 내부에서 t가 사용될 때 time() 함수가 실행된 것을 볼 수 있다. 함수가 Long이라는 primitive 타입으로 변환되는 것이 아니라, 함수의 인자값에 함수 자체가 있어서 그 함수를 실행을 하는 것이었다.

실제 디버깅모드로 확인해보면 time()함수가 t의 사용횟수 만큼 실행된다.

 





