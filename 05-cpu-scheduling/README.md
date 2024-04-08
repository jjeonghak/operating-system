# [ CPU Scheduling ]
> CPU-burst time 분포    
> CPU 스케줄링 알고리즘  

<br>

## CPU and I/O Bursts in Program Execution

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/05-burst-in-program-execution.png" width="500" height="400">

<br>

## 프로세스 특성 분류
1. I/O-bound process - CPU를 잡고 계산하는 시간보다 I/O 작업에 많은 시간 필요

2. CPU-bound process - 계산 위주

<br>

## CPU-burst Time 분포
* Interactive job에게 적절한 response 제공 요망

* CPU와 I/O 장치 등 시스템 자원 효율적 운영 필수

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/05-hyperexponential-distribution.png" width="500" height="400">

<br>

## CPU Scheduler And Dispatcher
* CPU Scheduler - Ready 상태의 프로세스 중 CPU 제어권을 넘길 프로세스 선택

* Dispatcher - CPU Scheduler에 의해 선택딘 프로세스에 CPU 제어권 넘김(context swirch)

<br>

## 스케줄러 성질
* 비선점형(`nonpreemptive`) - 강제로 빼앗지 않고 자진 반납

* 선점점형(`preemptive`) - 강제로 반납

<br>

## Scheduling Criteria(Performance Index)
1. CPU utilization(이용률)  
    * system side  
    * keep the CPU as busy as possible  

2. Throughput(처리량)  
    * system side  
    * of processes that comoplete their execution per time unit  

3. Trunaround time(소요시간)  
    * process side  
    * amount of time to execute a particular process  

4. Waiting time(대기시간)  
    * process side  
    * amount of time a process has been waiting in the ready queue  

5. Response time(응답시간)  
    * process side  
    * amount of time it takes from when a request was submitted unit the first response is produced(not ouput)  

<br>

## Scheduling Algorithm
1. FCFS(First-Come First-Served)

2. SJF(Shortest-Job-First)

3. SRTF(Shortest-Remaining-Time-First)

4. Priority Scheduling

5. RR(Round Robin)

6. Multilevel Queue

7. Multilevel Feedback Queue

<br>

## FCFS(First-Come First-Served)
* 비선점형 스케줄링

* 프로세스 도착 순서대로 처리

* `Convoy Effect` - 처리 시간이 큰 프로세스가 먼저 도착한 경우 비효율

<br>

## SJF(Shortest-Job-First)
* 비선점형 방식  

* 일단 CPU 제어권을 얻으면 완료될 때까지 실행  

* 프로세스의 CPU burst time가 짧은 순서대로 처리  

* 평균 대기시간이 가장 최소  

* Exponential Averaging - 과거의 CPU burst time을 이용해서 추정(estimate)만 가능  
  > t[n] = actual lenght of [n]th CPU burst  
  > τ[n+1] = predicted value of the next CPU burst  
  > α = time constant(0 <= α <= 1)
  >   
  > Define:  
  > τ[n+1] = αt[n] + (1 - α)τ[n]  
  > t[n+1] = αt[n] + (1 - α)αt[n-1] + ... + (1 - α)^(j - 1)αt[n-j] + ... + (1 - α)^(n - 1)τ[0]  

<br>

## SRTF(Shortest-Remaining-Time-First)
* SJF의 최적화 알고리즘

* 선점형 방식

* 현재 실행중인 프로세스의 남은 burst time이 새로 들어온 프로세스보다 긴 경우 CPU 제어권 반환

<br>

## Priority Scheduling
* 프로세스 별로 우선순위를 가지며 해당 순서대로 처리  

* 비선점형, 선점형 방식

* SJF도 Priority Scheduling 일종(priority = predicted next CPU burst time)

* Starvation(기아현상) - 자원을 받지 못하는 프로세스 발생

* Aging(노화) - 오래 대기하면 우선순위 상승, 기아현상 방지 기법

<br>

## RR(Round Robin)
* 선점형 방식

* 각 프로세스는 동일한 크기의 할당 시간(`time quantum`) 보유(10-100ms)

* 할당 시간이 지나면 CPU 제어권을 반환하고 ready queue 후위로 이동

* 응답 시간이 가장 빠름

* 할당 시간이 크면 FCFS, 할당 시간이 짧으면 context switch 오버헤드 증가

* Turnaround Time Varies With Time Quantum

<br>

## Multilevel Queue
* Ready Queue를 여러개로 분할  

* 각 큐마다 독립적인 스케줄링 알고리즘 가능  
  * `foreground`(interative) - RR  
  * `background`(batch - no human interaction) - FCFS  

* 큐에 대한 스케줄링 필요  
  * Fixed priority scheduling - serve all from foreground then from background  
  * Time slice - 80% to foreground in RR, 20% to background in FCFS  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/05-multilevel-queue.png" width="500" height="400">

<br>

## Multilevel Feedback Queue
* 프로세스가 다른 Queue로 이동 가능  

* 에이징을 이와 같은 방식으로 구현 가능  
  * Q0 - time quantum 8ms  
  * Q1 - time quantum 16ms  
  * Q2 - FCFS  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/05-multilevel-feedback-queue.png" width="500" height="400">

<br>

## Multiple-Processor Scheduling(멀티 CPU 스케줄링)
1. Homogeneous processor  
    * 큐에 한줄로 세워서 각 프로세서가 알아서 처리  
    * 반드시 특정 프로세서가 처리해야하는 경우 문제 발생  

2. Load sharing  
    * 일부 프로세서에 job이 몰리지 않도록 부하 분배  
    * 별개의 큐를 사용하는 방법과 공동 큐를 사용하는 방법 존재  

3. Symmetric Multiprocessing(SMP)  
    * 각 프로세서가 각자 알아서 스케줄링 결정  

4. Asymmetric multiprocessing  
    * 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지는 통제 받음  

<br>

## Real Time Scheduling  
* Hard real-time systems - 정해진 시간 안에 반드시 처리되도록 스케줄링  

* Soft real-time computing - 일반 프로레스에 비해 높은 우선순위로 스케줄링  

<br>

## Thread Scheduling
* Local Scheduling - OS 관여없이 라이브러리를 통해 프로세스가 쓰레드 관리  

* Global Scheduling - OS 관여, 쓰레드 관리  

<br>

## Algorithm Evaluation
1. Queueing models  
    * 확률 분포로 주어지는 arrival rate와 service rate등을 통해 performance index 값 계산, 이론적인 방법  

2. Implementation & Measurement  
    * 실제 시스템에 알고리즘을 구현하여 실제 작업(workload)에 대해서 성능 측정 비교  

3. Simulation  
    * 알고리즘을 모의 프로그램으로 작성후 trace를 입력으로 하여 결과 비교  

<br>





