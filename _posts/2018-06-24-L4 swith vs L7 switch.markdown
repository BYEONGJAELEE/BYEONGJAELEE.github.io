---
layout: post
title:  "[Network] L4 swith vs L7 switch"
date:   2018-06-24 17:47:25 +0900
categories: Network 
---

### 개요
  - 이번 문서에서는 L4 switch와 L7 switch의 차이점에 대해서 정리하려고 한다. 



### LAYER란 ?

* 여기서 말하는 Layer는 OSI 7 Layer에서 사용하는 개념이다. 



### OSI 7 LAYER 이란 ?

* OSI(Open System Interconnection Reference Model – 국제 표준화 기구)에서 개발한 모델로 컴퓨터 네트워크 소프트웨어의 개발을 돕기 위해서 7계층으로 나누어 정의한 명세서다. 

  ![image](https://user-images.githubusercontent.com/12456375/41821150-5977b8ac-7817-11e8-94a6-4c5df3612ed3.png)



### Switch란 ? 

* 네트워크 스위치는 네트워크를 연결하는 일을 하는 특수목적의 컴퓨터기기다.

* Switch(스위치)는 자신에게 연결된 모든 기기들로 부터 들어오는 패킷을 수신하고, 이 패킷을 적당한 기기로 보내는 일을 한다.

* 스위치보다 단순한 네트워크 장비로 **허브(hub)**가 있다. 

  * 허브도 패킷을 수신하고 송신한다는 관점에서 보면 스위치와 근본적으로는 차이가 없다.
  * 단 허브는 패킷을 “스위칭” 하지는 않고, 그냥 주변의 모든 기기에게 패킷을 복제해서 전송한다. 비효율적인데다가 보안에도 문제가 있지만 매우 싸기 때문에, 여전히 사용하고 있다.

* 스위치는 OSI 7 계층에서, 어느 계층까지를 다루는지를 기준으로 스위치의 카테고리를 나눈다. 

  * 예컨데 2 계층을 다룬다면 L2 스위치, 4 계층을 다루면 L4 스위치, 7 계층을 다루면 L7 스위치 이런식이다. 일반적으로 높은 계층을 다루는 스위치일 수록 가격역시 더 비싸다(기능도 많아진다).

  ![image](https://user-images.githubusercontent.com/12456375/41821174-99c5b3aa-7817-11e8-9f91-70b5d1861f0e.png)



* L2 : OSI Layer 2(Data Link)에 속하는 MAC Address 를 참조하여 Switching 하는 장비
* L3 : OSI Layer 3(Network)에 속하는 IP Address를 참조하여 Switching 하는 장비



그럼 이제 L4 Switch 장비와 L7 Switch 장비에 대해 자세하게 이야기 해보자. 



### L4 SWITCH란?

* OSI Layer 3~4(Transport)에 속하는 IP Address , TCP/UDP 포트 정보를 참조하여 패킷을 확인하고 세션을 관리하며, 로드밸런싱을 제공하는 스위치 장비.
* L4는 TCP/UDP 패킷 정보를 분석해서 해당 패킷이 사용하는 서비스 종류별로 처리(HTTP, FTP, SMTP…)한다.
* 세션관리, 서버/방화벽 로드밸런싱, 네트워크 서비스 품질 보장
* L4 switch 장비는 서버 IP를 VIP(Virtual IP)와 매핑한 Table을 갖고 있다. 따라서 L4 switch 장비는 VIP로 들어오는 트래픽을 여러 서버에 분산시킬 수 있는 로드 밸런싱 기능이 있다.



### L7 SWITCH란?

* OSI Layer 3~7(Session, Presentation, Application)에 속하는 IP Address, TCP/UDP 포트 및 Packet 내용까지 참조하여 Switching 하는 장비.

- L7은 L4 스위치의 서비스 단위 로드밸런싱을 극복하기 위해 포트 + 데이터 페이로드 패턴을 이용하여 패킷 스위칭한다.
- L7 스위치는 URL 기반 스위치다.
- L7 스위치는 모든 TCP/UDP 포트(0-65535)에 대한 인지가 가능하다.
- L7는 HTTP의 URL, FTP 쿠키 정보 및 바이러스 패턴을 분석하여 보안에 더 유리하고 정교한 로드밸런싱이 가능하다. (Traffic filter, Security, VPN 가능)



### L4 VS L7 ?

* 공통점
  * 스위치로 들어온 패킷을 적절한 목적지로 전송해 줌(불필요한 패킷은 drop 시킴)
* 차이점
  * 기능과 역할은 동일하나 패킷을 분석하는 intelligence가 다름, L7은 보안 기능 강화 (DDOS/SYN 공격 방어, CodeRed/Nimda 등 감염 패킷 필터링, 네트워크 자원 독점 방지 등..)
  * L4는 페이로드(뒤 데이터)에 대해서는 전혀 신경쓰지 않지만, L7은 세션을 분리하고 자신이 철저하게 클라이언트/서버로 동작한다.
  * L7은 보안스위치라고도 불려왔는데, 데이터 분석을 통해 DDoS 공격 방어, 감염 패킷 필터링 등의 기능을 제공한다. URL별로 스위칭이 가능하고, HTTP Header 값에 따른 특정 String을 기준으로 부하 분산이 가능하다.



  @ 포스팅 내용 참고 주소
  - http://www.escotal.com/osilayer.html
  - https://www.joinc.co.kr/w/man/12/switch
  - <http://kimseunghyun76.tistory.com/386>
- <http://sarc.io/index.php/forum/tips/553-l4-vs-l7>
- http://freeism.co.kr/tc/657




  @ 위 내용은 다른 개발자분들의 블로그를 참고하여 정리한 내용입니다. 
  혹시나 원본 정보의 출처를 누락되었거나 제 블로그에 게시되길 원치 않으시면 아래 메일로 연락 부탁드리겠습니다.
  littleprince0717@gmail.com 