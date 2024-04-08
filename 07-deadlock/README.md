# [ Deadlock ]
> 교착 상태  
> 교착 상태 발생 조건  
> 교착 상태 처리 방법  
> 자원할당 그래프  


<br>

## Deadlock Problem
* 일련의 프로세스들이 서로가 가진 자원을 기라리며 block된 상태  

* 하드웨어, 소프트웨어 등을 모두 포함하는 자원 개념	  

<br>

## 발생 조건
1. Mutual exclusion(`상호 배제`)  
    * 매 순간 하나의 프로세스만이 자원을 사용할 수 있음  

2. No preemption(`비선점`)  
    * 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음  

3. Hold and wait(`보유 대기`)  
    * 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않음  

4. Circular wait(`순환 대기`)  
    * 자원을 기다리는 프로세스간에 사이클 형성  

<br>

## Resource-Allocation Graph(자원할당그래프)
* 그래프에 cycle이 존재하지 않는 경우 deadlock 아님  

* 그래프에 cycle이 존재하는 경우  
  * if only one instance per resource type, then deadlock  
  * if several instances per resource type, possibility of deadlock  

* Vertex
  * Process P = { P1, P2, ..., Pn }  
  * Resource R = { R1, R2, ..., Rn }  

* Edge
  * `request edge` P[i] -> R[j]  
  * `assignment edge` R[j] -> P[i]  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/07-resource-allocation-graph.png" width="500" height="400">

<br>

## 처리 방법
1. Deadlock Prevention
    * Deadlock 형성 조건 중 하나가 만족되지 않도록 자원 할당  

2. Deadlock Avoidance  
    * 자원 요청에 대해 부가적인 정보를 사용해 가능성이 없는 경우에만 자원 할당  

3. Deadlock Detection and Recovery  
    * Deadlock 발생 허용, 그에 대한 detection 루틴을 두어 발생시 recover  

4. Deadlock Ignorance  
    * Deadlock을 시스템에서 책임지지 않음, UNIX를 포함한 대부분의 OS가 채택  

<br>

## Deadlock Prevention
* Utilization 저하, throughput 감소, starvation 문제 발생  

* Mutual Exclusion  
  * 공유해서는 안되는 자원의 경우 반드시 성립해야함  

* Hold and Wait  
  * 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다  
  * 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법  
  * 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청  

* No Preemption  
  * 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨  
  * 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작  
  * 상태를 쉽게 저장하고 재저장 가능한 자원에서 주로 사용(CPU, memory)  

* Circular Wait  
  * 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당  

<br>

## Deadlock Avoidance
* 자원 요청에 대한 부가정보를 이용해서 자원 할당이 deadlock으로부터 안전한지 동적 조사

* 간단한 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언

* Single instance per resource types - Resource Allocation Graph Algorithm

* Multiple instances per resource types - Banker's Algorithm

<br>

## Deadlock Avoidance - Resource Allocation Graph Algorithm
* `claim edge` P[i] -> R[j]

* 프로세스 P[i]가 자원 R[j]를 미래에 요청할 수 있음을 뜻함(점선)

* 프로세스가 해당 자원 요청시 request edge로 변경(실선)

* R[j]가 release되면 assignment edge는 다시 claim edge로 변경

* 점선을 포함해서 cycle 발생시 자원 할당 안함

* cycle 생성 여부 조사시 O(N^2) 시간 소요  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/07-claim-edge.png" width="500" height="200">

<br>

<br>

## Deadlock Avoidance - Banker's Algorithm
* 가정  
  * 모든 프로세스는 자원의 최대 사용량을 미리 명시  
  * 프로세스가 요청 자원을 모두 할당받은 경우 유한 시간 안에 이들 자원을 다시 반납  

* 방법  
  * 자원 요청시 safe 상태를 유지할 수 있는 경우에만 할당  
  * 총 요청 자원 수가 가용 자원수보다 적은 프로세스 선택(없다면 unsafe 상태)  
  * 할당 받은 프로세스가 종료되면 모든 자원 반납  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/07-banker-alorithm.png" width="500" height="400">

<br>

## Deadlock Detection and Recovery
* Deadlock Detection  
  * 모든 자원 요청 허용  
  * 시스템 성능이 감소한 경우 Deadlock 탐색 후 처리  
  * 추가 자원 요청을 하지 않은 프로세스가 선점한 자원까지 모두 가용자원으로 가정(낙관적)  

* Wait-for graph 알고리즘

* Recovery
> 1. Process termination  
>    * 연루된 모든 프로세스 한번에 종료  
>    * 연루된 프로세스를 하나씩 종료  
>
> 2. Resource Preemption  
>    * 비용 최소화할 victim 선정  
>    * safe state로 rollback 후 process restart  
>    * Starvation 발생  

<br>

## Deadlock Ignorance
* deadlock 발생해도 아무런 조치도 취하지 않음

* 매우 드물게 발생하므로 조치 자체가 더 큰 overhead일 가능성 존재

* 사용자가 직접 kill process를 하는 등의 방법으로 대처

* 대부분의 범용 OS가 해당 방법 채택

<br>

