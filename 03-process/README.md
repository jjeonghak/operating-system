# [ 03-process ]
> 프로세스 개념  
> 프로세스 상태  
> 프로세스 문맥교환  
> 프로세스 스케줄링  

<br>

## 프로세스 개념  
* `Process is a program in execution`  
* 프로세스 문맥(context)  
  * CPU 수행 상태를 나타내는 하드웨어 문맥  
    PC(Program Counter)  
    각종 Register  
  * 프로세스 주소 공간  
    code, data, stack  
  * 프로세스 관련 커널 자료구조  
    PCB(Process Control Block)  
    Kernel stack  
<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/03-process-context.png" width="500" height="400">

<br>

## 프로세스 상태
1. `Running` - CPU를 잡고 instruction을 수행중인 상태  
2. `Ready` - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족)  
3. `Blocked`(wait, sleep) - CPU를 주어도 instruction을 수행할 수 없는 상태, 자신이 요청한 event 만족시 ready  
4. `Suspended`(stopped) - 외부적인 이유로 프로세스 수행이 정지된 상태, 디스크로 swap out, 외부에서 resume시 activce  
5. `New` - 프로세스가 생성중인 상태  
6. `Terminated` - 수행(execution)이 끝난 후 정리하는 상태

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/03-process-state.png" width="600" height="500">

<br>

## PCB(Process Control Block)
* 운영체제가 각 프로세스를 관리하기 위해 프로세스 당 유지하는 정보  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/03-process-control-block.png" width="500" height="500">

<br>

> (1) OS가 관리상 사용하는 정보: process state, process id, scheduling information, priority  
> (2) CPU 수행 관련 하드웨어 값: program counter, registers  
> (3) 메모리 관련: code, data, stack의 위치정보  
> (4) 파일 관련: open file descriptors  

<br>

## 문맥 교환(Context Switch)
* CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정  
* CPU가 다른 프로세스에게 넘어갈 때 운영체제는 아래 과정 수행  
  * CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장  
  * CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/03-context-switch-1.png" width="600" height="500">

<br>

* System call이나 Interrupt 발생시 반드시 문맥 교환이 일어나는 것은 아님

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/03-context-switch-2.png" width="500" height="400">

<br>

## 프로세스 스케줄링 큐
1. Job Queue: 현재 시스템 내에 있는 모든 프로세스 집합  
2. Ready Queue: 현재 메모리 내에 존재하며 CPU 제어권을 대기하는 프로세스 집합  
3. Device Queues: I/O device 처리를 기다리는 프로세스 집합  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/03-process-queue.png" width="600" height="500">

<br>

## 스케줄러(Scheduler)
1. Long-term scheduler(Job scheduler, 장기 스케줄러)
    * 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
    * 프로세스에 memory 할당하는 문제
    * degree of Multiprogramming 제어
    * time sharing system에는 보통 존재하지 않음

2. Short-term scheduler(CPU scheduler, 단기 스케줄러)
    * 어떤 프로세스를 running 시킬지 결정
    * 프로세스에 CPU 할당하는 문제

3. Medium-term scheduler(Swapper, 중기 스케줄러)
    * 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 이동
    * 프로세스에게서 memory 해제하는 문제
    * degree of Multiprogramming 제어

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/03-process-scheduling.png" width="500" height="400">

<br>

