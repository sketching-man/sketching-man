Operating Systems: You should understand processes, threads, concurrency issues,
locks, mutexes, semaphores, monitors and how they all work. Understand deadlock,
livelock and how to avoid them. Know what resources a process needs and a thread
needs. Understand how context switching works, how it's initiated by the operating
system and underlying hardware. Know about scheduling and the fundamentals of
"modern" concurrency constructs.

운영체제:
메인 메모리의 영역 (code, data, stack, heap)
프로그램이 실행되면 운영체제는 메인 메모리에 4 종류의 메모리 공간을 할당해준다.
Code 영역: 프로그램의 소스 코드가 저장되는 영역이다. CPU는 코드 영역에 저장된 명령어 (소스, 함수, 제어문)를 가져와서 처리한다.
Data 영역: 전역 변수와 정적 변수가 저장되는 영역이다. 프로그램 시작과 함께 할당되며, 프로그램이 종료되면 소멸한다.
Stack 영역: 프로그램이 사용하는 임시 메모리 영역이며 함수의 호출과 관계되는 지역 변수와 매개 변수가 저장되는 영역이다.
	   함수의 호출과 함께 할당되며, 함수가 종료되면 소멸한다. 함수의 호출 구조가 스택 자료 구조 (LIFO)와 동일하기에 스택으로 구현된다. 컴파일 타임에 영역의 크기가 결정된다.
Heap 영역: 사용자에 의해 메모리 공간이 동적으로 할당되고 해제되는 영역이다. 즉, 동적 할당을 통해 생성된 동적 변수를 관리하기 위한 영역이다. 런타임에 영역의 크기가 결정된다.

프로세스 vs 쓰레드
프로세스: “컴퓨터에서 연속적으로 실행되고 있는 컴퓨터 프로그램”
	각각 독립된 메모리 영역(Code, Data, Stack, Heap의 구조)을 할당
	최소 하나의 메인 쓰레드를 가짐
	각 프로세스는 별도의 주소 공간에서 실행되며, 한 프로세스는 다른 프로세스의 변수나 자료구조에 접근 불가. 접근하기 위해서는 IPC 필요.
	(https://junyongs.wordpress.com/2014/01/19/process-%ea%b0%84-%ed%86%b5%ec%8b%a0-%eb%b0%a9%eb%b2%95-inter-process-communication-ipc/)
쓰레드: “컴퓨터에서 연속적으로 실행되고 있는 컴퓨터 프로그램”
	프로세스 내에서 각각 Stack만 따로 할당받고 Code, Data, Heap 영역은 공유
	한 스레드가 프로세스 자원을 변경하면, 다른 이웃 스레드(sibling thread)도 그 변경 결과를 즉시 볼 수 있다

IPC란?
multiple threads in one or more processes 간의 데이터 통신 방법들.
File: 다양한 프로세스들이 접근 가능한 파일에 써놓는 방법. lock 필요.
Signal: 한 프로세스에서 다른 프로세스로 system message 를 보냄. 대용량 불가.
Socket: Network interface 를 통해 data stream 을 전송.
Message Queue: receiving process별로 message queue를 가지고 있고, sending process를 해당 message queue로 data / message 전송. 대표적으로 Tibco사의 Rendezvous.
Shared Memory: Memory 상에 여러 process 가 읽고 / 쓸 수 있는 공간을 할당. lock 필요.

암달의 법칙
f: 전체 시스템 시간에서 개선된 부분이 차지하고 있는 시간의 비율. (= 병렬화 가능한 부분)
S: 성능이 몇 배 향상됐는지 (= 프로세서 개수)
Slatency=1/((1−f)+f/s)

구스탚슨의 법칙
a: 병렬화되지 않는 부분의 비율
P: 프로세서의 개수
이론상 성능 향상 비율 S(P)=P−a(P−1)

최고의 쓰레드 갯수는?
정형화 된 공식은 없지만, 경험적으로 수치를 정한다.
일반적으로 (프로세서 수 x 2 + 2) 에서 시작해서 테스트를 통해 결정한다는듯?

쓰레드는 두 가지 종류.
1) Foreground threads
2) Background threads
Foreground threads keeps application active.
Background threads working on background maintain GC activities etc
Once foreground threads finishes it's execution all background threads closed automatically.

Daemon Process vs Background Process
Daemon Process 와 Background Process 모두 사용자와 상호작용하는 Foreground가 아닌 Background에서 동작하지만 둘 사이에는 차이가 있다. 
Daemon: 일반적인 데몬은 터미널을 갖지 않는다. (유저와 상호작용하지 않는다.)
	정확히는 데몬은 그 자체로 Process Group Leader로 부모가 1번 pid를 갖는 init으로 세팅된다.
	즉, 독자적인 Session 을 갖고있으며 Linux System 자체가 아닌 별도의 독립된 Parent process를 갖지 않는다.
	리눅스의 cron, smartd 와 같은 스케줄링 & 모니터링 프로세스들은 바로 이 Daemon Process 의 형태를 갖는다.
	또한 윈도우에서는 데몬을 서비스라고 부른다.
Background: 백그라운드 프로세스는 이와 다르게 터미널을 통해 상호작용이 가능하다.
	별도의 Parent process를 가질 수 있으며 Parent process와 세션이 공유되기 때문에
	Parent process가 받는 Signal 의 영향을 받는다. (부모 프로세스가 종료되면 같이 종료된다.)

concurrency란?
concurrency is the ability of different parts or units of a program, algorithm, or problem to be executed out-of-order or in partial order, without affecting the final outcome.
concurrency란 프로그램의 서로 다른 부분들이 서로의 결과물에 영향을 끼치지 않고 순서에 무관하게, 즉 동시에 수행하는 것을 의미함.

Concurrency vs Parallelism
Concurrency: Logical Level.
	물리적으로 완전히 동일한 한 시점만을 말하는 것이 아니라 사실 상 동시라고 간주할 수 있는 시점도 포함한다.
	물리적으로 미세하게 다른 시점일지라도 애플리케이션 관점에서 동시라고 간주할 수 있는 시간 간격에서 복수의 태스크가 수행된다면 동시성이 있다.
	그래서 동시성은 CPU(또는 코어)가 1개인 상황에서도 가능하다.
	시분할 시스템을 통해 사실 상 동시라고 간주해도 무방한 시간에 여러 개의 태스크를 진행시키고 있다면 동시성이 있다.
Parallelism: Machine Level.
	병렬성에서 말하는 동시는 물리적으로 완전히 동일한 시점이다.
	그래서 CPU(또는 코어)가 1개인 상황에서는 병렬성을 가질 수 없다.

경쟁 상태란?
race condition.
여러 프로세스/스레드가 공유된 데이터를 읽고 쓰는 작업을 할 때 실행 순서에 따라서 잘못된 값을 읽거나 쓰게 되는 상황.
mutex/semaphore 등으로 해결.

lock vs mutex vs semaphores vs monitors (C# 기준)
https://sarveshknet.wordpress.com/2012/11/09/lock-vs-monitor-vs-mutex-vs-semaphore/
lock: process-level object, 해당 프로세스 내의 다른 스레드의 진입을 방지
monitor: c# 기준, lock(obj) 함수가 내부적으로는 monitor 객체를 사용해 구현되었음. 단, try-finally 문을 사용하기 때문에 편하게 만들어진+해제를 보장하는 lock을 쓰는게 나음
mutex: kernel-level object, 이름을 정해줄 수 있어서, 모든 프로세스가 공유함. 모든 프로세스로부터 해당 데이터의 스레드 접근을 막을 수 있음
semaphores: kernel-level object, 이름을 정해줄 수 있어서, 모든 프로세스가 공유함. 모든 프로세스로부터 해당 데이터의 스레드 접근을 정해진 수 까지만 받고 막을 수 있음
+) critical section: lock으로 막아놓은 특정 영역
+) spin lock: critical section에 진입이 가능할 때까지 루프를 돌면서 재시도하는 방식으로 구현된 락. Busy Waiting이며 대기 중 다른 쓰레드에게 CPU를 양보하지 않음

deadlock이란? livelock이란?
데드락은 스레드들이 서로 상대방이 가진 자원을 기다리느라 전혀 진도를 나가지 못하는 상황. 프로세스가 멈춰있음.
	= 서로 좁은 길에서 마주보고 있는 상황
라이브락은 두 스레드가 락의 해제와 획득을 무한 반복하는 상태. 프로세스가 동작은 하지만 실제 진행은 없는 상황.
	= 서로 길에서 왼쪽 오른쪽으로 무한히 와리가리하는 상황

deadlock의 조건은?
4가지 조건을 모두 만족할 때 deadlock이 발생할 가능성이 있음.
1. 상호 배제(Mutual Exclusion): 한 자원에 대한 여러 프로세스의 동시 접근은 불가능하다.
2. 점유와 대기(Hold and Wait): 하나의 자원을 소유하고 다른 프로세스 혹은 스레드의 자원을 요청하는 상태이다.
3. 비선점(Non preemptive): 하나의 프로세스나 스레드에게 주어진 자원은 해당 프로세스나 스레드가 스스로 놓기 전에는 놓게 만들 수 없는 상태.
4. 환형 대기(Circle wait):  각 프로세스가 다음 프로세스가 요구하는 자원을 가지고 있는 것을 말한다.

deadlock 해결 방법은?
위의 4가지 조건들 중 하나라도 제거하면 된다.
1. 예방(Prevention) : 교착상태가 발생하지 않도록 하는 것
2. 회피(Avoidance) : 교착상태를 피하는 것. Ex) Dijkstra의 Banker’s Algorithm
3. 탐지(Detection) : 교착상태가 발생하면 탐지 하는 것, 복구를 수반   
4. 복구(Recovery) : 프로세스 중지, 자원 선점(희생자의 선택-> 기아, 복귀)

AutoResetEvent
쓰레드 동기화에는 Locking과 같이 공유 리소스에 락을 걸어 쓰레드 접근을 제한하는 방식(Monitor, Mutex, Semaphore 등)이외에,
대기 중인 쓰레드에게 외부에서 신호(Signal)을 보냄으로써 쓰레드 흐름을 통제하는 방식

운영 체제에서 기아(Starvation) 란 무엇입니까?
특정 프로세스의 우선순위가 낮아서 원하는 자원을 계속 할당 받지 못하는 상태.
기아상태는 자원 관리 문제이다.
대기중인 프로세스는 리소스가 다른 프로세스에 할당되어 있기 때문에 오랫동안 필요한 리소스를 얻지 못한다.

프로세스가 필요한 자원 vs 쓰레드가 필요한 자원
프로세스가 사용하는 메모리 자원은 네 종류 있음 = 메인 메모리의 영역 (code, data, stack, heap)
이 중에서 쓰레드는 프로세스 안에서 stack만 따로 가져가고 나머지 code, data, heap은 프로세스 내의 모든 쓰레드와 공유함.

OS CPU 스케줄링이란?
CPU가 하나의 프로세스 작업이 끝나면 다음 프로세스 작업을 수행해야 한다.
이때 다음 프로세스가 어느 프로세스인지를 선택하는 알고리즘.
First-Come, First-Served
Shortest-Job-First
Priority: 문제점은 Starvation(기아), Aging을 통해 프로세스가 기다리는 동안 일정 시간이 지나면 우선순위를 높여줌
Round-Robin

선점형(Preemptive) 스케줄링 vs 비선점형(Non-Preemptive) 스케줄링
선점형(Preemptive) 스케줄링: 프로세스가 CPU를 할당받아 실행 중이더라도 I/O나 인터럽트가 발생한 것도 아니고 모든 작업을 끝내지도 않았는데, 다른 프로세스가 CPU를 강제로 빼앗을 수(=선점) 있는 방식
	CPU 사용 독점을 막을 수 있어 효율적인 운영이 가능하지만 잦은 스위칭으로 오버헤드가 많이 발생
비선점형(Non-Preemptive) 스케줄링: 선점형과 반대로, 프로세스가 CPU를 점유하고 있다면 이를 빼앗을 수 없는 방식
	필요한 스위칭만 일어나기 때문에 오버헤드가 상대적으로 적지만 프로세스 배치&규모에 따라 독점 발생 가능.

컨텍스트 스위칭이란? OS에서 어떻게 시작하는지, 하드웨어는 이 때 어떻게 동작하는지
프로세스 P0와 P1이 존재할 때, P0가 CPU를 점유중(excuting)이었고 P1이 대기중(idle)이었는 상태이다가,
얼마후에는 P1이 실행이 되고 P0가 대기가 되는 상태가 찾아온다. 이 타이밍을 인터럽트라고 함.
이때 P0가 실행중에서 대기로 변하게 될 때는 지금까지 작업해오던 내용을 모두 어딘가에 저장해야하는데 그것이 PCB(Process Control Block)이다.
즉, P0는 PCB에 저장해야하고 P1이 가지고 있던 데이터는 PCB에서 가져와야한다. 
이러한 과정 즉, P0와 P1이 서로 대기<->실행을 번갈아가며 하는 것을 컨텍스트 스위칭이라고 한다.

인터럽트란?
CPU가 프로그램을 실행하고 있을 때 실행중인 프로그램 밖에서 예외 상황이 발생하여 처리가 필요한 경우 CPU에게 알려 예외 상황을 처리할 수 있도록 하는 것
1. I/O request (입출력 요청할 때)
2. time slice expired (CPU 사용시간이 만료 되었을 때)
3. fork a child (자식 프로세스를 만들 때)
4. wait for an interrupt (인터럽트 처리를 기다릴 때)

모던한 concurrency construct란?
https://nikgrozev.com/2015/07/14/overview-of-modern-concurrency-and-parallelism-concepts/

페이징이란?
페이징 기법(paging)은 컴퓨터가 메인 메모리에서 사용하기 위해 2차 기억 장치(=하드?)로부터 데이터를 저장하고 검색하는 메모리 관리 기법
즉 가상기억장치를 모두 같은 크기의 블록으로 편성하여 운용하는 기법
주소공간을 페이지 단위로 나누고 실제기억공간은 페이지 크기와 같은 프레임으로 나누어 사용 (프레임 크기 = 페이지 크기)
프레임(Frame): 물리 메모리를 일정된 한 크기로 나눈 블록
페이지(Page): 가상 메모리를 일정된 한 크기로 나눈 블록
페이지 테이블: 프로세스의 페이지 정보를 저장하고 있는 테이블. 하나의 프로세스는 하나의 페이지 테이블을 가지고, 색인과 내용으로 구성.
	색인 : 페이지 번호.
	내용 : 해당 페이지에 할당된 물리 메모리(프레임)의 시작 주소

Unix / Linux Internals: Know what is happening under the hood. Understand kernels,
libraries, system calls, memory management, permissions, file systems, client-server
protocols and the shell. Check out these online books: The Art of UNIX Programming
and Advanced Programming in the Unix Environment

운영체제란?
하드웨어 자원들(cpu, memory, disk, tty)을 관리하고 프로그램들을 지원(support) 해주는 것
리눅스에서 운영체제는 커널, 쉘, 유틸리티 등으로 분리되어 있음

리눅스 커널이란?
‘Memory Resident’ 메모리에 항상 상주해 있는 운영체제의 한 부분.
C-based 프로그램이며, 약간의 C++과 어셈블리 포함.

유틸리티란?
=Command. 디스크에 존재하는 운영체제의 한 부분이며, 프로그램.
커맨드, 즉 명령어로 쓰는 기능들이 여기에 속함.

쉘이란?
유틸리티의 일종이지만, 특수함. Job control을 도맡고 있다는 특징이 있음.
쉘은 운영체제의 커널과 사용자 사이의 다리 역할.
shell은 상용자의 명령어를 해석하고 운영체제가 알아들을 수 있게 지시해준다.
다시 운영체제는 shell 에게서 받은 지시를 해석하여 하드웨어를 위한 지시어로 바꾸어준다.
terminal = text input/output environment
console = physical terminal
shell = command line interpreter

파일이란?
= sequence of bytes. I/O devices들도 파일로서 다뤄짐.

라이브러리란?
여러 곳에서 사용될 수 있겠다 싶은 객체들은 메인 프로젝트와 분리시켜 따로 컴파일해서 배포가 가능한데,
이런 특정 코드(=함수, 클래스)를 포함하는 컴파일된 결과물.
정적 라이브러리 vs 동적 라이브러리 vs 공유 라이브러리
정적 라이브러리(.a)
	컴파일의 link phase에서 프로그램에 포함됨.
	프로그램이 실행되는 순간부터 메모리에 함께 존재함.
	속도 빠름. 시스템 환경 변화에 안정적.
	버그 발생 시 fix에 취약함. 재배포가 힘듬.
공유 라이브러리(.so)
	빌드 시 실행 파일(=executable)에 포함되지 않음.
	프로그램이 실행할 때 메모리에 올라옴.
	다른 프로그램에서 이 라이브러리를 한 번이라도 로드했다면, 또 로드하지 않음
	하나의 시스템 환경에서 하나의 라이브러리를 모두 공유해서 사용
	속도 조금 느림, 시스템 변화에 취약
	버그 발생 시 fix에 유리함. 재배포 쉬움.
동적 라이브러리(.so)
	빌드 시 실행 파일(=executable)에 포함되지 않음.
	프로그램 시작 후 특정 타이밍에 메모리에 올라옴. (처음 라이브러리를 부를 때?)
	속도 느림. 시스템 변화에 취약
	버그 발생 시 fix에 유리함. 재배포 쉬움.	

System calls
이걸 얘기하기 전에 먼저 data protection 이슈부터 얘기해보자.
리눅스는 personal computer에 사용되는 windows와는 달리, 기본적으로 멀티 유저 환경이다.
밥과 대니가 같은 리눅스 시스템 안에서 있다고 가정해보자.
만일 밥의 프로세스가 대니의 프로세스의 정보를 함부로 I/O 하려고 하면 어떨까?
즉, 다른 프로세스의 파일을 삭제하거나 내용을 바꿀 수 있다면? = disaster.
이러한 보안문제를 해결하기 위해 리눅스에서는 사전방지(Prevent)하기 위해 많은 노력을 하는데, 그 중 하나가 system call
리눅스 환경에서 shell은 I/O를 할 수 없고, 대신 커널에게 I/O를 해달라는 작업 부탁을 한다.
현재 리눅스 시스템의 작동은 I/O instruction을 할 때는 커널이 갖고 있는 function에 부탁을 하는 방식으로 되어있다.
이 부탁하는 과정을 System call이라 함.
커널은 해당 입출력 명령이 합법적인 것인지 검사한 후에 I/O를 대신 진행해준다

memory management


permission in linux
리눅스 시스템의 모든 파일은 특정 사용자가 읽기, 기록하기, 삭제하기 행동을 하기 위해
권한이 필요하며, 각 파일마다 이 권한 configuration이 있음.
다중사용자 운영체제에서 파일의 접근권한과 보호 등을 위하여 반드시 필요함.
확인하려면 ls
바꾸려면 chmod

file system in linux
https://tech.osci.kr/linux/2018/07/31/linux-filesystem/
https://neul-carpediem.tistory.com/98

client-server protocols
TCP, UDP, 요런걸 말하는건가.
가장 기본적인 소켓을 쓴다는건 같다.
근데 리눅스는 소켓도 파일로 취급되므로, 하나 생성하면 윈도우에서 소켓 핸들처럼 파일 디스크립터가 반환 됨
