# [ Virtual Memory ]
> page fault  
> thrashing  

<br>

## Demand Paging
* 하드웨어가 paging 기법 사용 가정  

* 실제로 필요할 때 page를 메모리에 올리는 것  
  * I/O 양 감소  
  * 메모리 사용량 감소  
  * 빠른 응답 시간  
  * 더 많은 사용자 수용  

* Valid/Invalid bit 사용  
  * invalid - 사용되지 않는 주소 영역, 페이지가 물리적 메모리에 없는 경우  
  * 초기에 모든 page entry를 invalid 초기화  
  * `page fault` - address translation 발생시 invalid bit 설정되어 있는 경우  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/09-schematic-view-of-swapping.png" width="500" height="350">

<br>

## Page Fault
* invalid page 접근시 MMU가 trap 발생(`page fault trap`)  

* Kernel mode로 변경되어 page fault handler가 invoke  

* 처리루틴
> 1. Invalid reference?(bad address, protection violation) - abort process  
> 2. Get an empty page frame(없는 경우 선점, replace)  
> 3. 해당 페이지를 disk에서 memory로 로드(valid bit로 전환)  
> 4. 다시 프로세스가 CPU 제어권 선점 후 running  
> 5. 중단되었던 instruction 재개  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/09-page-fault-handling.png" width="600" height="500">

<br>

## Performance of Demand Paging
* Page Fault Rate 0 <= p <= 1.0    
  * if p == 0, no page faults  
  * if p == 1, every reference is a fault  
  * usually p = 0.0x  

* Effective Access Time  
````markup
EAT = (1 - p) * memory access  
      + p * (OS & HW page fault overhead  
      + [swap page out if needed]  
      + swap page in  
      + OS & HW restart overhead)  
````

<br>

## Page Replacement
* free frame이 존재하지 않는 경우  

* 어떤 frame을 선점할지 결정  

* 동일한 페이지가 여러 번 swap out, in 당할 가능성 존재  

* page fault rate 최소화를 목표  

* page reference string(page history)을 사용  

<br>

## Optimal Algorithm
* MIN(OPT) - 가장 먼 미래에 참조되는 페이지를 replace  

* Offline algoritm - 미래 참조를 이미 알고 있다는 가정 필요  

* 미래 참조를 알 수 없기 때문에 실제로 사용불가  

* 다른 알고리즘 성능에 대한 upper bound 제공하기 때문에 참고용으로 사용  

<br>

## FIFO Algorithm
* 먼저 들어온 페이지를 먼저 replace  

* FIFO Anomaly - 메모리 frame을 추가해서 더 성능이 나빠지는 경우 발생 가능  

<br>

## LRU(Least Recently Used) Algorithm
* 가장 오래 전에 참조된 페이지를 replace  

* 참조 횟수는 고려하지 않음  

* 링크드 리스트로 구현 - O(1) complexity  

<br>

## LFU(Least Frequently Used) Algorithm
* 참조 횟수(reference count)가 가장 적은 페이지를 replace  

* 최저 참조 횟수인 페이지가 여러 개인 경우  
  * LFU 알고리즘 자체에서는 여러 개중 임의로 선정  
  * 성능 향상을 위해 가장 오래 전에 참조된 페이지를 지우도록 구현 가능  

* 직전 참조 시점만 보는 것이 아닌 장기적 시간 규모로 보기 때문에 인기도를 정확히 반영  

* 참조 시점의 최근성을 반영하지 못함  

* LRU보다 구현이 복잡  

* 링크드 리스트로 구현 - O(n) complexity  

* 힙으로 구현 - O(log n) complexity  

<br>

## 캐시 환경
* 캐시 기법  
  * 한정된 빠른 공간에 요청된 데이터를 저장해 두었다가 후속 요청시 캐시로부터 직접 서비스  
  * paging system 외에도 cache memory, buffer caching, web caching 등 다양한 분야에서 사용  

* 캐시 운영의 시간 제약  
  * 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간 소요 금지  
  * O(1) 또는 O(log n)까지 허용  
  * LRU 또는 LFU 알고리즘은 페이징 기법으로 사용 불가(OS가 참조 횟수를 알 수 없음)  
  * page fault trap 발생 후 OS가 그 순간만 확인가능  

<br>

## Clock Algorithm
* LRU의 근사(approximation) 알고리즘  

* Second chance algorithm, NUR(Not Used Recently), NRU(Not Recently Used)  

* reference bit를 사용해서 교체 대상 페이지 선정(circular list)  

* `reference bit`는 하드웨어가 물리적 메모리 `접근`시에 1로 변환  

* `modified bit`(dirty bit)는 하드웨어가 물리적 메모리 값 `변경`시에 1로 변환  

* 동작 원리
> reference bit == 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동 후 결과 페이지 replace  
> 만약 해당 페이지의 modified bit == 1인 경우 swap area에 수정된 값을 반영 후 replace  
> 포인터 이동 중에 reference bit를 모두 0으로 변경  
> 자주 사용되는 페이지라면 second chance가 올 때 1  

<br>

## Page Frame Allocation
* Allocation problem - 각 프로세스에 얼마만큼의 page frame을 할당할 것인가  

* 메모리 참조 명령어 수행시, code/data 등 여러 페이지 동시 참조  

* 루프 구성하는 페이지들은 한꺼번에 allocate 되는 것이 유리  

* Allocation Scheme  
  * Equal allocation - 모든 프로세스에 똑같은 갯수 할당  
  * Proportional allocation - 프로세스 크기에 비례해서 할당  
  * Priority allocation - 프로세스의 우선순위에 따라 다르게 할당  

<br>

## Replacement
1. Global replacement  
    * 프로세스에게 미리 frame 할당하지 않는 방법  
    * replace 시 다른 프로세스에 할당된 프레임을 선점 가능  
    * Working set, PFF 알고리즘 사용  

2. Local replacement  
    * 자신에게 할당된 프레임 내에서만 교체  
    * FIFO, LRU, LFU 등의 알고리즘을 프로세스 별로 운영시  

<br>

## Thrashing

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/09-thrashing-diagram.png" width="500" height="300">

<br>

* 프로세스의 원활한 수행에 필요한 최소한의 페이지 frame 수를 할당 받지 못한 경우 발생  

* page fault rate이 지나치게 높아짐  

* CPU utilization이 낮아짐  

* OS는 MPD(Multiprogramming degree)를 높여야 한다고 판단  

* 또 다른 프로세스가 시스템에 추가(higher MPD)  

* 프로세스 당 할당된 frame 수 더욱 감소  

* 프로세느는 swap in/out으로 매우 바쁨  

* 대부분의 시간에 CPU는 한가함  

* low throughput  

<br>

## Working-Set Model
* Locality of reference  
  * 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조  
  * 집중적으로 참조되는 해당 페이지들의 집합을 `locality set`  

* Working-set Model  
  * Locality에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한번에 메모리에 올라와야 하는 페이지들의 집합을 `working set`  
  * 프로세스와 working set 전체가 메모리에 로드되어야 수행  
  * 아닌 경우 모든 frame 반납 후 swap out(suspend)  
  * thrashing 방지  
  * MPD를 결정함  

<br>

## Working-Set Algorithm
* working set window를 통해 페이지 집합 유추  

* window size Δ인 경우  

* WS(t[j]) = time interval[t[j] - Δ, t[j]] 사이에 참조된 서로 다른 페이지들의 집합  

* working set에 속한 페이지는 메모리에 유지(즉 참조된 후 Δ 시간 동안 해당 페이지 유지)  

<br>

## PFF(Page-Fault Frequency) Scheme

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/09-page-fault-frequency-scheme.png" width="500" height="300">

<br>

* page fault rate 범위 설정  

* 상한값 이상이면 frame 추가 할당(빈 frame 없는 경우 일부 프로세스 swap out)  

<br>

## Page Size 결정
* 페이지 크기 감소  
  * 페이지 수 증가  
  * 페이지 테이블 크기 증가  
  * 내부 조각 감소  
  * disk transfer 효율성 감소(seek/rotation 횟수 증가)  
  * 필요한 정보만 메모리에 로드되며, 메모리 이용이 효율적  
  * Locality 활용 측면에서 부정적
 
<br>

