# chap 1

스프링

- 의존 주입(Dependency Injection :DI) 지원
- AOP(Aspect-Oriented Programming) 지원
- MVC 웹 프레임 워크 제공
- JDBC, JPA 연동, 선언적 트랜잭션 처리등 DB 연동 지원

환경설정

- jdk
- 메이븐
- 그래이들(실패)
- 이클립스

jdk 

1. jdk 다운로드
2. 고오급 설정 → 환경변수 에서 JAVA_HOME을 등록하고 JDK 설치 폴더 경로를 지정해준다

프로그램 구성 도구

1. 메이븐(maven)
    1. 설치
    2. 환경변수 에서 Path에 경로 등록
2. 그레이들(gradle) 메이븐과 같은 방식

이클립스(pc 비트를 확인후 설치한다 내컴은 64비트)

# chap2

프로젝트 폴더 생성

- C:\spring5fs\sp5-chap02\src\main\java 생성
- C:\spring5fs\sp5-chap02 가 프로젝트 폴더

메이븐 프로젝트 생성

- pom.xml 생성
    - xml은 마크업 언어의 일종
    - 놀랍게도 책에서는 어떻게 만드는지 안알려준다 cli 로도 만들수 있는 것 같으나,
    - vs code롤 생성했다
- 생성된 pom. xml
- 

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/maven-v4_0_0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>sp5</groupId>
<artifactId>sp5-chap02</artifactId>
<version>0.0.1-SNAPSHOT</version>

<dependencies>
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-context</artifactId>
<version>5.0.2.RELEASE</version>
</dependency>
</dependencies>

```
<build>
    <plugins>
        <plugin>
            <artifactId>maven-eclipse-plugin</artifactId>
            <version>3.7.0</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>utf-8</encoding>
            </configuration>
        </plugin>
    </plugins>
</build>

```

</project>

- <artifactId>sp5-chap02</artifactId> 8행 : 프로젝트 생성자 식별
- <dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-context</artifactId>
<version>5.0.2.RELEASE</version>
</dependency> 12~16행 :프로젝트에서 5.0.2.RELEASE 버전의 spring-context 모듈을 사용한다고 설정한다
- <plugin>
            <artifactId>maven-eclipse-plugin</artifactId>
            <version>3.7.0</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>utf-8</encoding>
            </configuration>
        </plugin>
    - 1.8 버전 기준으로 자바 소스를 컴파일 하고 결과 클래스 생성 인코딩은 utf-8로 설정

메이븐 의존 설정

<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-context</artifactId>
<version>5.0.2.RELEASE</version>
</dependency>

- spring- context 라는 식별자를 가진 5.0.2.RELEASE 버전의 아티팩트에 대한 의존을 추가
- 즉 자바 어플리 케이션에서 클래스 패스에 sping-context모듈을 추가 한다는것
    - 클래스 패스 : class 파일로 변환 된 경로

메이븐 레퍼지토리

- 원격 리포지토리 : 로컬 리포지토리에 파일이 없으면 다운로드 하는 곳
- 로컬 리포지토리 :  원격 리포지토리에서 다운받은 파일을 보관

의존전이(Transitive Dependencies)

- 의존 설정된 파일을 받아올시 그 파일이 의존하는 파일을 받아옴으로써 의존이 연쇄되는 현상

메이븐 기본 폴더구조( src\main\java )

- src\main\java  : 기본 폴더 구조
- src\main\resources : xml이나 프로퍼티 파일 등 자바 소스이외의 다른자원 파일
- src\main\webapp : 웹 어플리케이션 기준폴더 WEB-INF\web.xml 파일 등 작성

예제 코드 작성

- [Greeter.java](http://Greeter.java) : 콘솔에 간단한 메세지 출력
- [AppContext.java](http://AppContext.java) : 스프링 설정 파일
- Main.jave : main 메서드를 통해 스프링과 Greeter를 실행하는 자바 클래스

1. Greeter.java

**package** chap02;

**public** **class** Greeter {

**private** String format;

**public** String greet(String guest) {

**return** String.*format*(format, guest);

}

**public** **void** setFormat(String format) {

**this**.format = format;

}

}

1. AppContext.java

package chap02;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppContext {

```
@Bean
public Greeter greeter(){
	Greeter g = new Greeter();
	g.setFormat("%s, 안녕하세요!");
	return g;
}
```

}

- 스프링 객체를 Bean이라한다
- @Bean 어노테이션을 붙이면 해당 메서드가 생성한 객체를 스프링이 관리한다

1. Main.java

**package** chap02;

**import** org.springframework.context.annotation.AnnotationConfigApplicationContext;

**public** **class** Main {

**public** **static** **void** main(String[] args) {

AnnotationConfigApplicationContext ctx =

**new** AnnotationConfigApplicationContext(AppContext.**class**);

Greeter g = ctx.getBean("greeter", Greeter.**class**);

String msg = g.greet("스프링");

System.***out***.println(msg);

ctx.close();

}

}

- AnnotationConfigApplicationContext 클래스는 자바설정에서 정보를 읽어와 빈 객체를 생성하고 관리한다
- AnnotationConfigApplicationContext  객체에 AppContext.class를 파라미터로 전달
- ~~이제 안된다 ㅠㅠ~~
- AnnotationConfigApplicationContext  는 설정된 Bean 정보를 읽어와 Greeter 객체를 생성하고 초기화
- greeter() 메서드가 생성한 Greeter 객체를 리턴한다
- 값을 설정하고 출력한다
- 안된다..ㅠㅠ

스프링 객체 컨테이너

- ApplicationContext은 Bean객체를 생성하여 관리한다
- 생성, 초기화 ,보관,제거들을 관리하고 있어서 컨테이너 라고 부르기도한다
    
    

싱글톤(Singleton)객체

- 별로 설정하지 않을 경우 스프링은 한개의 빈 객체만을 생성한다
- 이를 싱글톤의 범위를 갖는다 한다
- 즉, 한개의 @Bean 어노데이션에 한개의 빈 객체를 생성한다

# chap3

의존

- 어떤 한 클래스를 사용하기 위해 다른 클래스를 사용하는것
- 이런경우 클래스 안에 의존하는 객체를 직접생성하는 것이 쉽다
- 하지만 뭔 문제가 발생한다고하고 이것때문에 의존주입을 하는것 같은데 나중에 알려준다고한다

DI(의존주입)을 통한 의존 처리

- 의존하는 객체를 직접 생성하는 대신 의존 객체를 전달 받는 방식
- 생성자를 통해서 의존객체를 전달받는 방식(다른방식도 존재)
- 변경의 유연함 이라는 장점
- 생성자로 값을 받기 때문에 후에 변경사항이 있을 시 객체를 생성하는 부분만 바꾸면된다

조립기(assembler)

- 객체 생성자를 모아둔곳
- 변경사항이 있을시 이제 여기서 객체를 생성하는 부분을 수정해주면된다

스프링은 위의 두기능을 범용으로 지원한다

- AnnotationConfigApplicationContext으로 컨데티너를 생성하고(=assembler 역활)
- @Bean 으로 어노테이션된 객체를 getBean으로 꺼내오는 방식

DI방식

1. 생성자 방식
    - 장 :  완전한 상태로 사용가능
    - 단:  어떤 의존객체를 설정하는지 알기 어렵다
2. 세터(setter) 메서드 방식
    - 장: 의존객체를 알기 쉽다
    - 단: 값이 없으면 애러가 난다

자동주입

@Autowired : 자동으로 의존하고 있는 객체 값을 주입해준다, @Bean 클래스에서 따로 의존 주입을 위한 값을 작성하지 않아도 된다

@Import : 함께 사용할 클래스를 설정한다, 두개 이상의 설정 파일을 사용하는 방법, 여러 클래스 설정도 가능하다

getBean() 매서드

- 빈으로 설정된 메소드를 가져온다
- getBean(”빈의 이름”, 빈의타입) 형식으로 사용한다