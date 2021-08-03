[toc]

영상: [컴퓨터시스템구조 CSA-5 Part-5](https://youtu.be/4CKJs6s2OGI?list=PLc8fQ-m7b1hD4jqccMlfQpWgDVdalXFbH)

## [제 5장 Part-5,6](https://youtu.be/4CKJs6s2OGI?list=PLc8fQ-m7b1hD4jqccMlfQpWgDVdalXFbH)

### [입출력과 인터럽트 (Input-Output and Interrupt)](컴퓨터-구조/5장-기본-컴퓨터의-구조와-설계-Part2/입출력과-인터럽트.md)

5장이 끝나면 CPU를 설계할 수 있다.

### 입출력 가능 여부

- 입출력 장치는 CPU에 비해서 속도가 느리다. 이를 고려하지 않고 입출력 명령을 남발하면 입출력을 제때 하지 못 할 수 있다. 따라서 CPU는 입출력 장치가 사용 가능한지 항상 체크해야 한다.
- 입출력 장치의 사용 가능 여부를 파악하기 위해 플래그를 사용한다.

![캡처1](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처1.PNG)

키보드의 어떤 키가 눌렸다 --> FGI가 1로 세팅 --> CPU는 Input Register(INPR)를 통해서 데이터를 인터페이스에서 받아들이게 되고, 그 값을 AC(accumulator)로 전송한다.

만약 CPU가 데이터를 보내려고 하는데, Printer가 사용 가능하다면 FGO가 1로 세팅되고, CPU는 Output Register(OUTR)로 AC의 데이터를 보내게 된다. OUTR은 Receiver interface로 데이터를 내보내고, 이를 Printer로 전달하여 글자를 찍게 됨.

CPU가 입출력을 할 건지 말 건지는 IEN(Interrupt ENable) flag로 판단한다. IEN flag가 1이면 입출력을 할 수 있는 상태이고, 위에서 설명한 과정은 IEN flag가 1인 상태에서만 가능하다.



### 입출력과 인터럽트의 관계

입출력이 발생하면 곧 인터럽트가 발생하는 것과 같다.

이유: 입출력이 발생하면 CPU가 하던 일을 중단시키고 데이터를 받아서 다시 외부로 보내는 과정이 필요하고, 이게 곧 인터럽트다.



### 입출력 명령어

>  **사전 지식**
>
>  참고: [운영체제를 배우기 전에..](https://velog.io/@minni/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%EB%A5%BC-%EB%B0%B0%EC%9A%B0%EA%B8%B0-%EC%A0%84%EC%97%90)
>
>  **Instruction Register의 구조**
>
>  ![캡처3](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처3.PNG)
>
>  - OpCode 부분의 세 비트의 값에 따라 D0부터 D7까지 이름을 붙여놓음
>  - D7은 OpCode의 세 비트가 모두 1인 경우로, 이때 Indirect-direct bit(I) 가 1이면 입출력, 0이면 레지스터 동작 명령어가 된다.
>
>  
>
>  **명령어 사이클**
>
>  ![캡처4](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처4.PNG)
>
>  T0, T1, T2는 명령어 해독 단계(fetch 사이클), Register Reference Instruction이나 input-output instruction은 T3 단계에서 수행된다.



![캡처2](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처2.PNG)

- 입출력 명령어를 구성하는 기계어 명령어의 12번부터 15번 비트는 모두 1이다. 
- 명령어 사이클의 T3에서 입출력 명령어가 수행된다. (Register Reference도 T3에서 수행)
- 따라서 입출력 명령어를 수행하는 상태는 D7 & I & T3 일 때이고, 이를 줄여서 p로 쓴다.
- 그 외 6번부터 11번까지의 비트의 어떤 부분이 1인지에 따라 어떤 명령어인지가 결정된다.
  - 11번 비트가 1이면 Input 명령 (FGI=1, INPR로 데이터를 가져와서 AC의 8개 비트에 전달 후 FGI를 0으로)
  - 10번 비트가 1이면 Output 명령 (FGO=1, AC의 8개 비트를 OUTR로 보내고 FGO를 0으로)
  - 9번 비트가 1이면, FGI가 1이면(가져올게 있으면) 현재 수행하는 명령의 다음 명령어를 건너 뜀
  - 8번 비트가 1이면, FGO가 1일 때 (내보낼게 있으면) 현재 수행하는 명령의 다음 명령어를 건너 뜀
  - 7번 비트가 1이면, 입출력을 실행하겠다는 뜻
  - 6번 비트가 1이면, CPU가 입출력을 하지 않음.



### 프로그램 인터럽트 과정

![캡처5](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처5.PNG)

- 메인 프로그램이 실행 중 255번 명령어에서 interrupt가 발생했다.
- 255번 명령어를 수행 중일 때 PC값은 256이다.
- 인터럽트가 끝나고 돌아올 주소는 따라서 256이다.
- 서브루틴의 경우에는 보통 서브루틴의 시작 주소에 복귀 주소를 넣어놓지만 인터럽트의 경우에는 메모리의 0번지에 복귀 주소를 저장한다.
- 저장 후에 PC는 1로 세팅되며, 메모리 1번지에 있는 명령어를 수행하게 된다.
  - 0 BUN 1120(0 = 직접주소, 1120 = 이동할 직접주소)
- BUN(Branch Unconditionally) 1120을 따르게 되면 1120번지로 점프해가게 되고, 여기에는 입출력 프로그램이 있다. 입출력 프로그램은 Basic Input/Output System(BIOS)이다.
- 입출력 프로그램이 수행된 다음에는 1 BUN 0이 실행된다. (BIOS 기본 세팅인듯?)
  - 1 BUN 0 (1 = 간접주소라는 뜻, 0 = 이동할 간접주소)
  - 간접주소인 0번지로 이동하면 다시 이동할 주소인 256이 있고, 따라서 복귀주소였던 256으로 복귀할 수 있다.



인터럽트와 함수 서브루틴 콜이 실행되는 과정은 보기에 비슷해 보인다. 하지만 한 가지, return address가 호출되는 함수의 시작번지에 들어가느냐(함수였다면 1120번에 return address가 저장되었을 것), 0번지에 들어가느냐 하는 차이점이 있다.



### 인터럽트가 걸렸는지 어떻게 아는가?

![캡처6](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처6.PNG)

- 명령어가 수행되는 사이클은 위와 같다.
- IEN이 1이면 FGI와 FGO를 확인한다.
- FGI나 FGO중 하나라도 1이면 R을 1로 세팅한다.
- T3나 T4 과정에서 Excecute instruction을 하고 나면 R값을 확인한다.
- R값이 1이면 입출력을 수행해야 하므로 바로 직전에 알아본 과정인, Store return address in location 0 -> ... 을 수행한다.
- I/O Program도 마찬가지로 위의 과정을 거치면서 명령을 수행한다.
  - 그런데 **기본 컴퓨터**의 경우, I/O 프로그램 수행 도중에 또 FGI나 FGO값이 1이 되면, 0번 주소에 새로운 PC값을 저장하게 되므로 이전에 저장되어 있던 복귀주소는 날아가버린다.
  - 이를 방지하기 위해 입출력 명령을 수행하기 전에 IEN = 0, R = 0으로 고정하여 입출력 명령 수행 도중에는 새로운 입출력 명령이 발생하지 않도록 한다.
  - 따라서 기본 컴퓨터의 경우 **한번에 하나의 입출력**만을 수행할 수 있다.
  - 하지만 **현대 컴퓨터**는 인터럽트 처리 루틴 시작번지를 테이블에 저장하여 **여러 개의 입출력 명령을 차례대로 수행**할 수 있다.(IVT - Interrupt Vector Table)

하나의 명령어가 수행될 때마다 위 과정을 반복하므로 프로그램 인터럽트를 새로운 명령어 수행시마다 확인 할 수 있다.



## [제 5장 Part-7](https://youtu.be/693iK8vB7XU?list=PLc8fQ-m7b1hD4jqccMlfQpWgDVdalXFbH)

### 컴퓨터에 대한 완전한 기술 (Compute Computer Description)

![캡처7](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처7.PNG)

- 위 사진은 지금까지 다룬 명령어를 정리한 것
- CPU는 기억장치, 연산장치, 제어장치로 구성됨

- 명령어가 들어왔을 때 명령대로 움직이도록 하는 제어장치를 만들어야 한다. => 컴퓨터의 설계는 곧 이러한 제어장치를 설계하는 것을 의미한다.

## [제 5장 Part-8](https://youtu.be/pUrzmOqsV58?list=PLc8fQ-m7b1hD4jqccMlfQpWgDVdalXFbH)

### 기본 컴퓨터의 설계 (Design of Basic Computer)

### 1. 하드웨어 구성요소

참고: [정상에 서자](http://dreamform.egloos.com/v/2805176)

- 16비트 4096워드 메모리: 메인 메모리
- 9개의 레지스터(양방향 shift register)
  - AR: 메모리 주소 저장, 12비트
  - PC(Program Counter): 다음 수행할 명령어가 들어 있는 메모리의 주소를 저장, 12비트
  - DR(Data Register): 메모리에서 읽어온 피연산자 저장, 피연산자 저장이므로 16비트 
  - AC(accumulator): 연산 처리 후 나온 결과값을 일시적으로 기억, 16비트 범용 레지스터
  - IR(instruction register): 명령어 저장, 12bit(주소) + 3bit(OpCode) + 1bit(간접 or 직접주소)
  - TR(Temporal register): 임시 데이터 저장, 피연산자를 저장하므로 16비트
  - OUTR(출력레지스터): 8비트 문자 정보 출력
  - INPR(입력레지스터): 8비트 문자 정보 입력
  - SC(sequence counter): 연산 장치에서 곱셈, 나눗셈, 자리 이동 조작의 단계를 헤아릴 때 사용
- 7개의 플립플롭
  - I: indirect-direct 주소를 표시
  - S: 양수냐 음수냐
  - E: 계산 결과 올림수 또는 오버플로우가 나오면 기록
  - R: 인터럽트 발생 여부를 기록
  - IEN: 인터럽트를 받을건지 말건지 결정
  - FGI: 입력 가능 여부
  - FGO: 출력 가능 여부
- 2개의 디코더
  - 3 x 8 (Opcode), 4 x 16(타이밍, TC)
- 16bit 공통버스
  - 출력 16개가 겹쳐있고, 입력은 8개가 있다. 입력 8개는 AR, PC, DR, AC, IR, TR, INPR 와 메모리값
  -  AC 입력 연결 논리회로 (ALU)
  - 제어 논리 게이트(제어장치)

컴퓨터의 설계는 곧 제어장치를 설계하는 것이라고 했다. 우리가 설계할 것은 **제어 논리 게이트**이다.

### 2. 제어 논리 게이트 설계

제어 논리 게이트를 설계하는 것은 곧 앞서 언급한 메모리, 레지스터, 플립플롭, 디코더, 공통버스 등이 어떤 상태에서 어떻게 엮여있고, 어떤 결과로 이어지는지 표현하는 그림을 그리는 것과 같다.

![캡처8](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처8.PNG)

위 그림은 AR 레지스터의 제어 논리 게이트의 예로, 짧게 설명해보자면,

- LD(Load)
  - bus로부터 AR에 어떤 값이 load되는 상황은 아래의 세 가지 경우이다. (OR로 묶여있음)
  - R'T0: AR <= PC
    - R값이 0이고 T0일 때
    - PC를 AR에 load
  - R'T2: AR <= IR(0-11)
    - R값이 0이고 T2일 때
    - IR을 AR에 load
  - D7'IT3: AR <= M[AR]
    - D7이 0이고(Opcode가 111이 아니고) I는 1, T3 일 때
    - M[AR]을 AR에 load
- INR(Increment)
  - D5T4: AR <= AR+1
    - D5(Opcode가 101)이고 T4일 때
    - AR 값을 1 증가
- CLR
  - RT0: AR <= 0
    - R값이 1이고 T0일 때
    - AR clear (데이터 삭제)

정리: AR 레지스터가 동작하는 경우는 Load, Increment, Clear 의 세 가지 경우밖에 없는데, 어떤 경우에 Load하고, 어떤 경우에 increment하고, 어떤 경우에 clear 하는지를 모아서 AND, OR, NOT 게이트로 묶어주면 제어 논리 게이트(하드웨어)를 설계할 수 있다. 이렇게 다른 종류의 레지스터도 [컴퓨터에 대한 완전한 기술] 부분에서 보여주고 있는 기본 컴퓨터의 전체 명령어 set에서 해당하는 레지스터가 포함된 조건을 찾아서 논리 연산자로 잘 묶어주면 제어 논리 게이트를 설계할 수 있다.

예를 들어 DR 레지스터는 Load하는 조건은 네 가지 경우(D0T4, D1T4, D2T4, D6T4 <= OR 연산자), Increment하는 조건은 D6T5이다. DR을 clear 하는 경우는 없다.

Read = R'T1 + D7'IT3 + (D0 + D1 + D2 + D6)T4 도 마찬가지의 의미이다. M[AR]을 어딘가에 화살표 표시로 전달하는(ex AR <= M[AR]) 동작을 모아서 OR로 묶은 것을 수식으로 나타낸 것이다.



플립플롭에 대한 제어 논리 게이트 설계도 마찬가지이다.

![캡처9](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처9.PNG)

IEN의 제어 게이트는 기본 컴퓨터 전체 명령어 set을 보면 pB7, pB6, RT2의 세 가지 경우(그림은 pB2로 잘못 표기)이고,  IEN이 1이면 J값이 1이고 K가 0이고, IEN값이 0이면 J가 0이고 K가 1이거나 IEN값이 1일 때 J, K값이 1이 돼서 IEN값이 바뀌는 경우(??)라서 그림을 위와 같이 그린다고 한다.



공통 버스에 대한 제어 논리 게이트 설계 역시 비슷하다.

![캡처10](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처10.PNG)

AR, PC, DR, AC, IR, TR, Memory의 값을 버스에 태워 보내는(PC<=AR 등 레지스터 출력을 전달하는 경우) 경우를 순서대로 x1, x2, x3, x4, x5, x6, x7이라고 하고, 인코더는 이러한 출력값들을 인풋으로 받아 OR로 묶어준 후 S0, S1, S2의 출력으로 표현한다. 아마 너무 복잡해서 더 보여주지는 않은 것 같은데 버스 역시 어떤 레지스터의 값이 어떤 조건에서 Encoder의 인풋이 되는지 찾은 다음 S0, S1, S2에 해당하는 것 끼리 묶어주면 된다는 것을 알 수 있다.



### 3. 누산기 논리의 설계 (Design of Accumulator Logic)

![캡처11](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처11.PNG)

Accumulator는 Load에 해당하는 조건이 위에서부터 7가지, Clear가 그 다음의 한 가지, Increment가 그 다음의 한 가지가 있다. (r = D7I'T3) 이 조건들은 Control gates에 들어가 있다고 보면 되고, Adder and logic circuit 부분은 ALU(Arithmetic Logic Unit, 8장에서 다룸)라고 보면 되어서 추가적인 산술연산, 논리연산 및 시프트를 조건에 더해주는 역할을 한다.

![캡처12](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처12.PNG)

따라서 control gates는 위와 같이 설계할 수 있다.



Adder and logic circuit은 아래와 같이 설계한다.

![캡처13](C:\Users\multicampus\Desktop\Warrrrrr-CS\4. 컴퓨터구조\chapter05\유성호_Part2\5장-기본-컴퓨터의-구조와-설계-Part2.assets\캡처13.PNG)

이러한 구조 하나가 Accumulator register의 하나의 비트를 구성하며, 따라서 Accumulator register의 경우 16개의 adder and logic circuit이 필요하다.

FA: Full adder, 덧셈에 사용



### 수행 과제

- 5장의 연습문제 풀이(3, 8, 13, 18, ... or 2, 7, 12, 17, ...)
- "기본 컴퓨터의 설계"의 모든 설계사항을 Logisim으로 구현하라.



