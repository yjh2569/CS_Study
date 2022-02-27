# CPU 스케쥴링

### CPU와 I/O burst

* 프로그램 실행은 크게 CPU burst와 I/O burst로 나눈다.
* CPU burst : CPU를 가지고 기계어를 실행하는 단계
* I/O burst : I/O를 하러 가는 단계
* 프로그램에 따라 CPU burst를 오래 하는 경우가 있고, I/O burst를 자주 하는 경우가 있다.
  * I/O bound job(process) : I/O 작업이 빈번해 CPU를 짧게 자주 쓰는 프로그램, many short CPU bursts
  * CPU bound job(process) : 계산 위주로 CPU를 한 번에 길게 쓰는 프로그램, few very long CPU bursts
* 그렇다면 누구한테 CPU를 먼저 줄 것인가? 그리고 얼마만큼의 시간을 할당해 줄 것인가? -> CPU 스케쥴링을 통해 결정한다.
* 여러 종류의 job이 섞여 있기 때문에 CPU 스케쥴링이 필요하다.
  * Interactive job(사람과 상호 작용하는 job)에게 적절한 response 제공 요망
  * CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

### CPU Scheduler & Dispatcher

* 운영체제 일부의 코드(소프트웨어)다.
* CPU Scheduler : Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.
* Dispatcher : CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다. 이 과정을 문맥 교환이라 한다.
* CPU 스케쥴링이 필요한 경우 프로세스에게 다음과 같은 상태 변화가 있다.
  * Running -> Blocked(ex : I/O 요청하는 시스템 콜) 
  * Running -> Ready(ex : 할당 시간 만료로 timer interrupt)
  * Blocked -> Ready(ex : I/O 완료 후 인터럽트)
  * Terminate
  * 1번째와 4번째에서의 스케쥴링은 nonpreemptive(강제로 빼앗지 않고 자진 반납)
  * 나머지는 preemptive(강제로 빼앗음)
  
### Scheduling Criteria

* 여러 스케쥴링에 대한 성능 판단을 위한 척도
* CPU 입장에서의 척도
  * CPU utilization(이용률) : 전체 시간 중 CPU가 놀지 않고 일한 시간의 비율
  * Throughput(처리량) : 단위 시간 당 완료한 프로세스의 수
* 프로세스 입장에서의 척도
  * Turnaround time(소요 시간, 반환 시간) : 어떤 프로세스가 하나의 CPU burst를 완료할 때까지 걸린 시간
  * Waiting time(대기 시간) : 프로세스가 하나의 CPU burst 동안 ready queue에서 기다린 시간의 합
  * Response time(응답 시간) : 프로세스가 CPU를 쓰러 들어온 이후(ready queue에 들어온 이후) 첫 CPU 할당받기까지 걸린 시간
  
### FCFS(First-Come First-Served)

* 먼저 온 프로세스가 먼저 처리되는 스케쥴링 기법
* nonpreemptive
* Convoy effect : 먼저 온 프로세스가 CPU burst time이 길 경우 프로세스들의 평균 대기 시간이 길어진다. 이로 인해 CPU burst time이 짧은 프로세스들이 바로 실행되지 못하고 오랜 시간 기다려야 한다.

### SJF(Shortest-Job First)

* 각 프로세스의 다음 번 CPU burst time을 가지고 스케쥴링에 활용
* CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케쥴
* 두 가지 방식
  * nonpreemptive
    * 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 preemption당하지 않는다.
  * preemptive
    * 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗긴다.
    * 이 방법을 SRTF(Shortest-Remaining-Time-First)라고도 부른다.
    * nonpreemptive한 방식보다 더 효율적이다.
* SJF는 optimal하다. : 주어진 프로세스들에 대해 minimum average waiting time을 보장한다.
* SJF의 단점
  * Starvation : CPU burst time이 긴 프로세스는 영원히 CPU를 못 얻을 수도 있다.
  * 다음 CPU burst time을 알 수 없다.
    * 따라서 과거의 CPU burst time을 통해 다음 CPU burst time을 예측한다.(exponential averaging) : 과거 CPU burst time 값들을 참조하되 최근의 burst time일수록 높은 가중치를 둔다.

### Priority Scheduling

* 각 프로세스에게 우선순위(정수)를 부여하고, 우선순위가 높은(정수값이 낮은) 프로세스에게 CPU를 먼저 할당한다.
  * preemptive
  * nonpreemptive
* SJF는 priority가 다음 CPU burst time인 일종의 priority scheduling이다.
* 이 스케쥴링 역시 Starvation 문제를 가지고 있다.
* 해결책 : aging(시간이 지날수록 기다리는 프로세스의 우선순위를 높여준다.)

### Round Robin(RR)

* 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가진다.
* 할당 시간이 지나면 프로세스는 preempted당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다.
* I/O bound job의 경우 한 번의 할당 시간에 빠르게 처리하고 나가서 빠른 응답을 받고, CPU bound job은 I/O bound job에게만 CPU를 양보하지 않고 본인이 사용할 만큼은 사용할 수 있다.
* n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit의 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻는다. -> 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다.
* time quantum이 크면 FCFS가 되고, time quantum이 작으면 context switch 오버헤드가 커진다.
* 일반적으로 SJF보다 average turnaround time은 길지만 response time은 더 짧다. -> Interactive job이 많은 환경에서 유리하다.
* 동일한 길이의 job들만 있는 경우에는 FCFS와 달리 모든 job들이 늦게 완료되어(turnaround time이 길어) 오히려 더 비효율적이다.
* 대기시간이 CPU burst time에 비례한다.

### Multilevel Queue

* Ready queue를 여러 개를 분할하고 각 큐에 대한 우선순위를 부여한다.
  * foreground(interactive)
  * background(batch - no human interaction) : 오래 CPU를 사용하는 프로세스들
* 각 큐는 독립적인 스케쥴링 알고리즘을 가진다.
  * foreground - RR
  * background - FCFS
* 큐에 대한 스케쥴링
  * Fixed priority scheduling
    * foreground에 있는 모든 프로세스들을 실행한 뒤 background에 있는 프로세스들을 실행한다.
    * starvation 문제가 발생한다.
  * Time slice
    * 각 큐에 CPU time을 적절한 비율로 할당한다.
    * ex) 80%는 foreground에, 20%는 background에 할당
    
### Multilevel Feedback Queue

* 프로세스가 다른 큐로 이동 가능
* aging을 이와 같은 방식으로 구현할 수 있다.
* Multilevel-feedback-queue scheduler를 정의하는 파라미터들
  * 큐의 수
  * 각 큐의 scheduling algorithm
  * 프로세스를 상위 큐로 보내는 기준
  * 프로세스를 하위 큐로 내쫓는 기준
  * 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
* 일반적인 예시
  * 세 개의 큐
    * Q0 - 8ms의 할당 시간을 가지는 RR
    * Q1 - 16ms의 할당 시간을 가지는 RR
    * Q2 - FCFS
  * Scheduling
    * 새로운 job이 큐 Q0에 들어간다.
    * CPU를 잡아서 할당 시간 8ms동안 수행된다.
    * 8ms 동안 끝내지 못하면 큐 Q1으로 내려간다.
    * Q1에 줄서서 기다렸다가 CPU를 잡아서 16ms 동안 수행된다.
    * 16ms에 끝내지 못하면 큐 Q2로 쫗겨난다.
    
### Multiple-Processor Scheduling

* CPU가 여러 개인 경우 스케쥴링은 더욱 복잡해진다.
* Homogeneous processor인 경우
  * 큐에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
  * 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐
* Load sharing
  * 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 매커니즘 필요
  * 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
* Symmetric Multiprocessing(SMP)
  * 각 프로세서가 각자 알아서 스케쥴링 결정
* Asymmetric Multiprocessing
  * 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따른다.
  
### Real-Time Scheduling

* Hard real-time systems
  * Hard real-time task는 정해진 시간 안에 반드시 끝내도록 스케쥴링해야 한다.
  * CPU 도착 시간을 알고 미리 스케쥴링하는 경우가 많다(offline).
* Soft real-time systems
  * Soft real-time task는 일반 프로세스에 비해 높은 priority를 갖도록 해야 한다.
  * 주기적으로 일을 하는 경우가 많다.
  
### Thread Scheduling

* Local Scheduling
  * User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케쥴할지 결정
* Global Scheduling
  * Kernel level thred의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케쥴러가 아닌 어떤 thread를 스케쥴할지 결정
  
### Algorithm Evaluation

* Queueing models : 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index(처리량, 대기시간 등) 값을 계산
* Implementation & Measurement : 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해서 성능을 측정 및 비교
* Simulation : 알고리즘을 모의 프로그램으로 작성 후 trace(sample 데이터)를 입력으로 하여 결과 비교

