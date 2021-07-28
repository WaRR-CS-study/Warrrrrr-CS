영상: [컴퓨터시스템구조 CSA-5 Part-5](https://youtu.be/4CKJs6s2OGI?list=PLc8fQ-m7b1hD4jqccMlfQpWgDVdalXFbH)

## [제 5장 Part-5,6](https://youtu.be/4CKJs6s2OGI?list=PLc8fQ-m7b1hD4jqccMlfQpWgDVdalXFbH)

### [입출력과 인터럽트 (Input-Output and Interrupt)](컴퓨터-구조/5장-기본-컴퓨터의-구조와-설계-Part2/입출력과-인터럽트.md)

### 입출력 구성

- CPU와 IO 장치의 속도 차이 제어를 위하여 Flag 사용 : IO 장치가 CPU에 비해 현저히 느림
  - Buffer overrun
  - Buffer underrun
- FGI
  - 1 : 입력 가능한 상태
  - 0 : 입력 블러킹
- FGO
  - 1 : 출력 가능한 상태
  - 0 : 출력장치 사용중
- 인터럽트
  - 인터럽트란 곧 입출력이다 -> 입출력 발생 시 인터럽트 유발
  - IEN flag (Interrupt Enable flag) : 입출력을 받을지 결정하는 장치, 1인 경우 입출력 가능 
  - IEN flag에 의하여 제어 
  - 입출력 전체를 제어

<img src="5장-기본-컴퓨터의-구조와-설계-Part2.assets/image-20210727233335228.png" alt="image-20210727233335228" style="zoom:80%;" />

- 어느 비트가 1인지에 따라 어떤 I/O인지 알 수 있음

<img src="5장-기본-컴퓨터의-구조와-설계-Part2.assets/image-20210727234047480.png" alt="image-20210727234047480" style="zoom:80%;" />



### 인터럽트의 발생과 처리 과정

- 255번 명령이 실행될 때 인터럽트 발생, PC값은 256
- 인터럽트 발생 시 메모리의 0번지에 해당 PC값 저장
- 이후에 Unconditional Branch 1120번으로 간다
- 1120번지에는 I/O(입출력 프로그램)가 있다
- I/O 프로그램수행 후 간접번지인 0번지로 간다 
- 인터럽트 종료 후 프로그램의 원래 번지로 돌아옴

![image-20210728085452443](5장-기본-컴퓨터의-구조와-설계-Part2.assets/image-20210728085452443.png)



### 명령어수행 시 입출력 발생한다면

1) Fetch and decode (명령어 수행 과정)
2-1)  FGI == 1 or FGO == 1일 경우 R에 표시 ➡ 위의 인터럽트 처리과정 발생

   - R : IEN 이외에 입출력 필요 여부를 표시
   - 한 번에 하나의 인터럽트만 처리할 수 있으므로 여러 개의 인터럽트가 발생해도 한 사이클을 돌고나서 처리한다

2-2) FGI == 0 and FGO == 0일 경우 ➡ 명령 실행



<img src="5장-기본-컴퓨터의-구조와-설계-Part2.assets/image-20210728085512467.png" alt="image-20210728085512467" style="zoom:80%;" />

### 관련 장치

- 프로그램 인터럽트 
  - 장치 준비 시 CPU에게 알림
  - 인터럽트 발생시 BSA명령어처럼 동작
  - FGI, FGO 플래그 사용
    - 플래그가 set되면 R <- 1
    - R = 1이면 다음 명령어 사이클에 인터럽트 사이클 실행
  - IEN
    - 인터럽트 enable/disable 제어
- I/O 프로그램
  - 입출력 인터럽트 처리 루틴의 집합
- IVT (Interrupt Vector Table)
  - 각 인터럽트에 벡터 번호 부여
  - 벡터번호와 인터럽트 처리 루틴 시작번지를 Table로 유지
  - 시스템 부팅 시에는 IVT는 0번 segment에 load
  - 현대의 대부분의 CPU가 IVT 사용



### 인터럽트 사이클

- IC로 분기되는 조건 (Interrupt cycle)

## [제 5장 Part-7](https://youtu.be/693iK8vB7XU?list=PLc8fQ-m7b1hD4jqccMlfQpWgDVdalXFbH)

### 컴퓨터에 대한 완전한 기술 (Computer Computer Description)

- 기본 컴퓨터의 전체 명령어 

  - CPU는 기억장치, 연산장치, 제어장치로 구성 
  - 컴퓨터 설계는 제어장치를 설계하는 것을 의미

  ![image-20210728194439047](5장-기본-컴퓨터의-구조와-설계-Part2.assets/image-20210728194439047.png)
  



### 하드웨어 구성 요소

- 16bit 4096 워드 메모리 (메인 메모리)
- 9개의 레지스터 (범용 양방향 쉬프트 레지스터)
  - AR : 메모리 주소 저장 (12비트)
  - PC : 12비트 주소 레지스터
  - DR : 16비트에 피연산자 저장
  - AC : 16비트에 연산 결과값 저장
  - IR 
  - TR : Temporary Register
  - OUTR : OutRegister
  - INPR : Input Register
  - SC 
- 7개의 플립플롭
  - I, S, E, R, IEN, FGI, FGO
- 2개의 디코더
  - 3 x 8 (Opcode), 4 x 16(타이밍)
- 16bit 공통버스 : 8개의 입력은 위의 메모리와 레지스터를 지정하는 내용 
- 제어 논리 게이트
- AC 입력 연결 논리회로 (ALU)



### 레지스터와 메모리에 대한 제어

![image-20210728200433102](5장-기본-컴퓨터의-구조와-설계-Part2.assets/image-20210728200433102.png)

### 

-