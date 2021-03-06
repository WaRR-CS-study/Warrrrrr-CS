# Chapter6

> 웹 서버가 응답하는 과정



1. **패킷을 송, 수신하는 동작에서 서버와 클라이언트의 차이가 있나요?**

클라이언트와 서버의 네트워크 요소들의 기능은 동일합니다. 하지만 클라이언트는 요청을 보내고, 서버는 응답하는 구조상 소켓의 접속 동작에서 차이가 발생합니다. 

클라이언트는 소켓 접속 동작을하고, 서버는 접속대기 역할을 합니다. 따라서 Socket 라이브러리를 호출하는 과정에서 서버는 클라이언트와 다르게 추가적으로 접속 대기상태로 만드는 bind 함수와, 접속을 접수하는 accept 함수를 사용한다는 차이점이 있습니다.



2. **서버에 패킷이 도착하면 LAN어댑터가 신호 수신하고 CPU에 수신을 알리는데, 이때 사용하는 구조를 무엇이라고 하나요?**

인터럽트라는 방법을 사용하여 CPU에게 패킷의 도착을 알립니다.  CPU는 패킷의 수신처리를 자동적으로 하지 못하기 때문에 인터럽트를 통해 CPU에 패킷도착을 알려야 프로토콜 스택에 패킷을 넘겨줄 수 있습니다.



3. **웹 서버는 여러 클라이언트에서 액세스를 동시에 병행하여 처리하는데, 이때 이용하는 OS의 기능은 무엇입니까?**

멀티쓰레드와 멀티태스크 기능입니다.

멀티태스크 : 복수의 프로그램을 동시에 실행하는 기능

멀티쓰레드 : 복수의 쓰레드를 동시에 실행하는 기능



4. **웹 서버에 대한 액세스를 제어할 때 판단 기준은 무엇입니까?**
   - 클라이언트의 IP주소
   - 클라이언트의 도메인
   - 사용자명과 패스워드



5. **클라이언트 측에서 응답데이터의 종류를 어떻게 판단할 수 있나요?**

Content-type이라는 응답헤더필더를 통해 응답데이터의 종류를 판단합니다.

또는 응답데이터의 확장자와 포맷을 종합적으로 판단하는 방법도 있습니다.