---
layout: post
title:  "[Linux] Linker & Loader"
date:   2018-06-10 17:47:25 +0900
categories: linux
---

### 개요
  - 이번 문서에서는 ELF포맷을 기준으로 Linker와 Loader의 역할에 대해 알아보고자 한다.
  - 그 다음엔 Linker와 Loader 과정을 통해서 만들 수 있는 Library의 종류의 장단점에 대해 알아보고자 한다.



### GCC Compilation Process

1. “test.c”, "test2.c" file  처리
  - C 전처리기(cpp)를 통해 “test.i“(중간 파일)을 만든다.
2. “test.i”. "test2.i" file 처리
  - C 컴파일러(cc1)를 통해 “test.s”(어셈블리 파일)를 만든다.
3. “test.s”, "test2.s" file 처리 
  - assemlber(as)를 통해 “test.o”(오브젝트 파일)를 만든다. 
4. “test.o”, "test2.o" file 처리
  - linker(collect2)는 printf 등 외부 library에 있는 symbol을 링크해서 최종 프로그램 생성한다.(test.exe, test.out )
  ````
  	ld other-command-line-options test.o test2.o -o test.out
  ````
  * collect2는 gcc가 호출하는 linker(ld)의 wrapper다. 
5. linker에 의해 생성된 최종 프로그램(test.exe, test.out..)은 메모리에 로딩할 수 있다. 
  ````
  	./test.out
  ````
6. shell은 loader 함수를 수행하고, test.out에 포함된 code와 data 정보들을 메모리에 로딩한다.
  메모리 로딩이 완료되면 program을 시작할 수 있도록 준비한다.

![image](https://user-images.githubusercontent.com/12456375/41199973-026fd576-6cd7-11e8-9c1c-30e20038e0c8.png)

  * gcc는 위 과정을 간편하게 해주는 wrapper다. 
    위 과정은 아래 명령어 한줄이면 수행된다.
    ````
      gcc test.c test2.c
    ````


### Linkers vs Loaders

  - Linker와 Loader는 비슷한 작업을 수행하지만, 개념적으로 다른 작업을 수행합니다.




##### Linking이란 ? 

- 다양한 code와 data 조각들을 하나의 오브젝트(메모리에 로드되어 실행될 수 있는 양식)로 결합되는 과정이다. [메모리에 로드될 수 있는 오브젝트 종류는 아래 참조]
- 하나의 오브젝트로 결합하면서 아래 2가지 작업이 일어나게 된다. (자세한 내용은 Detail Linking)
    1. Relocation (재배치) 
      - 재배치는 같은 타입의 모든 section을 하나의 단일 section으로 통합함으로서 프로그램의 각 파트에 메모리 로딩 시 사용되는 주소를 할당하는 작업이다.
    2. Symbol Resolution
      - 다른 프로그램의 함수 주소를 모르지만 호출할 수 있도록 Symbol을 해석하는 작업이다. 그 결과 caller는 다른 프로그램의 함수 주소를 모르더지만, 링커덕분에 호출할 수 있게 된다. 
  - Linking은 compile-time, memory-load-time, run-time에 수행될 수 있다. 
    - memory-load-time엔 dynamic loader에 의해서 수행된다.
    - run-time엔 application program에 의해서 수행된다.



##### Loader란 ? 

  - 쉽게 말해서 Loader는 "디스크에 저장된 파일을 메모리에 로딩하는 과정"을 수행하는 프로그램이다.
  - 정확하게는, 메모리에 로드될 수 있는 포맷의 code와 data들을 메모리에 로딩하고, program의 시작 주소로 이동하는 프로그램이다. 
  - loader는 'execve' 함수로 호출되는 프로그램이다. 
    - execve는 다음 링크 참조 ( http://man7.org/linux/man-pages/man2/execve.2.html)




##### [Detail] Linking

- Linking 과정에서 아래 2가지의 작업이 수행된다.
    1. Symbol Resolution (Symbol 해석)
      * 먼저 Linker(ld)는 입력된 Object File을 검색하면서 아래 3가지 Group으로 재분류하는 과정을 반복한다. (초기에 모든 그룹은 비어있다.) 
        *O-group
            - 재배치 가능한 오브젝트 파일
        * U -group
          - 해석되지 않은 심볼
             - 아카이브 파일의 U-gruop이 비어있지 않는다면 에러가 발생한다. 
        * D-group
          - 입력 모듈에서 정의된 심볼
    2. Symbol Relocation
        - Symbol Resolution이 모두 끝나게 되면, 각각의 심볼들은 오직 하나의 심볼 정의만 갖게 된다.
        - Symbol Relocation 작업은 다음 두 단계의 작업을 진행한다.
            1. “.section” & “Symbol Definition” Relocation
            2. Allocate Load-address for each Symbol
        - Asselber가 unresolved symbol을 만나면, 해당 오브젝트에 relocation entry를 생성하고, 이를 .relo.text / .relo.data 섹션에 저장한다.
        - relocation entry에는 해당 심볼을 어떻게 참조할 것인지에 대한 정보를 담고 있다. 
        - 전형적인 ELF relocation entry에는 다음과 같은 정보들로 구성되어 있다. 
            - Offset : 재배치 해야할 심볼 참조의 section offset. 재배치 가능한 파일에서 보면 이 값은 섹션의 시작부터 심볼 참조 위치까지의 byte offset 값이다.
            - Symbol : 변경된 심볼 참조가 가리켜야할 심볼. 각각 재배치가 될 심볼 테이블의 인덱스라고 보면 된다.
            - Type : 재배치 타입, 정상적인 경우 프로그램 카운터(PC) 상대 주소 방식(PC-relative addressing)을 의미하는 "R_386_PC32"이다.
                - "R_386_32"는 절대 주소 방식(absolute addressing)을 의미한다.

    - 링커는 재배치 가능한 오브젝트 모듈에 있는 모든 relocation entry를 조사해서 unresolved symbol을 type에 따라 재배치하게 된다.
    - 가령 심볼이 "R_386_PC32" 타입인 경우, 재배치 주소는 S+A-P로 계산이 이루어지고 "R_386_32" 타입인 경우 S+A 식으로 계산이 된다. 여기서 'S'는 relocation entry의 심볼 값, 'P'는 section offset이나 재배치될 주소값(relocation entry에 저장된 offset값을 사용해 계산한다.), 'A'는 relocatable field의 값을 계산할 때 필요한 주소를 의미한다.

    - 2번 단계가 마무리되면, 프로그램 내에 모든 instruction과 전역 변수들은 고유한 load-time address를 갖는다. 




##### [Detail] Linking 과정에서 생성될 수 있는 Object Type
- Linker는 다음 3가지 Type으로 Object를 생성할 수 있다. 
  1. Relocatable Object file 
    - Compile-time에 다른 재배치 가능한 오브젝트 파일과 통합할 수 있어 하나의 실행파일이 생성될 수 있는 형태의 바이너리 코드와 데이터를 포함한다. 
  2. Executable Object file 
    - 메모리에 직접 로등되어 실행될 수 있는 형태의 바이너리 코드와 데이터를 포함한다.
  3. Shared Object file 
    - Load-time이나 run-time에 동적으로 메모리에 로드되고, 링크될 수 있는 특수한 타입의 재배치 가능한 오브젝트 파일. 


##### Executable Object file  vs Shared Object file 
1. Executable Object file (Static Library)
  - 아카이브형태로 만들어지는 형태로 compile-time에 심볼들의 Runtime-Address가 결정된다.
  - Linking 시 사용되는 모든 파일 및 라이브러리들은 하나의 Object로 만든다.

![image](https://user-images.githubusercontent.com/12456375/41200512-b9885a9a-6ce0-11e8-9605-1928cf6d33d3.png)
````
	* 장점
		* 로딩 시간 감소
		* 단독 Application 제작 가능
	* 단점
		* 코드 사이즈 증가
		* 메모리 사용량 증가
		* 유지보수가 어려움 (업데이트마다 재컴파일 및 배포 필요)
````



2. Shared Object file (Shared Library)
  - 공유 라이브러리는 run-time 시 언제나 임의의 메모리 주소에 로딩할 수 있는 object module이다.
  - 이미 메모리에서 실행중인 프로그램에 의해서 링크될 수도 있다. 
  - 공유 라이브러리의 링킹과정은 크게 2단계로 나뉜다.
    * 부분적 링킹 ( on disk )
      - disk에서 링킹 시 External symbol의 주소는 알 수 없기 때문에, external symbol의 주소값은 메모리 로딩 시 할당받도록 링킹하는 방법이다.
    * 완전한 링킹 ( on memory )
      - Library 메모리 로딩 시 Dynamic Linker에 의해서 불확실했던 주소값이 정해진다.
      - 실행파일이 메모리에 로딩될 때 Loader는 Dynamic linker에 실행 제어를 넘긴다. 
      - Dynamic Linker는 공유 라이브러리를 프로그램의 주소 영역에 매핑시키는 start-up 코드를 포함하고 있다. 
      - 동적 링커(Dynamic Linker)의 작업
         - so파일의 텍스트와 데이타를 실행파일의 memory segment로 재배치한다.
         - so파일에 정의된 심볼을 참조하는 "test.out"내의 모든 심볼 참조를 재배치한다.
  - 위 두가지 작업이 끝나면 동적 링커는 어플리케이션에 실행 제어를 넘긴다.
    이때부터 공유 오브젝트의 위치가 메모리에 고정된다. 

![image](https://user-images.githubusercontent.com/12456375/41200613-f22f6dfa-6ce2-11e8-8ac4-88c73a5ef8d3.png)



````
	* 장점
		* 코드 사이즈 감소
		* 메모리 사용량 감소
		* 코드 모듈화 및 유지보수가 용이함
	* 단점
		* 로딩 시간 증가
		* 다른 라이브러리 없이 Application 단독 운영 불가
		* 라이브러리 간 의존성이 높아 충돌 가능성 높음
		* 유지보수가 어려움 (업데이트마다 재컴파일 및 배포 필요)
````




  @ 포스팅 내용 참고 주소
  - https://www3.ntu.edu.sg/home/ehchua/programming/cpp/gcc_make.html
  - https://www.linuxjournal.com/article/6463


  @ 위 내용은 다른 개발자분들의 블로그를 참고하여 정리한 내용입니다. 
  혹시나 원본 정보의 출처를 누락되었거나 제 블로그에 게시되길 원치 않으시면 아래 메일로 연락 부탁드리겠습니다.
  littleprince0717@gmail.com 