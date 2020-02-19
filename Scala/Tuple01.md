### Scala Tuple

Tuple은 여러개의 Type을 가질 수 있는 자료구조이다. 또한 불변적이다.

~~~scala
val ingredient = ("Sugar", 25)
println(ingredient) //(Sugar, 25) 
~~~

Tuple을 선언한 변수다. 
위의 ingredient 변수는 String 타입의 요소와 Int 타입의 요소를 포함하였다.

Scala 공식홈페이지에서는 해당 ingredient의 추론된 타입은 Tuple2[String, Int]의 약칭인 (String, Int)라고 되어있다.

무슨 말일까?

~~~scala
  val ingredient = new Tuple2[String, Int]("Sugar", 25)
  println(ingredient)
~~~
두번째 예시에서 축약된 구조가 첫번째 예시이다. 실제로 Tuple 클래스는 총 22가지가 있다. 

![tuple 클래스](/Scala/20200220_015905.png)

내부 클래스를 살펴보면, 

~~~scala
package scala

final case class Tuple2[@specialized(Int, Long, Double, Char, Boolean/*, AnyRef*/) +T1, @specialized(Int, Long, Double, Char, Boolean/*, AnyRef*/) +T2](_1: T1, _2: T2)
  extends Product2[T1, T2]
{
  override def toString(): String = "(" + _1 + "," + _2 + ")"

  def swap: Tuple2[T2,T1] = Tuple2(_2, _1)

}
~~~

final 키워드가 붙어 있어 불변클래스인걸 눈으로 확인 할 수 있다. 
또한, 매개타입으로는 기본적인 Primitive 타입과 AnyRef로 되어있는데 레퍼런스 타입으로 생각하면 된다. (Java에서의 Object타입과 비슷) 그래서 모든 타입을 받을 수 있는 구조가 된다. 

사용자정의 래퍼런스타입 예제
~~~scala
  val customIngredient = (1, new CustomClazz)
  println(customIngredient)
~~~



#### 요소 꺼내기 
~~~scala
  val ingredient = ("Sugar", 25)
  
  println(ingredient._1)//Sugar
  println(ingredient._2)//25
~~~
 Tuple의 요소는 언더스코어+숫자 형태로 해당 요소를 추출 할 수 있다. 특이한점은 Tuple은 0부터 시작이 아닌 1부터 시작한다는 것을 알 수 있다.


#### swap 메서드
~~~scala
  val swapIngredient = ingredient.swap
  println(swapIngredient)//(25,Sugar)
~~~
Tuple 클래스에 정의된 swap 메서드를 보면 _1와 _2의 요소를 바꾼 뒤 새로운 Tuple을 생성하여 리턴해주는 것을 알 수 있다. 


#### 패턴 매칭
공식 홈페이지에서는 패턴 매칭이라고 되어있는데 정의된 자료구조를 다른 변수로 할당된다고 생각하면 된다.

~~~scala
  val (name, quantity) = ingredient
  println(name)//Sugar
  println(quantity)//25
~~~

요소 순서에 정의된 타입을 추론하여 name은 String 타입으로 quantity는 Int타입으로 추론되었다.

또 다른 예시,
~~~scala
  val planets = List(("Mercury", 57.9), ("Venus", 108.2), ("Earth", 149.6), ("Mars", 227.9), ("Jupiter", 778.3))

  planets.foreach {
    case ("Earth", distance) => println(s"Our planet is $distance million kilometers from the sun")
    case _ =>
  }
~~~
위의 예제는 List의 데이터 타입이 Tuple2로 구성되어있다. 
case문은 Java로 치면 일종의 swich문과 같으며 println은 s문자열을 넣어줌으로써 출력하고자 하는 String 값을 동적으로 대입할 수 있다. 

두번째 case문의 언더스코어 "_" 는 위의 첫번째 Case이외에 모든 것을 나타낸다. 

또 다른 예제,
~~~scala
val numPairs = List((2, 5), (3, -7), (20, 56))
for ((a, b) <- numPairs) {
  println(a * b)
}
~~~
"<-" 해당 모양의 화살표는 for 루프문의 반복자를 일컫는다. 


