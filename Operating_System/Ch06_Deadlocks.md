# Deadlocks

### Deadlock Problem

* Deadlock : 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태
* Resource
  * 하드웨어, 소프트웨어 등을 포함하는 개념
  * I/O device, CPU cycle, memory space, semaphore 등
  * 프로세스가 자원을 사용하는 절차 : request -> allocate -> use -> release
* deadlock 예시 1
  * 시스템에 2개의 tape drive가 있다.
  * 프로세스 P_1과 P_2 각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.
* deadlock 예시 2
  * binary semaphore A와 B에 대해
  * P_0 : P(A); P(B);
  * P_1 : P(B); P(A);
  
### Deadlock 발생의 4가지 조건

* mutual exclusion : 매 순간 하나의 프로세스만이 자원을 사용할 수 있다.
* no preemption : 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않는다.
* hold and wait : 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있다.
* circular wait
  * 자원을 기다리는 프로세스 간에 사이클이 형성되어야 한다.
  * 프로세스 P_0, P_1, ... , P_n이 있을 때
    * P_0는 P_1이 가진 자원을 기다린다.
    * P_1은 P_2가 가진 자원을 기다린다.
    * P_(n-1)은 P_n이 가진 자원을 기다린다.
    * P_n은 P_0가 가진 자원을 기다린다.

### Resource-Allocation Graph(자원 할당 그래프)

![deadlock_1](https://user-images.githubusercontent.com/70595250/158007995-8f6c2bb8-3d2f-4949-b1d5-36fe744d9637.PNG)
* 자원에서 점의 개수 : 해당 자원의 인스턴스 개수
* 간선
  * 프로세스 -> 자원 : 프로세스가 자원을 기다리고 있다.
  * 자원 -> 프로세스 : 프로세스에 자원이 할당되어 있다.
* 그래프에 cycle이 없으면 deadlock이 아니다.
* 그래프에 cycle이 있으면
  * 각 자원마다 하나의 인스턴스만 있다면 deadlock
  * 각 자원마다 여러 개의 인스턴스가 있다면 deadlock이 발생할 가능성이 있다.
  
### Deadlock의 처리 방법

* Deadlock Prevention : 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 한다.
* Deadlock Avoidance
  * 자원 요청에 대한 부가적인 정보를 이용해 deadlock의 가능성이 없는 경우에만 자원을 할당
  * 시스템 상태가 원래 상태로 돌아올 수 있는 경우에만 자원 할당
* Deadlock Detection and Recovery : Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견시 recover
* Dealock Ignorance : deadlock을 시스템이 책임지지 않는다. UNIX를 포함한 대부분의 OS가 채택한 방식

### Deadlock Prevention

* mutual exclusion : 공유해서는 안 되는 자원의 경우 반드시 성립해야 하기에 만족되지 않도록 할 수 없다.
* hold and wait
  * 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
  * 방법 1 : 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법 - 시작할 때 불필요한 자원까지 모두 가지게 돼서 비효율적
  * 방법 2 : 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청
* no preemption
  * process가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
  * 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
  * state를 쉽게 저장하고 복원할 수 있는 자원에서 주로 사용(CPU, memory)
  * 작업 도중에 자원을 빼앗으면 문제가 발생하는 경우에는 불가능
* circular wait
  * 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
  * 예를 들어 순서가 3인 자원 R_i를 보유 중인 프로세스가 순서가 1인 자원 R_j를 할당받기 위해서는 우선 R_i를 release해야 한다.
* Utilization 저하, throughput 감소, starvation 문제

### Deadlock Avoidance

* Deadlock avoidance
  * 자원 요청에 대한 부가정보를 이용해서 자원 할당이 deadlock으로부터 안전한지를 동적으로 조사해서 안전한 경우에만 할당
  * 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법이다.
* safe state : 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태
* safe sequence
  * 프로세스의 sequence P_1, P_2, ... P_n이 safe하려면 P_i의 자원 요청이 가용 자원 + 모든 P_j의 보유 자원에 의해 충족되어야 한다.
  * 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장한다.
    * P_i의 자원 요청이 즉시 충족될 수 없으면 모든 P_j(j < i)가 종료될 때까지 기다린다.
    * P_(i-1)이 종료되면 P_i의 자원요청을 만족시켜 수행한다.
* Resource Allocation Graph Algorithm
  * Claim edge P_i -> R_j
    * 프로세스 P_i가 자원 R_j를 미래에 요청할 수 있음을 뜻한다.(점선으로 표시)
    * 프로세스가 해당 자원 요청시 request edge로 바뀜(실선)
    * R_j가 release되면 assignment edge는 다시 claim edge로 바뀐다.
  * request edge의 assignment edge 변경시 (점선을 포함하여) cyle이 생기지 않는 경우에만 요청 자원을 할당한다.
  * Cycle 생성 여부 조사시 프로세스의 수가 n일 때 O(n^2) 시간이 걸린다.