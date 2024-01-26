# Chapter 3. 컴퓨터 시스템의 동작 원리
- 커널 : 운영체제 상에서 필요한 메모리를 그때 그때 올려서 사용하는데 커널은 그냥 항상 올려져 있는 부분이다.
    좁은 의미의 운영체제이면서 핵심적인 부분
- 인터럽트 : cpu에게 자기한테 우선적으로 메모리할당해 달라고 신호를 보내는것.
    - cpu의 인터럽트 라인에 신호가 들어오면 인터럽트를 우선적으로 처리한다.
    - cpu가 명령 하나를 수행할 때마다 인터럽트가 발생했는지 확인한다.
    - 통상 인터럽트는 `하드웨어 인터럽트`를 말하고 소프트웨어 인터럽트는 `trap`이라는 용어로 주로 불린다.
    - 소프트웨어 인터럽트의 예) `exception`, `system call`
    - `system call` : 사용자 프로그램이 운영체제 내부에 정의된 코드를 실행하고 싶을 때 운영체제에 서비스를 요청하는 방법
- 인터럽트 벡터 : 인터럽트 종류마다 번호를 정해서, 번호에 따라 처리해야 할 코드가 위치한 부분을 가리키고 있는 자료구조
- 인터럽트 핸들링 : 인터럽트가 발생한 경우에 처리해야 할 일의 절차
> 1. 프로그램 A가 실행 중
> 2. 인터럽트 발생
> 3. A의 현재 상태를 PCB에 저장 : 실행 중인 메모리 주소, 레지스터값, 하드웨어 상태 등
> 4. 인터럽트 처리
> 5. PCB로부터 저장된 상태를 다시 복원해 나머지 실행을 이어간다.

- PCB(**P**rocess **C**ontrol **B**lock) : 운영체제가 현재 시스템 내에서 실행되는 프로그램들을 관리하기 위해 두는 자료구조.
    - 각각의 프로그램마다 하나씩 존재한다.
    - 해당 프로그램의 어느 부분을 실행 중이었는지를 저장하고 있다.

- 로컬버퍼 : 장치컨트롤러가 장치의 입출력데이터를 임시로 저장하기 위한 작은 메모리.
    - 로컬버퍼로 읽어온 후 인터럽트를 발생시킴.

- 동기식 입출력
    - 입출력 중인 프로그램에게는 cpu를 할당하지 않는다.
    - 아무런 명령도 수행할 수 없기 때문이다.
    - 그 프로그램은 `봉쇄상태(blocked state)`로 전환시킨다.
    - 동기식 입출력에서 입출력 요청의 동기화를 위해 장치별로 `큐`를 두어 순서대로 처리한다.
    - 입출력이 완료되었다는 인터럽트를 받으면 봉쇄상태를 해제한다.

- 비동기식 입출력
    - 입출력 중인 프로그램에게 곧바로 cpu를 할당한다.
    - 입출력과 무관한 작업부터 수행한다.
    - 입출력이 완료되면 인터럽트를 통해 알린다.
    - 이후 읽어온 데이터를 필요로 하는 명령을 수행할 수 있다.

- DMA
    - 원래 메모리에 접근 가능한 것은 cpu뿐이고 다른 프로그램들은 cpu에게 메모리접근을 대행시킨다.
    - 이는 cpu의 업무를 방해한다.
    - 때문에 cpu이외에 메모리 접근이 가능한 장치를 하나 더 둔다.
    > 이것을 DMA (**D**irect **M**emory **A**ccess)라고 한다.

    - 일종의 컨트롤러로서 cpu가 자주 인터럽트 당하는 것을 막아주는 역할
    > 큰 단위의 데이터를 입출력한다.
    > cpu에 발생하는 인터럽트의 빈도를 줄여 cpu를 좀 더 효율적으로 관리하고 입출력 연산을 빠르게 수행할 수 있게 한다.

- `스왑 영역`
    - 부족한 주기억장치의 메모리의 연장공간으로서 보조기억장치에 속한 영역이다.
    - 당장 필요하지 않은 부분을 디스크의 스왑영역에 내려놓는 것을 `스왑아웃`시킨다고 말한다.
    - 보조기억장치에 속하면서도 프로그램이 실행될 때 내용을 저장하고, 프로그램이 종료될 때 삭제하는 메모리의 연장공간.

- 주기억장치(primary) : 레지스터 > 캐시 메모리 > 메인 메모리
- 보조기억장치(secondary) : 마그네틱 디스크 > 광디스크 > 마그네틱 테이프

- 캐싱 기법 : 상대적으로 용량이 적은 빠른 저장장치를 이용해 느린 저장장치의 성능을 향상시키는 총체적 기법
    > 상대적으로 느린 저장장치에 있는 내용 중 당장 사용되거나 빈번히 사용될 정보를 빠른 저장장치에 선별적으로 저장함으로써 두 저장장치 사이의 속도를 완충시킨다.

- 특권명령 : 시스템의 보안과 관련된 명령들
> `커널모드`가 아닌 `사용자모드`에서는 시스템에 중요한 영향을 미치는 연산을 제외한 제한적인 명령만을 수행할 수 있다.
> 이는 하드웨어적으로 `모드 비트`를 두어 사용자 프로그램을 감시한다.

- 프로그램이 접근하려는 메모리 부분이 합법적인지 체크함으로써 메모리를 보호하기 위해 2개의 레지스터를 사용한다.
1. 기준 레지스터(base register) : 어떤 프로그램이 접근할 수 있는 메모리 주소의 최소값을 저장한다.
2. 한계 레지스터(limit register) : 그 프로그램이 기준 레지스터값으로부터 접근할 수 있는 메모리의 범위를 저장한다.
3. 이 범위 밖의 메모리에 접근하려고 하면 예외(소프트웨어 인터럽트)를 발생시킨다.

> 위는 한 프로그램이 한 영역에 연속적으로 위치하는 단순화된 메모리 관리 기법을 사용한 경우.
> 한 프로그램이 메모리의 여러 영역에 나뉘어 위치하는 페이징 기법은 따로 설명.

- 타이머 : cpu가 하나의 프로그램에 의해 독점되는 것을 막기위해 정해진 시간이 지나면 인터럽트를 발생시키는 하드웨어
> 시분할 시스템에서 현재 시간을 계산할 때도 사용한다.

---
---
# Chapter 4. 프로그램의 구조와 실행
- 프로그램의 주소 영역
1. 코드 : 작성한 프로그램 함수들의 코드가 cpu에서 수행할 수 있는 기계어 명령 형태로 변환되어 저장되는 부분
2. 데이터 : 전역 변수 등 프로그램이 사용하는 데이터를 저장하는 부분
3. 스택 영역 : 함수가 호출될 때 호출된 함수의 수행을 마치고 복귀할 주소 및 데이터를 임시로 저장하는 데에 사용되는 공간
- 각각의 프로그램마다 이러한 주소 공간을 별도로 가진다.
- 독자적으로 존재하는 이와 같은 주소 공간을 **가상메모리(virtual memory)** 또는 **논리적 메모리(logical memory)**라고 부른다.

- 프로세스 제어블록
    - 운영체제가 관리하는 공간
    - 인터럽트 때문에 cpu를 빼앗긴 위치가 저장된다.
    - 인터럽트 발생 시점에서 그 프로그램읭 어느 부분까지 수행했는지를 저장한다.

- 프로그램 카운터
    - cpu가 수행해야 할 메모리 주소를 담고 있는 레지스터이다.
    - 일반적으로는 항상 다음 명령을 가리키게 되어 코드의 순차적인 수행이 이루어진다.
    - 프로그램 카운터가 운영체제가 존재하는 메모리 주소를 가리킨다면 cpu가 `커널 모드`에서 수행 중이라고 말한다.
    - 프로그램 카운터가 사용자 프로그램이 존재하는 메모리 주소를 가리킨다면 cpu가 `사용자 모드`에서 수행 중이라고 말한다.

- 프로그램 실행 시, 당장 cpu의 수행에 필요한 부분은 메모리에 올려놓고 그렇지 않은 부분은 스왑 영역에 내려놓는 방식으로 운영된다.

- 커널
    - 데이터 영역
    > 각종 자원을 관리하기 위한 자료구조가 저장된다.
    > 하드웨어 자원을 관리하기 위한 자료구조뿐 아니라 현재 수행 중인 프로그램을 관리하기 위한 자료구조도 포함한다.
    
    - 스택 영역
    > 일반적인 용도인 함수호출 시의 복귀주소를 저장하기 위한 용도
    > 현재 수행 중인 프로세스마다 별도의 스택을 두어 관리한다.
    > 프로세스가 커널의 함수를 호출했는데 또 다른 함수가 호출되었을 때에는 그 복귀 주소가 커널 내의 주소가 되어 사용자 프로그램이 스택과는 별도의 저장공간이 필요하기 때문.
    > 커널은 모든 사용자 프로그램의 공유 코드이기 때문에 일관성 유지를 위해 각 프로세스마다 커널 내에 별도의 스택을 둔다.
    
- 프로그램이 사용하는 함수
    1. 사용자 정의 함수 : 프로그래머 본인이 직접 작성한 함수
    2. 라이브러리 함수 : 본인이 직접 작성하지 않앗지만 누군가 이미 작성해놓은 함수를 호출만 하여 사용하는 경우
    > 위 두 함수는 프로그램의 코드 영역에 기계어 명령 형태로 존재한다.
    > 위 두 함수는 프로그램이 실행될 때 프로세스의 주소 공간에 포함되며, 또한 함수호출 시에도 자신의 주소 공간에 있는 스택을 사용한다.

    3. 커널 함수
        1) 시스템 콜 함수
        2) 인터럽트 처리 함수
    > 사용자 프로그램의 주소 공간에 그 코드가 존재하지 않는다.
    > 운영체제 커널의 주소 공간에 코드가 정의된다.
    > 사용자 프로그램이 운영체제 내부의 함수를 호출해서 사용.
    
- 인터럽트의 처리 우선순위
> 원칙적으로 인터럽트 처리 중에 또 다른 인터럽트가 발생하는 것을 허용하지 않는다.
> 데이터의 일관성 문제 때문이다.
> 그러나 인터럽트마다 우선순위를 부여하여 상대적으로 더 높은 우선순위의 인터럽트가 발생한다면 현재 처리 중이던 인터럽트 코드의 수행 지점을 저장하고 우선순위가 높은 인터럽트를 처리하게 된다.

- 시스템 콜 함수호출
> 일반 함수호출과는 달리 주소공간 자체가 다른 곳으로 이동해야 하므로 프로그램 자신이 인터럽트 라인에 인터럽트를 세팅하는 명령을 통해 이루어진다.
> 이후 일반적인 인터럽트 발생과 동일한 과정을 거친다.
> 시스템 콜을 통해 실행되는 것은 프로세스의 코드가 아닌 운영체제 커널의 코드이지만, 시스템 콜이 수행되는 동안 커널이 `실행상태(running state)`에 있다고 하지 않고, 프로세스가 실행 상태에 있다고 말한다. 대신 `프로세스가 커널모드에서 실행 중`이라고 이야기한다.

- 프로그램의 실행이 끝날 때에는 커널모드로 진입하여 프로그램을 종료한다.

---
---
# Chapter 5. 프로세스 관리
- 프로세스 : 실행 중인 프로그램

- 프로세스의 문맥(context)
    1. 하드웨어 문맥
    2. 프로세스의 주소 공간
    3. 커널상의 문맥

1. 하드웨어 문맥
    - cpu의 수행 상태를 나타내는 것
    > 프로그램 카운터 값
    > 각종 레지스터에 저장하고 있는 값

2. 프로세스의 주소 공간
    - 코드, 데이터, 스택으로 구성되는 자기 자신만의 독자적인 주소 공간
    > 프로세스 context를 결정짓는 중요한 요소

3. 커널상의 문맥
    - 프로그램이 프로세스가 되면 운영체제는 프로세스를 관리하기 위한 자료구조를 유지한다.
    > PCB
    > 커널스택

---
- 프로세스의 3가지 상태
    1. 실행(running)
    2. 준비(ready)
    3. 봉쇄(blocked)

1. 실행 상태 : 프로세스가 cpu를 보유하고, 기계어 명령을 실행하고 있는 상태
2. 준비 상태 : 프로세스가 cpu만 보유하면 당장 명령을 실행할 수 있는 상태. 그러나 아직 cpu를 할당받지 못한 상태
3. 봉쇄 상태 : cpu를 할당받더라도 당장 명령을 실행할 수 없는 프로세스의 상태

- 추가) 아래는 일시적인 상태이다.
> 1. 시작 상태(new) : 프로세스가 생성 중인 상태
> 2. 완료 상태(terminated) : 프로세스가 종료 중인 상태

- context switch
> 실행시킬 프로세스를 변경하기 위해 원래 수행 중이던 프로세스의 context를 저장하고
> 새로운 프로세스의 context를 세팅하는 과정
> 준비 상태에 있는 프로세스들 중 cpu를 할당받을 프로세스를 선택한 후 실제로 cpu의 제어권을 넘겨받는 과정을 `CPU 디스패치`라고 한다.

- PCB의 요소
> 프로세스의 상태 : cpu를 할당해도 되는지 여부
> 프로그램 카운터의 값 : 다음에 수행할 명령의 위치
> cpu 레지스터의 값 : cpu연산을 위해 현 시점에 레지스터에 어떤 값을 저장하고 있는지
> cpu 스케줄링 정보 : cpu 스케줄링을 위해 필요한 정보
> 메모리 관리 정보 : 메모리 할당을 위해 필요한 정보
> 자원 사용 정보 (accounting information) : 사용자에게 자원 사용 요금을 계산해 청구하는 등의 용도로 사용된다.
> 입출력 상태 정보 : 프로세스의 입출력 관련 상태 정보

- 모드 변경과 context switch 는 엄연히 다르다.
> `context switch`에는 훨씬 많은 `overhead`가 뒤따르게 된다.
> 타이머 인터럽트/입출력 요청 시스템 콜 에는 `context switch`가 일어난다.
> 그밖의 인터럽트나 시스템 콜 발생 시에는 실행모드만이 변경된다. (원래의 `사용자모드`에서 `커널모드`를 거쳐 다시 동일한 프로세스의 `사용자모드`로 돌아올 뿐이다.)

- cpu를 기다리는 줄
> `ready queue` : 준비 상태에 있는 프로세스들의 줄 :: cpu 스케줄링 방법에 따라 줄세우는 방법이 달라진다.
> `device queue` : 특정 자원을 기다리는 프로세스들을 줄 세우기 위해 자원별로 두는 큐. ex) `disk I/O queue`, `keyboard I/O queue`
> `job queue` : 시스템 내으 모든 프로세스를 관리하기 위한 큐. 프로세스의 상태와 무관하게 현재 시스템 내에 있는 모든 프로세스가 속해있다. 위 두 queue를 포함한다.


- 스케줄러
1. `long term scheduler` = `job scheduler` : 어떤 프로세스를 준비 큐에 진입시킬지 결정한다.
    - 가끔 호출되기 때문에 상대적으로 속도가 느린 것이 허용된다.
    - 메모리에 동시에 올라가 있는 프로세스의 수를 조절한다.
    - 시작 상태의 프로세스에게 메모리 할당을 승인할지 여부를 결정하기 때문이다.
    - 현대의 시분할 시스템에서는 `long term scheduler`를 거의 두지 않는다.
    - 자원이 빈약하던 시절에나 쓰던것.
2. `short term scheduler` = `cpu scheduler` : 준비 상태의 프로세스 중 어떤 프로세스를 다음번 실행 상태로 만들 것인지 결정한다.
    - 시분할 시스템에서 타이머 인터럽트가 발생하면 `short term scheduler`가 호출된다.
    - 매우 빈번하게 호출되기 때문에 수행 속도가 빨라야 한다.
    - 

3. `medium term scheduling` : 너무 많은 프로세스에게 메모리를 할당해 시스템의 성능이 저하되는 경우 이를 해결하기 위해 메모리에 적재된 프로세스의 수를 동적으로 조절하기 위해 추가된 스케줄러.
    - 너무 많은 프로세스 때문에 cpu 수행에 당장 필요한 프로세스의 주소 공간조차 메모리에 올려놓기 힘들 때 호출된다.
    - 메모리에 올라와 있는 프로세스 중 일부의 메모리를 통째로 스왑영역에 저장해둔다.
    - 이런 행위를 `스왑아웃`이라 한다.
    - 이 스케줄러의 추가로 프로세스의 상태에 `중지 상태(suspended, stopped)`가 추가되었다.

- 중지상태
    1. 중지준비상태
        - 준비 상태에 있던 프로세스가 디스크로 스왑아웃 되었을 때 이 프로세스는 중지준비 상태가 된다.
        - 중지봉쇄 상태에 있던 프로세스가 봉쇄되었던 조건을 만족하게 되면 이 프로세스의 상태는 중지준비 상태가 된다.
    2. 중지봉쇄상태
        - 봉쇄 상태에 있던 프로세스가 디스크로 스왑아웃 되었을 떄 이 프로세스는 중지봉쇄 상태가 된다.

---
- 프로세스의 생성
> 운영체제는 시스템 부팅 후 최초의 프로세스만 직접 생성한다.
> 이후에는 이미 존재하는 프로세스가 다른 프로세스를 복제 생성하게 된다.

- 부모 프로세스 : 프로세스를 생성한 프로세스
    - 자식 프로세스가 모두 종료된 후에 종료될 수 있다.
- 자식 프로세스 : 부모 프로세스에 의해 새롭게 생성된 프로세스


- 생성된 프로세스가 작업을 수행하기 위해서는 자원이 필요하고 자원을 획득하는 방법은 운영체제와 자원의 종류에 따라 상이하다.
    1. 운영체제로부터 직접 자원을 할당받기
    2. 부모 프로세스와 자원을 공유해서 사용하는 방법

- 프로세스가 수행되는 모델
    1. 부모와 자식이 공존하며 수행되는 모델
    > 자식과 부모가 같이 cpu를 획득하기 위해 경쟁하는 관계
    2. 자식이 종료(terminate)될 때까지 부모가 기다리는(wait) 모델
    > 자식 프로세스가 종료될 때까지 부모는 아무일도 하지 않고 봉쇄상태에 머물러 있다가 자식프로세스가 종료되면 준비상태가 된다.

- fork() 시스템 콜 : 자식 프로세스를 생성할 때 부모 프로세스의 내용을 그대로 복제 생성하는 것. 프로세스 식별자를 제외하고 모든것이 동일.
- exec() 시스템 콜 : 새로운 프로그램으로 주소 공간을 덮어 씌운다.

- 프로그램 종료
    1. 자발적 종료 : exit()
    2. 비자발적 종료 : abort()
    > 부모가 자식프로세스를 강제종료 시킨다.
    > 1. 프로세스가 할당 자원의 한계치를 넘어서는 많은 양의 자원을 요구할 때
    > 2. 자식 프로세스에게 할당된 작업이 더 이상 필요하지 않을 때
    > 3. 부모 프로세스가 종료될 때

- IPC(Inter-Process Communication) : 프로세스 간의 협력 메커니즘을 위해 운영체제가 제공하는 대표적인 메커니즘
> 하나의 컴퓨터 안에서 실행 중인 서로 다른 프로세스 간에 발생하는 통신

- 프로세스 사이에 공유 데이터를 사용하는가 그렇지 않는가에 따라 다음의 두 가지 방법으로 나뉜다.
    1. 메시지 전달 방식(message passing)
    > 주소 공간이 다르기 때문에 커널이 대신 메세지를 전달해주며 통신한다.
    > 커널에 의해 `send`와 `receive`연산을 제공받는다.
    > 통신을 원하는 두 프로세스는 `커뮤니케이션 링크`를 생성하여 메시지를 주고 받는다.
    > 전송 대상이 다른 프로세스인지 저장공간인지에 따라 `직접통신`과 `간접통신`으로 나뉜다.
    2. 공유메모리 방식(shared memory)
    > 프로세스들이 주소 공간의 일부를 공유한다.
    > 서로의 데이터에 일관성 문제가 유발될 수 있다.
    > 이는 커널이 책임지지 않기에 프로세스들끼리 직접 동기화 문제를 책임져야 한다.

---
# Chapter 6. CPU 스케줄링
- 프로그램의 수행은 서로 다른 두 단계의 조합으로 이루어진다.
1. CPU버스트 : 사용자 프로그램이 cpu를 직접 가지고 빠른 명령을 수행하는 일련의 단계
2. I/O 버스트 : I/O 요청이 발생해 커널에 의해 입출력 작업을 진행하는 비교적 느린 단계

- 프로세스를 두 분류로 나눈다면.
1. I/O 바운드 프로세스 : I/O 요청이 빈번해 CPU 버스트가 짧게 나타나는 프로세스
> 사용자로부터 인터랙션을 계속 받아가며 프로그램을 수행시키는 대화형 프로그램
> interactive job을 수행하는 프로그램들은 빠른 응답이 중요하므로 cpu를 우선적으로 사용할 수 있게 해야한다.
2. CPU 바운드 프로세스 : I/O 작업을 거의 수행하지 않아 CPU 버스트가 길게 나타나는 프로세스
> 프로세스 수행의 상당 시간을 입출력 작업 없이 CPU 작업에 소모하는 계산 위주의 프로그램

- CPU 스케줄링이 필요한 경우
1. 실행 상태의 프로세스가 I/O 요청 등에 의해 봉쇄상태로 바뀌는 경우 : `nonpreemptive`
2. 실행 상태의 프로세스가 타이머 인터럽트 발생에 의해 준비 상태로 바뀌는 경우 : `preemptive`
3. I/O 요청으로 봉쇄 상태의 프로세스의 I/O 작업이 완료되어 인터럽트가 발생하고 그 결과 이 프로세스의 상태가 준비 상태로 바뀌는 경우 : `preemptive`
4. CPU에서 실행 상태에 있는 프로세스가 종료되는 경우 : `nonpreemptive`

- CPU 스케줄링 방식
1. 비선점형 방식(nonpreemptive) : CPU를 획득한 프로세스가 스스로 CPU를 반납할 때까지 CPU를 뺏기지 않는 방법
2. 선점형 방식(preemptive) : 위와는 달리 강제로 빼앗을 수 있는 방법; 주로 타이머 인터럽트를 발생시킨다.

---
- 디스패처 : CPU 스케줄러에 의해 새롭게 선택된 프로세스가 CPU를 할당받고 작업을 수행할 수 있도록 환경설정을 하는 운영체제의 코드
> 현재 수행 중이던 프로세스의 context를 그 프로세스의 PCB에 저장
> 새롭게 선택된 프로세스의 context를 PCB로부터 복원
> 그 프로세스에게 CPU를 넘기기

- 디스패치 지연시간(dispatch latency) : 디스패처가 하나의 프로세스를 정지시키고 다른 프로세스에게 CPU를 전달하기까지 걸리는 시간
> latency의 대부분은 문맥교환 오버헤드에 해당된다.

- 스케줄링 성능 평가
    - cpu 이용률(CPU utilization) : 전체 시간 중에서 CPU가 일을 한 시간의 비율
    - 처리량(throughput) : 주어진 시간 동안 준비 큐에서 기다리고 있는 프로세스 중 몇 개를 끝마쳤는지를 나타낸다.
    - 소요시간(turnaround time) : 프로세스가 CPU를 요청한 시점부터 자신이 원하는 만큼 CPU를 다 쓰고 CPU 버스트가 끝날 때까지 걸린 시간
    > 준비큐에서 기다린 시간 + CPU를 사용한 시간
    - 대기시간(waiting time) : CPU 버스트 기간 중 프로세스가 준비 큐에서 CPU를 얻기 위해 기다린 시간의 합
    - 응답시간(response time) : 프로세스가 준비 큐에 들어온 후 첫 번째 CPU를 획득하기까지 기다린 시간

---
#### 스케줄링 알고리즘
1. 선입선출 스케줄링(FCFS) : 프로세스가 준비 큐에 도착한 시간 순서대로 CPU를 할당하는 방식
> 자발적으로 CPU를 반납할 때까지 빼앗지 않는다.
> 따라서 평균 대기시간이 길어지게 된다.
> I/O 장치들의 이용률또한 동반 하락한다.
> 
> `convoy effect` : CPU 버스트가 짧은 프로세스가 CPU 버스트가 긴 프로세스보다 나중에 도착해 오랜 시간을 기다려야 하는 현상

2. 최단작업 우선 스케줄링(Shortest-Job First; SJF) : CPU 버스트가 가장 짧은 프로세스에게 제일 먼저 CPU를 할당하는 방식
> 비선점형 방식과 선점형 방식의 두 가지로 나뉜다.
> 선점형 방식 : 준비 큐에서 CPU 버스트가 가장 짧은 프로세스에게 CPU를 할당했더라도 CPU버스트가 더 짧은 프로세스가 도착할 경우 CPU를 빼앗아 더 짧은 프로세스에게 부여하는 방식을 말한다.
> 선점형 구현 방식 = SRTF(Shortest Remaining Time First)
> 프로세스의 CPU 버스트 시간을 미리 알 수 없어서 예측을 통해 CPU 버스트 시간을 구한 후 예측치가 가장 짧은 프로세스에게 CPU를 할당한다.
> 예측 시간은 과거의 CPU 버스트 시간으로 최근 것에 가중치를 주는 방식으로 계산한다.
> CPU 버스트가 짧은 프로세스에게만 CPU를 할당할 경우 CPU 버스트가 긴 프로세스는 준비 큐에 무한히 대기해야 하는 문제(`기아 현상(starvation)`) 발생

3. 우선순위 스케줄링(priority scheduling) : 준비 큐의 프로세스들 중 우선순위가 가장 높은 프로세스에게 제일 먼저 CPU를 할당하는 방식
> priority number를 통해 표시하며 값이 작을수록 높은 우선순위이다.
> 마찬가지로 선점형 방식과 비선점형 방식으로 구현할 수 있다.
> 우선순위가 높은 프로세스가 계속 도착하면 낮은 우선순위의 프로세스는 여전히 영원히 기다려야 할 수도 있다. (기아현상 발생)
> 이를 해결하기 위해 `노화(aging)` 기법을 사용하여 기다리는 시간이 길어지면 우선순위를 조금씩 높인다.

4. 라운드 로빈 스케줄링(Round Robin Scheduling) : 시분할 시스템을 활용하여 CPU를 연속적으로 사용할 수 있는 시간이 특정시간으로 제한된다. 이후 준비 큐의 맨 뒤에 가서 줄선다.
> 여러 종류의 이질적인 프로세스가 같이 실행되는 환경에서 효과적이다.
> 대화형 프로세스의 빠른 응답시간을 보장할 수 있다는 장점이 있다.
> 할당시간을 너무 짧게 설정하면 문맥교환의 오버헤드가 증가해 전체 시스템의 성능을 저하시킬 수 있다.

5. 멀티레벨 큐 : 준비 큐를 여러 개로 분할해 관리하는 스케줄링 기법
> 어느 큐를 우선적으로 스케줄링할 것인가, 어느 큐에 프로세스를 줄세울 것인가 하는 문제도 발생한다.
> 일반적으로 성격이 다른 프로세스들을 별도로 관리하고 프로세스의 성격에 맞는 스케줄링을 적용하기 위해 준비 큐를 별도로 두게 된다.
> 일반적으로 대화형 작업을 위한 전위 큐(foreground queue)와 계산 위주의 작업을 담기 위한 후위 큐(background queue)로 분할 운영된다.
> 전위 큐에서는 응답시간을 짧게 하기 위한 라운드 로빈 스케줄링, 후위 큐에서는 FCFS 스케줄링 기법을 사용해 문맥교환 오버헤드를 줄인다.
>
> - 큐에 대한 스케줄링
> 1) 고정 우선순위 방식 : 일반적으롲 전위 큐에 우선순위를 둔다.
> 2) 타임 슬라이스 방식 : 큐에 대한 기아 현상을 해소하기 위한 방식이다.

6. 멀티레벨 피드백 큐 : 멀티레벨 큐와 동일하지만 프로세스가 하나의 큐에서 다른 큐로 이동이 가능하다.
> aging 기법을 멀티레벨 피드백 큐 방식으로 구현할 수 있다.
> 라운드로빈큐에서 5의 할당시간동안 작업을 완료하지 못했으면 10라운드로빈큐로 강등, 이후로도 작업을 완료하지 못했다면 계산위주의 프로세스로 간주되어 FCFS큐로 강등하는 방식.

7. 다중처리기 스케줄링 : CPU가 여러 개인 시스템(multi-processor system)
> CPU별 부하가 적절히 분산되도록 하는 부하균형(load balancing) 메커니즘을 필요로 한다.
> 1) 대칭형 다중처리(symmetric multi-processing) : 각 cpu가 각자 알아서 스케줄링을 결정하는 방식
> 2) 비대칭형 다중처리(asymmetric multiprogramming) : 하나의 cpu가 다른 모든 cpu의 스케줄링 및 데이터 접근을 책임지고 나머지는 거기에 따라 움직이는 방식

8. 실시간 스케줄링 : 각 작업마다 데드라인이 주어져 정해진 시간 안에 반드시 작업을 처리해야 한다.
> 1) 경성 실시간 시스템(hard real-time system) : 미사일 발사, 원자로 제어 등 시간을 정확히 지켜야 하는 시스템
> 2) 연성 실시간 시스템(soft real-time system) : 데드라인이 있지만 지키지 못해도 위험하지는 않은 시스템. ex) 멀티미디어 스트리밍 시스템
> EDF(Earlist Deadline First) : 먼저 온 요청을 처리하기 보다는 데드라인이 얼마 남지 않은 요청을 먼저 처리하는 스케줄링

---
- 스케줄링 알고리즘의 평가
- 시뮬레이션 방식 : 실제 시스템에 구현하지 않고 가상으로 cpu 스케줄링 프로그램을 작성한 후 프로그램의 cpu 요청을 입력값으로 넣어 어떤 결과가 나오는지 확인하는 방법
> 입력값은 가상으로 생성할 수도 있고 실제 시스템에서의 cpu요청 내역을 추출해 사용할 수도 있다.
> `트레이스(trace)` : 실제 시스템에서 추출한 입력값. 몇 초에 어떤 프로세스가 도착하고, 각각 CPU 버스트 시간을 얼마로 하는지에 대한 정보를 시간 순서대로 적어놓은 파일

---
# Chapter 7. 메모리 관리
- 컴퓨터상의 주소는 32비트를 나누어 관리한다. 보통 4KB(12비트) 단위로 묶어 페이지라는 단위를 만든다.

### 1. 주소 바인딩
- 프로그램이 실행을 위해 메모리에 적재되면 그 프로세스를 위한 독자적인 주소 공간이 생긴다.
> 이 주소를 `논리적 주소(logical address)` 또는 가상 주소(virtual address) 라고 부른다.
> CPU는 이 주소에 근거해 명령을 실행한다.

- `물리적 주소(physical address)` : 물리적 메모리에 실제로 올라가는 위치
> 낮은 주소 영역에는 운영체제가, 높은 주소 영역에는 사용자 프로세스들이 올라간다.
> 프로세스가 실행되기 위해서는 해당 프로그램이 물리적 메모리에 올라가 있어야 한다.

- 논리적 주소를 통해 메모리 참조를 하면 논리적 주소가 물리적 메모리의 어느 위치에 매핑되는지 확인해야 한다.
> 이 연결 작업을 `주소 바인딩`이라고 한다.(address binding)

- 주소 바인딩 방식 : 프로그램이 적재되는 물리적 메모리의 주소가 결정되는 시기에 따라 분류
1. 컴파일 타임 바인딩 : 물리적 메모리 주소가 프로그램을 컴파일할 때 결정되는 방식
> 프로그램이 절대주소로 적재된다는 뜻에서 절대코드(absolute code) 생성 바인딩 방식 이라고도 한다.
> 물리적 메모리의 위치를 변경하고 싶다면 컴파일을 다시 해야 한다.
> 따라서 시분할 컴퓨팅 환경에 잘 사용되지 않는다.
2. 로드 타임 바인딩 : 프로그램의 실행이 시작될 때에 물리적 메모리 주소가 결정되는 방식
> loader의 책임하에 물리적 메모리 주소가 부여된다
> 프로그램이 종료될 때까지 물리적 메모리상의 위치가 고정된다.
> 컴파일러가 재배치 가능 코드(relocatable code)를 생성한 경우에 가능한 주소 바인딩 방식
3. 실행시간 바인딩(runtime binding) : 프로그램이 실행을 시작한 후에도 그 프로그램이 위치한 물리적 메모리상의 주소가 변경될 수 있는 방식
> CPU가 주소를 참조할 때마다 해당 데이터가 물리적 메모리의 어느 위치에 존재하는지, `주소매핑테이블`을 통해 바인딩을 점검해야 한다.
> 기준 레지스터(base register)와 한계 레지스터(limit register)를 포함해 MMU(Memory Management Unit)이라는 하드웨어적 지원이 필요하다.
> MMU : 논리적 주소를 물리적 주소로 매핑해주는 하드웨어 장치

---
- MMU 기법
> CPU가 특정 프로세스의 논리적 주소를 참조하려고 할 때
> 그 주소값에 기준 레지스터(relocation register)의 값을 더해 물리적 주소값을 알아낸다.
> 프로그램의 주소 공간이 물리적 메모리의 한 장소에 연속적으로 적재되는 것으로 가정한다.
> 기준레지스터의 값은 프로세스마다 상이하므로 같은 논리적 주소값이라도 실제 물리적 메모리 주소값은 다르게 된다.

- MMU 기법에서 `memory protection`이 이루어지지 않는 경우를 방지하기 위해 운영체제는 한계 레지스터를 사용한다.
> 자신의 주소 공간을 넘어서는 메모리 참조를 하려고 하는지 체크하는 용도로 사용된다.
> 프로세스의 논리적 주소의 최댓값을 담고 있다.
> 최댓값보다 큰 주소 영역에 접근하려는 시도가 발견되면 트랩을 발생시켜 해당 프로세스를 강제종료시킨다.

---
### 2. 메모리 관리 용어