# Refactoring(1) - Exception 처리
   
**에외**

현재 맡고 있는 프로젝트의 Controller 혹은 Service에는 `try-catch`문을 사용하거나 특정 값을 return 하여
예외를 처리하고 있다.

~~~java
//try-catch
public void updateUser(User updatedUser){
    try{
        User user = userDao.findById(updatedUser.getUserId);
        user.updateInformation(updateUser);
        userDao.updateUser(user);
    }catch(NullPointerException e) {
        //예외처리 로직
    }
}

//특정 값 return
private static final int USER_NOT_FOUND = 10;

public int updateUser(User updatedUser){
    
    User user = userDao.findById(updatedUser.getUserId);
    if(user==null){
        return USER_NOT_FOUND;
    }

    int result = userDao.updateUser(updatedUser);
    return result; //받는 쪽에서 return 값에 따른 분기처리를 해줘야한다.
}
~~~

try-catch문이나 특정값 예외처리 로직을 사용할 경우, 예외처리가 1개 정도는 어느정도 읽힐 수가 있으나,
2개 이상이 될 경우에는 분기처리가 많아지거나 catch문이 많아져 코드의 가독성이 떨어진다. 또한, `비즈니스 로직`보다 `예외처리로직`이 더 비대해져서 배보다 배꼽이 큰 경우를 나을 수 있다.   

---
**비즈니스 로직과 예외 분리**

RuntimeException을 상속한 uncheked 예외를 사용하고, 사용자정의 예외를 만들어, Spring이 제공하는 `@ControllerAdvice`나 `@ExceptionHandler`를 통해 예외처리와 비즈니스 로직을 분리할 수 있다.


~~~java
public void updateUser(User updatedUser){
    User user = userDao.findById(updatedUser.getUserId);
    if(user==null){
        throw new UserNotFoundException(updatedUser.getUserId);
    }
    user.updateInformation(updateUser);
    userDao.updateUser(user);
}

//사용자 정의 예외 
public class UserNotFoundException extends RuntimeException{
    private static final message = "에 해당되는 id를 찾을 수 없습니다.";

    public UserNotFoundException(Long userId){
        super(userId+message);
    }
}

//@ControllerAdvice
@ControllerAdivce
public class HandlerAdvice{
    
    @ExceptionHandler(UserNotFoundException.class) 
    public ResponseEntity userNotfoundHandler(UserNotFoundException e) {

         //예외처리 로직.. 
         return new ResponseEntity<>(e.getMessage(), HttpStatus.BAD_REQUEST); 
    }
}
~~~

이렇게 핵심 비즈니스 로직과 예외처리 로직 분리를 통해 각각이 수행하고자 하는 책임에 집중할 수 있어 좀 더 확장성 있는 유연한 구조가 될 수 있다.