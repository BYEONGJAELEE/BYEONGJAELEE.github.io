---
layout: post
title:  "[Spring Boot] 기본 구조 알아보기"
date:   2018-06-10 00:47:25 +0900
categories: jekyll update
---


- POJO ? 
    - Plain Old Java Object라는 뜻으로 “평범한 옛날 자바 객체”를 말한다. 

- Spring Framework의 장점 ? 
    - 과거에는 Java로 Web Application을 설계하기 위해 Servlet Class를 상속받아 구현해야 함. (servlet Class는 POJO가 아님) 
    - Spring Framework는 Servlet Class를 직접 작성하지 않고, POJO만으로 Web Application을 구축할 수 있다는 장점이 있음.
    - Servlet Class는 모두 추상화되어 라이브러리에 포함되어 있으며, 개발자는 간단하게 Servlet Class를 설정할 수 있다.

- Spring Framework의 특징 ? 
    1. Container
	2. IoC / DI      (Inversion of Control / Dependency Injection) 
	3. AOP           (Aspect Oriented Programming) 
	4. PSA           (Portable Service Abstraction) 

1. Container
    - 컨테이너는 특정 객체의 생성과 관리를 담당하며 객체 운영에 필요한 다양한 기능을 제공함. 
      Application 운영에 필요한 객체를 생성하고 객체 간 의존관계를 관리한다는 점에서 스프링도 일종의 컨테이너라고 볼 수 있다. 
    - Spring Framework에서는 BeanFactory와 이를 상속한 ApplicationContext 두 가지 유형의 컨테이너를 제공함.

    A. BeanFactory
        * 스프링 설정 파일(applicationContext.xml)에 등록된 bean 객체를 생성하고 관리하는 기능 제공.
        * 컨테이너가 구동될 때 객체를 생성하는 것이 아니라, 클라이언트로의 요청에 의해서만 객체를 생성함(Lazy Loading)  
        
    B. ApplicationContext
        * 컨테이너가 구동되는 시점에 bean에 등록되어 있는 클래스를 객체화 함. (Eager Loading)
        * 트랜잭션 관리나 메시지 기반의 다국어 처리 등 다양한 기능 지원.
    
    - bean에 등록된 모든 객체는 singleton으로 관리되고 있다.

    ![image](https://user-images.githubusercontent.com/12456375/41198125-0609a0c4-6cad-11e8-8ad7-789e6e882520.png)


2. IoC / DI (Inversion Of Control / Dependency Injection)
    - 비즈니스 로직을 개발할 때 신경써야 할 부분이 낮은 결합도와 높은 응집도이다.
      Spring Framework는 IoC란 방법을 통해 낮은 결합도를 유지한다. 

    - IoC란 “Inversion of Control”이란 뜻으로, 제어의 역전이란 말로 번역될 수 있다. 
        - IoC는 객체 생성을 자바 코드로 직접 처리하는 것이 아니라 Container가 대신 처리하게 한다. 
          그와 동시에 객체와 객체 사이의 의존관계 역시 Container가 처리한다.
          Spring Framework는 DI로 의존 관계를 설정한다.

    - DI란 “Dependency Injection”이란 뜻으로, 의존성 주입이란 말로 번역될 수 있다. 
        - 즉, 코드 상에서 사용자가 new 키워드로 객체를 생성하고 다른 객체와 관계를 설정하는 것이 아니라, 
          Spring Framework의 Container가 객체 간 의존관계를 설정한다.  

    - 의존관계는 XML을 통해 주입하거나 Annotaion을 통해 주입할 수 있다. 

    ![image](https://user-images.githubusercontent.com/12456375/41199369-a1fb60b2-6ccb-11e8-8ea8-608b01143781.png)


3. AOP (Aspect Of Programming)
    - DI가 객체 간 의존성을 주입한 거라면, AOP는 Logic 주입이라고 할 수 있다. 

    - AOP의 주 목적은 “다수의 모듈에 공통적으로 나타나는 부분을 제거하는 것”.
      반복적인 Logic을 공통 Logic으로 분리하면, 응집도를 높게 가져갈 수 있다. 
      AOP 또한 xml과 Annotation을 이용해서 설정할 수 있다. 


    * POJO를 사용한 기본 예제 

      ![image](https://user-images.githubusercontent.com/12456375/41199480-d30dc698-6ccd-11e8-9e02-b43bd145c765.png)

      ![image](https://user-images.githubusercontent.com/12456375/41199515-7dfbbede-6cce-11e8-92cd-bc05821ed70a.png)

    * AOP 구현 예제 

      ![image](https://user-images.githubusercontent.com/12456375/41199483-e3547164-6ccd-11e8-9b56-ebfc14a3eb37.png)

      ![image](https://user-images.githubusercontent.com/12456375/41199517-8084030a-6cce-11e8-9fb0-0fa774a87332.png)



4. PSA (Portable Service Abstraction) 
    - 직역하자면 일관성 있는 서비스 추상화이다.

    - JDBC처럼 Adapter 패턴을 적용하여 같은 일을 하는 다수의 기술을 공통 인터페이스로 제어할 수 있게 한 것을 서비스 추상화라고 한다. 

    - Spring Framework에서는 서비스 추상화를 위해 다양한 어댑터를 제공한다.

    - Spring Framework은 OXM, ORM, Cache, Transaction 등 다양한 기술에 대한 PSA. 즉 API 를 제공한다. 





- Spring Framework 내부 동작 원리

    ![image](https://user-images.githubusercontent.com/12456375/41199491-121d7478-6cce-11e8-82ae-fb211f838881.png)

    1. Web Application 실행  Tomcat(WAS)에 의해 web.xml이 loading됨.
    2. web.xml에 등록되어 있는 ContextLoaderListener(ServletContextListener 구현,  ApplicationContext생성 역할)가 생성됨. 
    3. 생성된 ContextLoader는 root-context.xml을 Loading.
    4. Root-context.xml에 등록되어 있는 Spring Container가 구동됨. 이때 DAO, VO 객체들이 생성됨.
    5. Client로부터 Request 요청이 온다.
    6. DispatcherServlet(Servlet)이 생성됨.  ( DispatcherServlet은 Client의 요청을 Controller로 분기함. – Handler-Mapping)
    7. DispatcherServlet은 servlet-context.xml을 Loading. ( DispatcherSevlet은 독립된 Context를 구성하면서 서로 참조불가.)
    8. 두번째 Spring Container가 구동되며 응답에 맞는 Controller들이 동작함. 이때 첫번째 Spring Container가 구동 시작됨.






  @ 포스팅 내용 참고 주소
  - http://asfirstalways.tistory.com/334
  - https://docs.spring.io/autorepo/docs/spring/2.0.8/reference/beans.html  


  @ 위 내용은 다른 개발자분들의 블로그를 참고하여 정리한 내용입니다. 
  혹시나 원본 정보의 출처를 누락되었거나 제 블로그에 게시되길 원치 않으시면 아래 메일로 연락 부탁드리겠습니다.
  littleprince0717@gmail.com 