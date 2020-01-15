# Transactional 전파옵션



### 1. Required Propagation
 이미 시작된 트랜잭션이 있으면 해당 트랜잭션에 참여하게 되고, 없을 경우에는 **새로운 트랜잭션이 시작된다.** 


### 2. SUPPORTS Propagation
이미 시작된 트랜잭션이 있으면 해당 트랜잭션에 참여하게 되고, 
없을 경우에는 **트랜잭션이 없는 상태에서 실행된다.** 


### 3. MANDATORY Propagation
이미 시작된 트랜잭션이 있으면 해당 트랜잭션에 참여하게 되고, 없을 경우에는 **예외**를 발생시킨다.

### 4. REQUIRES_NEW Propagation
현재 시작되는 트랜잭션이 있으면, 잠시 **중단**하고, **새 트랜잭션을 만든다.** 

### 5. NEVER Propagation
트랜잭션을 사용하지 않도록 강제하며, 이미 진행중인 트랜잭션이 있으면 예외를 발생시킨다. 

### 6. NOT_SUPPORTED Propagation
이미 시작된 트랜잭션이 있으면 일시 중단 한 다음, 트랜잭션 없이 실행된다.


### 7. NESTED Propagation
일명 중첩트랜잭션, 이미 시작된 트랜잭션이 있으면, 부모 트랜잭션에 **Save Point**를 표시하고, 자식 트랜잭션으로 진행된다. 만약 예외가 발생하면, **Save Point**로 롤백 된다.
만약 시작된 트랜잭션이 없을 경우, Required처럼 작동한다.