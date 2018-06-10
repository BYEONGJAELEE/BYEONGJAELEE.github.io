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
    - Container
	- IoC / DI      ( Inversion of Control / Dependency Injection ) 
	- AOP            ( Aspect Oriented Programming ) 
	- PSA             ( Portable Service Abstraction ) 

- Container
    - Spring Framework에서는 BeanFactory와 이를 상속한 ApplicationContext 두 가지 유형의 컨테이너를 제공함.
    1. BeanFactory
        - 스프링 설정 파일(applicationContext.xml)에 등록된 bean 객체를 생성하고 관리하는 기능 제공.
        - 컨테이너가 구동될 때 객체를 생성하는 것이 아니라, 클라이언트로의 요청에 의해서만 객체를 생성함(Lazy Loading)
    2. ApplicationContext
        - 컨테이너가 구동되는 시점에 bean에 등록되어 있는 클래스를 객체화 함. ( Eager Loading)
        - 트랜잭션 관리나 메시지 기반의 다국어 처리 등 다양한 기능 지원.
    - bean에 등록된 모든 객체는 singleton으로 관리되고 있다.


        ![image](https://user-images.githubusercontent.com/12456375/41198125-0609a0c4-6cad-11e8-8ad7-789e6e882520.png)