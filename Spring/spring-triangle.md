# Spring Triangle : IOC, AOP, PSA

스프링은 엔터프라이즈 애플리케이션 개발을 편리하게 하기 위해 등장하였습니다. 비즈니스와 애플리케이션 로직의 복잡함을 상대하기 위해 스프링은 객체지향과 DI라는 핵심 도구를 가지고 유연하고 확장성 있는 설계를 가능하게 만들었습니다.

스프링은 순수 자바 오브젝트(POJO)를 이용해 특정 환경과 기술에 종속되지 않고 비즈니스 로직 구현이 가능한데, 이러한 배경에는 스프링을 지탱하고 있는 3대 가능 기술 IoC, AOP, PSA가 있습니다.

## **IoC/DI : Inversion of Control, Dependency Injection**

### **의존성 주입**

A → B(인터페이스)라는 의존관계를 갖는 구조일때, A가 생성자를 통해 B의 구현 클래스 인스턴스를 외부에서 주입받는 방식을 **의존성 주입**이라고 합니다. B의 변경이 A에 영향을 주지 않기 때문에 B의 입장에서는 유연한 확장이 가능하며, A의 입장에서는 변경 없이 재사용이 가능합니다(OCP). 이때 B의 구현 클래스 인스턴스를 생성하고 주입하는 역할을 스프링의 IoC 컨테이너가 하게 됩니다.

### **IoC 컨테이너**

라이브러리와 프레임워크를 나누는 기준은 개발자가 제어권을 갖느냐 아니냐로 볼 수 있습니다. 스프링은 IoC 컨테이너를 통해 제어의 역전을 제공하기 때문에 프레임워크라고 부릅니다. 스프링은 관계설정의 책임을 BeanFactory(ApplicationContext)에 위임함으로써 유연한 확장을 가능하게 합니다.

> **ApplicationContext vs Bean Factory**
>
> ApplicationContext는 빈 팩토리를 조금 더 확장한 개념입니다. 스프링의 AOP기능을 쉽게 이용할 수 있도록 해주고, MessageResource관리, 이벤트 발행 및 응용 계층을 위한 컨텍스트(ex WebApplicationContext)를 제공합니다. 따라서 Bean Factory라고 말할 때는 IoC의 기본기능에 초점을 맞추게 되고, ApplicationContext 라고 말하면 애플리케이션 전반에 걸쳐 모든 구성요소의 제어 작업을 담당하는 IoC엔진이라는 의미가 강조됩니다.


IoC컨테이너는 어떤 객체를 어떻게 관리해야할지에 대한 정보를 Configuration Metadata로부터 얻습니다. Configuration MetaData는 XML, 어노테이션, 또는 자바코드로 표현할 수 있습니다.

**XML 기반의 구성 정보**

```
<?xml version="1.0" encoding="UTF-8"?>
<beans>
    <bean id="myService" class="com.acme.services.MyServiceImpl"/>
</beans>
```

**Annotation 기반의 구성정보**

```
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```

## **AOP : Aspect Oriented Programming**

AOP는 프로그래밍 패러다임으로 공통관심사를 분리하여 모듈화를 이끌어 내는것입니다. 객체지향에서 모듈화의 단위를 클래스(Class)라고 본다면, AOP에서는 모듈화의 단위를 애스팩트(Aspect)로 봅니다. 애스팩트는 여러 객체와 타입에 걸친 공통 관심사(ex 트랜잭션 관리)의 모듈화를 가능하게 합니다.

![Image](https://docs.spring.io/spring-framework/docs/3.0.0.M4/reference/html/images/tx.png)

스프링에 적용된 가장 인기 있는 AOP의 적용대상은 [선언적 트랜잭션기능](https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction-declarative)입니다. 선언적 트랜잭션은 소스코드에 직접 트랜잭션 관련 로직을 넣어두지 않고 비즈니스 로직에서 분리해 냅니다. 비즈니스 로직을 담당하고 있는 객체는 트랜잭션을 어떻게 해야할지에 대한 구체적인 방법과 환경에 종속되지 않으며, 비즈니스 로직에 대한 테스트를 손쉽게 만들어낼 수 있게 됩니다.

### **용어 정리**

-   **Aspect** : 여러 클래스에 걸친 공통관심사의 모듈화
-   **Join point** : 프로그램을 실행할때의 시점으로 스프링 AOP 에서는 메서드가 실행될때를 말합니다.
-   **Advice** : aspect를 통해 특정 join point에서 가져온 부가기능을 의미합니다. 부가기능을 언제 실행할지에따라 around, before, after 등의 타입으로 나뉩니다.
-   **Pointcut** : Advice를 적용할 Join Point를 선별하는 기능을 정의한 모듈을 말합니다.
-   **Weaving** : PointCut에 의해 결정된 타겟에 Advice를 삽입하는 과정을 말합니다.
-   **Advisor** : Pointcut과 Advice를 하나씩 갖고 있는 오브젝트로 부가기능을 어디에 전달할 것인가를 알고있는 AOP의 기본 모듈입니다. 스프링 AOP에서만 사용되는 특별한 용어입니다.

### **AOP 적용기법**

-   다이나믹 프록시 사용
    -   스프링의 기본적인 AOP구현 방법
    -   기존코드에 영향을 주지 않고 부가기능을 적용하게 해주는 데코레이터 패턴을 응용한 것으로 만들기 쉽고 적용하기 간편합니다.
    -   부가기능을 부여할 수 있는곳이 메소드의 호출이 일어나는 지점뿐이라는 제약이 있습니다.
-   AspectJ
    -   오픈소스 AOP툴로 프록시 방식 AOP에서는 불가능한 다양한 조인포인트를 제공합니다
    -   메소드 호출뿐만아니라 인스턴스 생성, 필드 액세스, 특정 호출 경로를 가진 메소드 호출 등에도 부가기능을 제공할 수 있습니다.

## **PSA : Portable Service Abstraction**

POJO로 개발된 코드는 특정 환경이나 구현방식에 종속적이지 않아야 합니다. 이를위해 스프링이 제공하는 대표적인 기술이 바로 서비스 추상화 기술입니다. 스프링은 다양한 기술에 서비스 추상화를 제공하는데 그중 스프링 MVC와 트랜잭션에서의 서비스 추상화를 살펴 보도록 하겠습니다.

### **MVC에서의 서비스 추상화**

스프링 컨트롤러에서 메서드를 작성할때 특정 URL의 HTTP 메서드 요청을 처리하기 위해 @GetMapping, @PostMapping과 같은 어노테이션을 사용하곤 합니다. 이때 서블릿 애플리케이션을 만들고 있음에도 불구하고 다음과 같은 서블릿 코드를 작성하지 않습니다.

```
public class UserSignupServlet extends HttpServlet {

  @Override
  protected void doPost(
      HttpServletRequest req,
      HttpServletResponse resp
  ) throws ServletException, IOException {
    super.doPost(req, resp);
    // ...
  }

}
```

대신 단순히 어노테이션 사용만으로 위의 서블릿 기반의 코드를 동작하게 할 수 있습니다.

```
@PostMapping("signup")
@ResponseStatus(HttpStatus.CREATED)
public void signup(
    @Valid @RequestBody
    UserSignupRequest request
) {
  userService.signup(request.username(), request.profileImgUrl());
}
```

이렇게 스프링이 서비스 추상화를 제공하는 이유는 위와 같은 편의성을 제공하기 위함과 기술에 종속적이지 않은 코드를 작성할 수 있도록 도와주기 위함도 있습니다. 위의 코드를 변경하지 않고 spring-boot-starter-web의존성을 spring-boot-starter-webflux 의존성으로 변경하더라도 코드의 변경없이 정상적으로 실행됩니다.

### **트랜잭션에서의 서비스 추상화**

먼저 JDBC로 트랜잭션을 구현한 코드를 살펴보도록 하겠습니다.

```
package com.mkyong.jdbc;

import java.math.BigDecimal;
import java.sql.*;
import java.time.LocalDateTime;

public class TransactionExample {

    public static void main(String[] args) {

        try (Connection conn = DriverManager.getConnection(
                "jdbc:postgresql://127.0.0.1:5432/test", "postgres", "password");
             Statement statement = conn.createStatement();
             PreparedStatement psInsert = conn.prepareStatement(SQL_INSERT);
             PreparedStatement psUpdate = conn.prepareStatement(SQL_UPDATE)) {

            statement.execute(SQL_TABLE_DROP);
            statement.execute(SQL_TABLE_CREATE);

            // start transaction block
            conn.setAutoCommit(false); // default true

            // Run list of insert commands
            psInsert.setString(1, "mkyong");
            psInsert.setBigDecimal(2, new BigDecimal(10));
            psInsert.setTimestamp(3, Timestamp.valueOf(LocalDateTime.now()));
            psInsert.execute();

            psInsert.setString(1, "kungfu");
            psInsert.setBigDecimal(2, new BigDecimal(20));
            psInsert.setTimestamp(3, Timestamp.valueOf(LocalDateTime.now()));
            psInsert.execute();

            // Run list of update commands

            // error, test roolback
            // org.postgresql.util.PSQLException: No value specified for parameter 1.
            psUpdate.setBigDecimal(2, new BigDecimal(999.99));
            //psUpdate.setBigDecimal(1, new BigDecimal(999.99));
            psUpdate.setString(2, "mkyong");
            psUpdate.execute();

            // end transaction block, commit changes
            conn.commit();

            // good practice to set it back to default true
            conn.setAutoCommit(true);

        } catch (Exception e) {
            e.printStackTrace();
        }

    }

    //...

}
```

이렇게 JDBC에서 트랜잭션을 적용하기위해서는 commit과 관련된 코드들을 작성해야 하지만, 스프링에서는 단순히 @Transactional 어노테이션을 사용하기만 하면 트랜잭션을 적용할 수 있습니다.

이러한 트랜잭션은 사용하는 기술에따라 트랜잭션을 관리하는 구현체를 JDBC를 사용하면 DatasourceTransactionManager를, JPA를 사용하면 JpaTranscationManager로 바꾸어 사용할 수 있습니다.

## 참고

-   [https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop)
-   토비의 스프링 3.1
-   백기선 - 예제로 배우는 스프링 입문, 12 스프링 PSA