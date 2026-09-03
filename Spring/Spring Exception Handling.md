## Refresher on Java Exceptions

-> Checked Exception : Checked at compile time. Must handle

```java
public void throwCheckedException() throws IOException {
    throw new IOException("IO issue");
}
public void handleCheckedException(){
    try {
        throwCheckedException(); // I have to surround with try catch or throw it. No other way
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}
```

-> Un-checked Exception : Checked at Runtime. Notice how we don't need to explicitly catch it.

```java
public void throwUnchckedException() {
    throw new RuntimeException("IO issue");
}
public void handleUncCheckedException(){
    throwUnchckedException(); // IDGAF
}
```

->  Custom Exceptions 

```java
public class ClientMain {
    public static void main(String[] args) {
        System.out.println("Hello World");
        ClientMain clientMain = new ClientMain();
        clientMain.getUser();
        clientMain.getAccount();
        System.out.println("Line is skipped as exception is not handled"); // This is skipped.
        
    }

    void getUser() { // We had to address checked exception in a try catch or throw it
        try {
            throw new UserNotFoundException("User Not Found"); 
        } catch (UserNotFoundException e) {
            e.printStackTrace();
        }

    }

    void getAccount(){
       throw new AccountNotFoundException("Account Not Found");
    }

}

class UserNotFoundException extends Exception {
    public UserNotFoundException(String message) { // Checked Exception
        super(message);
    }
}

class AccountNotFoundException extends RuntimeException { // Unchecked Exception
    public AccountNotFoundException ( String message) {
        super(message);
    }
}
```

-> Output for the above

```bash
S Bala@LAPTOP-E79PK7CU MINGW64 ~/Documents/CodeSource/java-projects/scripts
$ java ClientMain.java 
Hello World
UserNotFoundException: User Not Found
        at ClientMain.getUser(ClientMain.java:13)
        at ClientMain.main(ClientMain.java:5)
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:103)
        at java.base/java.lang.reflect.Method.invoke(Method.java:580)
        at jdk.compiler/com.sun.tools.javac.launcher.Main.execute(Main.java:484)
        at jdk.compiler/com.sun.tools.javac.launcher.Main.run(Main.java:208)
        at jdk.compiler/com.sun.tools.javac.launcher.Main.main(Main.java:135)
Exception in thread "main" AccountNotFoundException: Account Not Found
        at ClientMain.getAccount(ClientMain.java:21)
        at ClientMain.main(ClientMain.java:6)

```

## Now we pivot to Spring Boot's Exception Handling

### 1. Simple Example of Runtime Exception being handled by Spring Controller

```java
package com.lexus.lexus.auto.controller;  
  
import org.slf4j.Logger;  
import org.slf4j.LoggerFactory;  
import org.springframework.http.HttpStatus;  
import org.springframework.http.ResponseEntity;  
import org.springframework.web.bind.annotation.ExceptionHandler;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
import java.time.LocalDateTime;  
  
@RestController  
public class LogiController {  
  
    Logger LOG = LoggerFactory.getLogger(LogiController.class);  
  
    @GetMapping("/user")  
    public ResponseEntity<?> getUser() {  
        User john = new User("John");  
        if(john.name().equals("John")){  
            throw new UserNotFoundException("John is not a valid User");  
        }        
        return ResponseEntity.ok(john);  
    }  
    @ExceptionHandler(UserNotFoundException.class)  
    public ResponseEntity<?> handleUserNotFoundException(Exception e){  
        LOG.error("User Exception : {}", e.getMessage(),e); // The 'e' here makes stack trace thrown into the mix as well. Perfect  
        return new ResponseEntity<>(new ErrorResponse(LocalDateTime.now(),e.getMessage()),HttpStatus.NOT_FOUND);  
    }
}    
record User(String name) {  
}  
  
class UserNotFoundException extends RuntimeException{  
    public UserNotFoundException(String message){  
        super(message);  
    }}  
  
class ErrorResponse{  
    private final LocalDateTime localDateTime;  
    private final String errorMessage;  
  
    public ErrorResponse(LocalDateTime localDateTime, String errorMessage) {  
        this.localDateTime = localDateTime;  
        this.errorMessage = errorMessage;  
    }  
    public LocalDateTime getLocalDateTime() {  
        return localDateTime;  
    }  
    public String getErrorMessage() {  
        return errorMessage;  
    }}
```

-> Above here we use `@ExceptionHandler` but the problem here is `@ExceptionHandler` method will only handle exception from this controller. Not others.
-> Output

```txt
2026-09-03T12:24:28.860+05:30  INFO 11976 --- [lexus-auto] [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 1 ms
2026-09-03T12:24:28.885+05:30 ERROR 11976 --- [lexus-auto] [nio-8080-exec-1] c.l.l.auto.controller.LogiController     : User Exception : John is not a valid User

com.lexus.lexus.auto.controller.UserNotFoundException: John is not a valid User
	at com.lexus.lexus.auto.controller.LogiController.getUser(LogiController.java:22) ~[main/:na]
	at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:103) ~[na:na]
	....
```

-> The above looks beautiful. We can actually extend the above and lets say you want to do some post processing upon catching the exception Note - Exception is generated from Service Layer here.

```java
@GetMapping("/user")
public ResponseEntity<?> getUser() {
    User user = null;
    try {
        user = logiService.getUser();
    } catch (UserNotFoundException e) {
        LOG.warn("Doing some Shenanigans here");
        throw e;
    }
    return ResponseEntity.ok(user);
}

@ExceptionHandler(UserNotFoundException.class)
public ResponseEntity<?> handleUserNotFoundException(Exception e) {
    LOG.error("User Exception : {}", e.getMessage(), e); // The 'e' here makes stack trace thrown into the mix as well. Perfect
    return new ResponseEntity<>(new ErrorResponse(LocalDateTime.now(), e.getMessage()), HttpStatus.NOT_FOUND);
}
```

-> Output

```txt
2026-09-03T12:27:27.899+05:30  WARN 22892 --- [lexus-auto] [nio-8080-exec-2] c.l.l.auto.controller.LogiController     : Doing some Shenanigans here
2026-09-03T12:27:27.901+05:30 ERROR 22892 --- [lexus-auto] [nio-8080-exec-2] c.l.l.auto.controller.LogiController     : User Exception : John is not a valid User

com.lexus.lexus.auto.controller.UserNotFoundException: John is not a valid User
	at com.lexus.lexus.auto.controller.LogiService.getUser(LogiController.java:52) ~[main/:na]
	at com.lexus.lexus.a
```
### 2. Bad Example of Exception Handling - Overwriting `@ExceptionHandler` behavior

```java
package com.lexus.lexus.auto.controller;  
  
import org.slf4j.Logger;  
import org.slf4j.LoggerFactory;  
import org.springframework.http.HttpStatus;  
import org.springframework.http.ResponseEntity;  
import org.springframework.stereotype.Service;  
import org.springframework.web.bind.annotation.ExceptionHandler;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
import java.time.LocalDateTime;  
  
@RestController  
public class LogiController {  
  
    private final Logger LOG = LoggerFactory.getLogger(LogiController.class);  
    private final LogiService logiService;  
  
    public LogiController(LogiService logiService) {  
        this.logiService = logiService;  
    }  
    @GetMapping("/user")  
    public ResponseEntity<?> getUser() {  
        User user = null;  
        try {  
            user = logiService.getUser();  
        } catch (UserNotFoundException e) {  
            LOG.warn("Doing some Shenanigans here");  
            return new ResponseEntity<>(HttpStatus.CONFLICT);  
        }        return ResponseEntity.ok(user);  
    }  
    @ExceptionHandler(UserNotFoundException.class)  
    public ResponseEntity<?> handleUserNotFoundException(Exception e) {  
        LOG.error("User Exception : {}", e.getMessage(), e); // The 'e' here makes stack trace thrown into the mix as well. Perfect  
        return new ResponseEntity<>(new ErrorResponse(LocalDateTime.now(), e.getMessage()), HttpStatus.NOT_FOUND);  
    }}  
  
record User(String name) {  
}  
  
@Service  
class LogiService {  
    public User getUser() {  
        User john = new User("John");  
        if (john.name().equals("John")) {  
            throw new UserNotFoundException("John is not a valid User");  
        }        return john;  
    }}  
  
class UserNotFoundException extends RuntimeException {  
    public UserNotFoundException(String message) {  
        super(message);  
    }}  
  
class ErrorResponse {  
    private final LocalDateTime localDateTime;  
    private final String errorMessage;  
  
    public ErrorResponse(LocalDateTime localDateTime, String errorMessage) {  
        this.localDateTime = localDateTime;  
        this.errorMessage = errorMessage;  
    }  
    public LocalDateTime getLocalDateTime() {  
        return localDateTime;  
    }  
    public String getErrorMessage() {  
        return errorMessage;  
    }}
```

-> Output 

```txt
2026-09-03T12:23:16.674+05:30  WARN 4760 --- [lexus-auto] [nio-8080-exec-2] c.l.l.auto.controller.LogiController     : Doing some Shenanigans here
```

-> No error logs or stack trace and we get a 409 back.

## 3. @RestControllerAdvice

```java
@RestControllerAdvice  
class LogiGlobalException{  
    private final Logger LOG = LoggerFactory.getLogger(LogiGlobalException.class);  
    @ExceptionHandler(UserNotFoundException.class)  
    public ResponseEntity<?> handleUserNotFoundException(Exception e) {  
        LOG.error("User Exception : {}", e.getMessage(), e); // The 'e' here makes stack trace thrown into the mix as well. Perfect  
        return new ResponseEntity<>(new ErrorResponse(LocalDateTime.now(), e.getMessage()), HttpStatus.NOT_FOUND);  
    }
}
```

We just define a global class that takes care of all Controller. Much easier this way.