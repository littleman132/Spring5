# Chapter 4 - 의존 자동 주입

 ### @Autowired를 이용한 의존 자동 주입

- 자동 주입을 사용하지 않은 코드

```java
@Bean
public MemberDao memberDao(){
    return new MemberDao();
}

@Bean
public ChangePasswordService changePwdSvc(){
    ChangePasswordService pwdSvc = new ChangePasswordService();
    pwdSvc.setMemberDao(memberDao());
    return pwdSvc;
}
```

- 자동 주입 사용 시

```java
import org.springframework.beans.factory.annotation.Autowired;

public class ChangePasswordService{

    @Autowired
    private MemberDao memberDao;

    ...

}

```

```java
@Bean
public MemberDao memberDao(){
    return new MemberDao();
}

@Bean
public ChangePasswordService changePwdSvc(){
    ChangePasswordService pwdSvc = new ChangePasswordService();
    //pwdSvc.setMemberDao(memberDao());
    return pwdSvc;
}
```

- 의존 주입 대상인 memberDao 필드에 `@Autowired` 애노테이션을 붙힌다. <br> -> 해당 타입의 빈 객체를 찾아서 필드에 할당한다.
- 알맞은 빈 객체가 주입되지 않을 경우 필드는 Null, NullPointerException이 발생.

---------
<br>

- 메서드에도 `@Autowired` 애노테이션 사용이 가능하다.


```java
...

public class MemberInfoPrinter{
    
    private MemberDao memDao;
    private MemberPrinter printer;

    ...

    @Autowired
    public void setMemberDao(MemberDao memberDao){
        this.memDao = memberDao;
    }

    @Autowired
    public void setPrinter(MemberPrinter printer){
        this.printer = printer;
    }

}
```
- 메서드에서 타입이 일치하는 MemberDao , MemberPrinter 빈 객체를 자동으로 주입해준다.

### @Qualifier을 이용한 의존 객체 선택

- @Autowired에서 발생할 수 있는 Exception
  
  - 일치하는 빈이 없을 경우 : UnsatisfiedDependencyException
  - 타입이 같은 빈이 여러개인 경우 : UnsatisfiedDependencyException

- `@Qualifier` : 자동 주입 가능한 빈이 두 개 이상일 때 대상 빈을 한정 할 수 있다.

```java
public class UserService {

    private UserDao userDao;

    @Autowired
    @Qualifier("dao1")
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
}
```
```java
@Configuration
public class ApplicationContext {

    @Bean
    @Qualifier("dao1") // <- 해당 bean이 사용된다!
    public UserDao userDao1() {
        return new UserDaoImpl1();
    }

    @Bean
//    @Qualifier("dao2")
    public UserDao dao2() { // @Qualifier 어노테이션 생략 시 빈의 이름을 메서드명으로 지정
        return new UserDaoImpl2();
    }
}
```

- @Qualifier을 설정하지 않을 경우 빈의 이름은 메서드명으로 지정된다.

### @Autowired 필수 여부 설정

자동 주입이 필수가 아닌 경우에 설정하는 방법

#### &emsp;1. @Autowired(required=false)

- false로 지정시 매칭되는 빈이 없어도 Exception이 발생하지 않는다.

```java
public class UserService {

    @Autowired(required = false)
    private UserDao userDao;
}
```

- 매칭되는 빈이 없을경우 메소드는 실행되지 않는다.
```java
public class UserService {

    private UserDao userDao;
    
    ...

    @Autowired(required = false)
    public void setUserDao(UserDao userDao){ // 매칭되는 빈이 없을경우 실행되지 않는다.
        this.userDao = userDao;
    }
}
```

#### &emsp;2. Optional

  - 타입을 Optional로 선언시 빈이 존재하지 않을 경우 값이 없는 Optional을 인자로 전달한다.

```java
public class UserService {

    @Autowired(required = false) // 인자값을 Optional로 전달
    private Optional<UserDao> userDao;  
}
```

#### &emsp;3. @Nullable

  - setter 메서드에서 @Nullable을 붙이면 존재하지 않을 경우 Null로 전달한다.

```java
public class UserService {

    private UserDao userDao;
    
    ...

    @Autowired // 매칭되는 빈이 없을 경우에도 무조건 실행된다.
    public void setUserDao(@Nullable UserDao userDao){
        this.userDao = userDao;
    }
}
```

### 자동 주입과 명시적 의존 주입

- 자동 주입 대상을 명시적으로 의존을 주입시켰을 경우

```java

...

@Bean
@Qualifier("printer")
public MemberPrinter memberPrinter1(){
    return new MemberPrinter();
}

@Bean
@Qualifier("summaryPrinter")
public MemberSummaryPrinter memberPrinter2(){ // memberPrinter를 상속 받은 클래스
    return new MemberSummaryPrinter();
}



@Bean
public MemberInfoPinter infoPrinter(){
    MemberIntoPrinter infoPrinter = new MemberIntoPrinter();
    infoPrinter.setPrinter(memberPrinter2()); // memberSummaryPrinter를 주입
    return infoPrinter;
}

```

```java
public class MemberInfoPrinter{
    ...
    @Autowried
    @Qualifier("printer")
    public void setPrinter(MemberPrinter printer){
        this.printer = printer
    }
}
```

- 위 코드에서 결과는 설정 클래스에서 setter로 의존을 주입해도 @Autowired가 붙어 있으면 자동 주입을 통해 일치하는 빈을 주입한다.

#### 자동 주입과 수동 주입

> 코드를 섞어서 사용 할 경우 주입을 제대로 하지 않아 NullPointerException이 발생했을 때 원인을 찾는데 오래 걸릴 수 있다.
<br>자동 주입을 사용한다면 일관되게 사용해야 이러한 문제를 줄일 수 있다.

어플리케이션 기능은 크게 핵심 기능과 부가 기능으로 나뉜다.

* 핵심 기능: 해당 객체가 제공하는 고유한 기능 (ex: CustomerService - 구매 로직)
* 부가 기능: 핵심 기능을 보조하는 기능 (ex: 로그 추적, 트랜잭션 처리 등)
 

부가 기능은 핵심과 비교해 수가 적고 어플리케이션에 광범위하게 영향을 미친다. 이런 부분에 대해서는 수동 주입을 통해 명확하게 드러내는 것이 좋을 때도 있다.