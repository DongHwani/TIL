# Atomic 

`Atomic`은 CAS알고리즘을 사용하여 멀티 쓰레드 프로그래밍 환경에서 공유자원(변수)에 대해 Thread-safety하게 동기화된 변수값을 제공한다. 

CAS 알고리즘에 대해 살짝 정리하기전에 멀티 thread 상황을 간단하게 살펴보자.

아래는 Effective Java2에 나온 예제이다. 

~~~java 
 public class StopThread {
     private static boolean stopRequested;

     public static void main(String[] args) throws InterruptedException{
        Thread backgroundThread = new Thread(()->{
             int i = 0;
             while(!stopRequested)
                i++;
        });
        
        backgroundThread.start();
        TimeUnit.SECONDS.sleep(1);
        stopRequested = true;

     }
 }
~~~ 
위 예제는 1초 뒤에 프로그램이 종료가 안되고, 계속해서 실행된 상태가 된다. 

멀티 thread 환경에서는 성능상의 이유로 각각의 thread가 `Main Memory`로부터 읽은 변수 값을 자신만 접근할 수 있는 `CPU cache`로 복사한다. 

![CPU CAHCE](/JAVA/img/cpu_cache.png)

그래서 최초 Main memory로부터 할당받은 false 값이 background thread의 cpu cache에 적재되어 있어 계속 false 값을 읽어 들이기 때문이다. 

main thread가 변경시킨 true값은 main thread의 cpu cache에만 변경이 되어있고, Main Memory로 부터 쓰는 시점과 Main Memory에 변경된 값을 background thread가 읽는 시점이 보장이 안된다. 


이러한 부분을 보완하기 위하여 `volatile` 혹은 `synchronized` 키워드를 사용하여 변수값을 동기화 할 수 있다. 

~~~java 
 public class StopThread {

     private static volatile boolean stopRequested;

     public static void main(String[] args) throws InterruptedException{
         //생략....

     }
 }

 ~~~

Atomic은 CAS 알고리즘을 사용하여 `synchronized`의 lock인 단점과 `volatile`의 쓰기 쓰레드 1개와 여러개의 쓰레드가 읽어야한다는 규칙(?)인 단점들을 보완하여 논블로킹 그리고 여러 쓰레드가 읽기와 쓰기를 병행을 할 수 있는 장점이 있다. 


위의 예제를 Atomic으로 바꿔보자.
~~~ java
public class StopThread {
    private static AtomicBoolean stopRequested = new AtomicBoolean(false);

    public static void main(String[] args) throws InterruptedException {
        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested.get())
                i++;
        });

        backgroundThread.start();
        TimeUnit.SECONDS.sleep(1);
        stopRequested.set(true);

    }
}
~~~

멀티 쓰레드 환경에서 공유되는 자원이 있다면, 좀 더 사용하기 쉬운 Atomic을 사용하자.