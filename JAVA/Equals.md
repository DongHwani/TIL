# Equals


### 1. 메모리 관점에서의 객체의 동일성 

`equals`에 대해 정리하기 전에 메모리관점에서 객체를 생각해보자.

객체는 new 연산자를 통해 생성되면 고유한 참조값을 가지고 있다. 같은 클래스 타입의 객체라도 생성이 두 번 이루어진다면, 그 두개의 객체는 `==` 연산자로 비교 할 때 서로 다른 객체로 인식하게 된다. 

~~~JAVA
 //서로 다른 메모리값을 가지고 있음으로 두 객체는 다르다. 
 Apple apple1 = new Apple(); 
 Apple apple2 = new Apple();

 assertThat(apple1 == apple2).isFalse();

 //같은 메모리값을 가지고 있음으로 서로 같은 객체이다. 
 Apple apple1 = new Apple(); 
 Apple apple2 = apple1;

 assertThat(apple1 == apple2).isTrue();
~~~

 이렇게 메모리관점에서의 객체를 사용할 경우, 같은 메모리값을 지닌 여러개의 객체 중에서 단 하나의 객체라도 값이 변경되면, 아래의 예제처럼 모든 객체에 변경된 값이 적용된다.

~~~JAVA
    Fruit apple = new Fruit("apple");
    Fruit apple2 = apple;
    Fruit apple3 = apple;

    //apple, apple2, apple3는 생성자로 받은 "apple"가 필드값에 동일하게 저장되어있다.

    //apple3에만 banan로 내용을 변경하면 무슨일이 생길까?
    apple3.setName("banana");
~~~

때로는 비즈니스 로직을 구성하는데 있어서, 서로 다른 생명주기를 가지되 `서로가 같은 객체`임을 논리적으로 구현하고 싶을 때가 있다. 

이럴경우 Object 클래스인 equals를 재정의하여 사용한다. 


### 2. 논리적인 관점에서의 객체의 동일성(equals)

Object클래스의 equals를 그대로 사용 할 경우 `==` 연산을 사용하기 때문에 객체간의 논리적인 동일성을 맞출 수가 없다. 그래서 비즈니스 로직에서 요구되는 사항에 맞춰 equals를 재정의하여 구현해야 한다. 

아주 간단한 예제로 Apple 클래스와 Banana 클래스는 과일상품 안에 들어가기 때문에 같은 객체로 인식이 필요하다고 생각해보자. 

~~~JAVA
public class Apple {
    
    private String name;
    
    public Apple(String name){
        this.name = name;
    }

    @Override
    public boolean equals(Object obj) {
        return obj instanceof Banana;
    }
}

public class Banana {

    private String name;

    public Banana(String name){
        this.name = name;
    }

    @Override
    public boolean equals(Object obj) {
        //다양한 조건을 구현하여 객체간의 논리적인 동일성을 만들 수 있다. 
        return obj instanceof Apple; 
    }
}

    @Test
    public void appleBanana(){
        Apple apple = new Apple("apple");
        Banana banana = new Banana("banana");

        assertThat(apple.equals(banana)).isTrue();
        assertThat(banana.equals(apple)).isTrue();

    
    }
~~~

이렇게 equals를 재정의함으로써 서로 논리적인 동일한 객체임을 만들 수가 있다. 


### 3. equals의 대칭성과 반사성

위의 과일 예제는 equals의 `대칭성`은 만족하였지만, `반사성`은 만족하지 못하였다.   
 euqals 메서드를 Apple 클래스와 Banana 클래스 두 군데 모두 다 정의하여 어느 클래스든 equals 메서드로 접근해도 동일한 결과값을 만족할 수 있는 것을 `대칭성`이라 한다. 만약 Apple클래스에만 equals를 재정의를 하게 되면 `apple.equals(banana)`는 `true`지만 `banana.equals(apple)`은 `false`를 반환하게 되어, 프로그래밍 혹은 도메인 모델이 깨져버리게 된다.

그런데, 여기서 문제는 Apple -> Banana 와 Banana -> Apple 간의 객체의 동일성은 보장되지만 정작 자신의 객체 동일성은 보장하지 못하는 문제가 발생한다.(`반사성`) 

~~~JAVA
    @Test
    public void appleBanana(){
        
        Apple apple = new Apple("apple");
        Banana banana = new Banana("banana");

        assertThat(banana.equals(banana))).isFalse(); 

        List<Apple> list = new ArrayList<>();
        list.add(apple);

        assertThat(list.contains(apple)).isFalse();
    }
~~~

이렇게 객체가 자기자신과 같지 않으면, Colletion 자료구조를 사용할 때에도 문제가 발생한다. 

~~~ JAVA
    //간단하게 자기자신과 같은 조건을 추가해야한다.
    @Override
    public boolean equals(Object obj) {
        return obj instanceof Banana || this == obj;
    }
~~~


### 3. 정리

위의 내용 이외에도 EffectiveJava 책을 참고하면 더 많은 내용을 알 수 있다. 

equals는 보통 hashcode와 기본적으로 같이 사용하여 논리적인 동일성을 보장하는 방식으로 구현한다고 한다. 그래서 다음에는 hashcode에 대한 내용을 정리 해봐야겠다. 


