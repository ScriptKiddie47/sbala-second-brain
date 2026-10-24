1. Aspect Oriented Programming
2. Think of it as a middleware but very much closer to the code.
3. Dependency - `implementation 'org.springframework.boot:spring-boot-starter-aspectj'`
4. In Boot 3.x it was `spring-boot-starter-aop`, and in 4.x it’s `spring-boot-starter-aspectj`
5. Lets define a simple controller

```java
package com.essi.centrum.controller;
@RestController
@RequestMapping("/api/v1/payments")
public class CentrumPaymentController {
    private static final Logger log = LoggerFactory.getLogger(CentrumPaymentController.class);
    private final CentrumPaymentService centrumPaymentService;
    public CentrumPaymentController(CentrumPaymentService centrumPaymentService) {
        this.centrumPaymentService = centrumPaymentService;
    }
    @PostMapping("/create")
    public ResponseEntity<?> createPayment(@RequestBody Payment payment){
        Payment paymentResponse = centrumPaymentService.createPayment(payment);
        return new ResponseEntity<>(paymentResponse, HttpStatus.CREATED);
    }
    @PostMapping("/update")
    public ResponseEntity<?> updatePayment(@RequestBody Integer paymentId){
        Payment paymentResponse = centrumPaymentService.updatePayment(paymentId);
        return new ResponseEntity<>(paymentResponse, HttpStatus.CREATED);
    }
    @DeleteMapping("/delete")
    public void deletePayment(){
        throw new RuntimeException("Error occurred when deleting payment"); // Bad Example for Exception. Never do this.
    }
}
```

#### Enable EnableAspectJAutoProxy

```java
package com.essi.centrum;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
@SpringBootApplication
@EnableAspectJAutoProxy
public class CentrumApplication {
	public static void main(String[] args) {
		SpringApplication.run(CentrumApplication.class, args);
	}
}
```

1. Spring Boot auto-configures AOP when `spring-boot-starter-aspectj` is on the classpath, so `@EnableAspectJAutoProxy` is optional. It is shown here for clarity.

#### CORE AOP CONCEPTS

1. Aspect -> The module eg. Logging,Transaction etc
2. Advice -> What we want to do ( Log execution methods in our case )
3. Pointcut -> Expression that selects join points
4. Join Point -> Where advice is applied
5. Weaving -> Linking aspects with the app during runtime.

```java
package com.essi.centrum;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.*;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {
    private static final Logger log = LoggerFactory.getLogger(LoggingAspect.class);
    /*
    * This will be run at execution time. Match all classes and methods
    * in below package
    * */
    @Pointcut("execution (* com.essi.centrum.controller.*.*(..))")
    public void controllerMethods(){}
    /*
     * Log Before pointcut method -> So Before any controller methods runs
     * We can get method signature ( name ) & arguments using the Joinpoint object
     * */
    @Before("controllerMethods()")
    public void logBefore(JoinPoint joinPoint){
        log.info("Calling Controller Method: {}() with arguments: {}",joinPoint.getSignature().getName(),joinPoint.getArgs());
    }
    /*
     * Log after controller method returns something
     * We refer to the pointcut and we save the results in a variable called 'response'
     * */
    @AfterReturning(pointcut = "controllerMethods()", returning = "response")
    public void logAfterReturning(JoinPoint joinPoint,Object response){
        log.info("Controller method {} returned {} ",joinPoint.getSignature().getName(),response);
    }
    /*
     * Log after an exception is thrown from the controller method because that's where the pointcut is
     * */
    @AfterThrowing(pointcut = "controllerMethods()", throwing = "exception")
    public void logException(JoinPoint joinPoint,Throwable exception){
        log.error("Exception in method: {}() : {}",joinPoint.getSignature().getName(),exception.getMessage());
    }
}
```

1. Don't think of it as too complex -> Its just a trigger that we set for any class,method. Before & After the method trigger we can trigger our own methods. That's all

#### Time Tracking Aspect with Custom Annotation

1. Create a custom annotation

```java
package com.essi.centrum.annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface TrackExecution {
}
```

1. Use it on a simple method like below

```java
package com.essi.centrum;
import com.essi.centrum.annotation.TrackExecution;
import com.essi.centrum.dto.Payment;
import org.springframework.stereotype.Service;
@Service
public class CentrumPaymentService {
    @TrackExecution
    public Payment createPayment(Payment payment) {
        sleep(2000);
        return payment;
    }
    public Payment updatePayment(Integer paymentId) {
        sleep(1000);
        return new Payment(paymentId);
    }
    private void sleep(int milliseconds){
        try {
            Thread.sleep(milliseconds);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}
```

1. AOP Code

```java
package com.essi.centrum.controller;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class ExecutionTrackerAspect {

    private static final Logger log = LoggerFactory.getLogger(ExecutionTrackerAspect.class);

    /*
    * Only tag methods/classes that are tagger with the below custom annotation.
    * proceedingJoinPoint.proceed() -> We are going to execute the method for example
    * in our case it will be 'createPayment()'
    * start & end happens before & after method gets executed.
    * We log the time difference, and we return the result.
    * */
    @Around("@annotation(com.essi.centrum.annotation.TrackExecution)")
    public Object logExecutionTime(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
        long start = System.currentTimeMillis();
        Object result = proceedingJoinPoint.proceed();
        long end = System.currentTimeMillis();
        String methodName = proceedingJoinPoint.getSignature().getName();
        log.info("Execution time for {} : {} ms",methodName,(end-start));
        return result;
    }
}
```

1. Always return the value of `proceed()` from an `@Around` advice. If you call `proceed()` and drop the result, the caller receives `null` (for example, an empty response body) and no error is shown.
2. An `@Around` advice that returns `Object` must declare `throws Throwable` because `proceed()` can throw. Don't swallow the exception unless you intend to hide it. Let it propagate so the caller and the logs see it.
