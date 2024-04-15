# [ Memory Management ]
> 주소 바인딩  
> 메모리 관리 기법  
> 메모리 재할당 기법  

<br>

## Address

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-address-translation-architecture.png" width="500" height="400">

<br>

1. Logical Address(Virtual Address)  
    * 프로세스마다 독립적으로 가지는 주소 공간  
    * 각 프로세스마다 0번지부터 시작  
    * CPU가 보는 주소  

2. Physical Address  
    * 메모리에 실제 올라가는 위치

<br>

## Address Binding
* 주소를 결정하는 것  

* Symbolic Address -> Logical Address -> Pyhsical Address

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-address-binding.png" width="500" height="400">

<br>

1. Compile time binding  
    * 물리적 메모리 주소가 컴파일 타임에 결정(Logical Address == Physical Address)  
    * 시작 위치 변경시 재컴파일  
    * 컴파일러는 절대 코드(`absolute code`) 생성  

2. Load time binding  
    * Loader의 책임하에 물리적 메모리 주소 부여  
    * 컴파일러가 재배치가능코드(`relocatable code`) 생성한 경우 가능  

3. Execution time binding(Run time binding)  
    * 수행이 시작된 이후에도 프로세스 메모리 상 위치 변경 가능  
    * CPU가 주소를 참조할 때마다 주소 점검(`address mapping table`)  
    * 하드웨어적 지원 필수(base and limit registers, MMU)  

<br>

## Memory-Management Unit(MMU)
* 논리적 주소를 물리적 주소로 매핑하는 하드웨어  

* MMU scheme - 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 base register(relocation regiser) 값을 더함  

* user program - 논리적 주소만 처리, 물리적 주소는 모름  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-dynamic-relocation.png" width="500" height="400">

<br>

## Hardware Support for Address Translation
* 운영체제 및 사용자 프로세스 간의 메모리 보호를 위해 사용하는 레지스터  

* `Relocation register`(base register) - 접근할 수 있는 물리적 메모리 주소 최소값  

* `Limit register` - 논리적 주소의 범위  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-hardware-address-translation.png" width="400" height="300">

<br>

## Terminologies
1. Dynamic Loading  
    * 프로세스 전체를 메모리에 미리 다 올리지 않음  
    * 해당 루틴이 호출될 때 메모리에 로드  
    * 오류처리루틴과 같이 사용빈도가 낮은 코드에 유용  
    * 운영체제 지원없이 프로그램 자체에서 라이브러리로 구현 가능  

2. Dynamic Linking
    * Linking을 실행 시간까지 미루는 기법  
    * 실행파일 안에 `stub` 코드 존재(라이브러리 루틴 위치 포인터)  
    * 운영체제 지원 필요  

3. Overlays(Manual Overlay)  
    * 메모리에 프로세스 부분 중 실제 필요한 정보만 올림  
    * 프로세스 크기가 메모리보다 큰 경우 유용  
    * 운영체제 지원없이 프로그램 자체에서 구현  
    * 메모리 공간이 협소한 초창기 시스템에서 수작업으로 프로그래머가 직접 구현  

4. Swapping  
    * 프로세스를 일시적으로 `backing store`(swap area)로 쫓아냄  
    * run time binding 지원하는 경우 효과적  
    * swap time의 대부분이 transfer time  

<br>

## Allocation of Physical Memory
* 메모리는 OS 상주영역과 사용자 프로세스 영역으로 나누어 사용  

* 사용자 프로세스 영역 할당 방법  
  1. Contiguous allocation(연속 할당)  
  2. Noncontiguous allocation(불연속 할당)  

<br>

## Contiguous Allocation
* 각각의 프로세스가 메모리의 `연속적인 공간`에 적재  

* `External fragmentation`(외부조각)  
  * 프로그램 크기보다 분할 크기가 작은 경우  
  * 아무 프로그램에도 배정되지 않은 빈 곳이지만 프로그램이 올라갈 수 없는 작은 분할  

* `Internal fragmentation`(내부조각)  
  * 프로그램 크기보다 분할의 크기가 큰 경우  
  * 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각  
  * 특정 프로그램에 배정되었지만 사용되지 않는 공간  

* `Hole`  
  * 가용 메모리 공간  
  * 프로세스가 도착하면 수용가능한 hole을 할당  
  * 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 존재  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-contiguous-allocation.png" width="500" height="300">

<br>

1. Fixed partition allocation(고정분할)  
    * 물리적 메모리를 영구적 분할로 나눔  
    * 분할당 하나의 프로그램 적재  
    * 융통성 없음  
    * 외부조각, 내부조각 발생  

2. Variable partition allocation(가변분할)  
    * 프로그램 크기를 고려해서 할당  
    * 분할 크기, 개수가 동적으로 변경  
    * 외부조각 발생  

<br>

## Contiguous Allocation - Dynamic Storage-Allocation Problem
* 가변 분할 방식에서 크기 n인 요청을 만족하는 가장 적절한 hole 탐색  

* First-fit - 크기가 n 이상 중 최초로 발견된 것 할당  

* Best-fit - 크기가 n 이상인 가장 작은 것 할당  

* Worst-fit - 가장 큰 것 할당  

<br>

## Contiguous Allocation - Compaction
* 외부조각 문제를 해결하는 방법  

* 사용중인 메모리 영역을 한 곳으로 몰고 hole들을 다른 한곳으로 모아서 큰 block 형성  

* run time binding 지원하는 경우만 사용 가능  

* 매우 많은 비용 발생  

<br>

## Noncontiguous allocation
* 하나의 프로세스가 메모리의 여러 영역에 `분산`되어 올라감
  1. Paging - 프로세스와 메모리를 일정한 작은 단위로 분할  
  2. Segmentation - 프로세스와 메모리를 의미 단위로 분할  
  3. Paged Segmentation  

<br>

## Paging

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-paging.png" width="500" height="400">

<br>

* 프로세스의 가상 메모리를 동일한 사이즈의 `page` 단위로 분할  

* 가상 메모리의 내용이 페이지 단위로 `noncontiguous`하게 저장  

* 일부는 backing storage에, 일부는 물리적 메모리에 저장  

* Basic Method  
> 물리적 메모리를 동일한 크기의 `frame`으로 분할  
> 논리적 메모리를 동일한 크기의 `page`로 분할(frame과 동일한 크기)  
> 모든 가용 frame들을 관리  
> page table을 사용해서 논리적 주소를 물리적 주소로 변환  
> 외부조각 발생안함, 내부조각 발생 가능(프로세스 가상 메모리가 page의 배수가 아닌 경우)  

<br>

## Implementation of Page Table
* 페이지 테이블은 메인 메모리에 상주  

* `Page-table base register`(PTBR) - page table 가리킴  

* `Page-table length register`(PTLR) - 테이블 크기를 보관  

* 모든 메모리 접근 연산에는 2번의 메모리 접근이 필요  

* page table 접근, 실제 data/instruction 접근  

* 속도 향상을 위해 associative register 또는 translation look-aside buufer(`TLB`) 하드웨어 캐시 사용  

<br>

## TLB - Associative Register

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-translation-lookaside-buffer.png" width="600" height="500">

<br>

* TLB의 경우 page talbe의 모든 엔트리를 저장할 수 없어 일부만 존재(캐시)  

* 병렬 탐색이 가능  

* page table 중 일부가 associative register에 보관되어 있음  

* 만약 해당 page #가 associative register에 있는 경우 곧바로 frame # 얻음  

* 아닌 경우 메인 메모리에 있는 page talbe로부터 랜덤 엑세스  

* TLB는 context switch 때 flush(remove old entries)  
> Associative register lookup time = ε  
> memory cycle time = 1  
> Hit ratio(associative register 탐색 가능한 비율) = α  
> effective access time(EAT) = &lt;hit&gt; (1 + ε)α + &lt;miss&gt; (2 + ε)(1 - α) = 2 + ε - α  

<br>

## Two-Level Page Table

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-two-level-page-table.png" width="500" height="500">

<br>

* 현대 컴퓨터는 주소 공간이 매우 큰 프로그램 지원  

* 접근 시간은 좀 더 걸리지만 메모리 효율성 증가  

* 32 bit 주소 체계  
  * 2^32B(4GB)  
  * 페이지 엔트리 4K  
  * 1M개의 페이지 테이블 엔트리 필요  
  * 페이지 테이블 공간 낭비  

* page table 자체를 page로 구성  

* 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 null(inner table 생성 안함)  

* logical address(on 32-bit machine with 4K page size)  
  * 20bit page number(10-bit page number, 10-bit page offset)  
  * 12bit page offset  

<br>

## Multilevel Paging and Performance
* 주소 공간이 더 커지면 다단계 페이지 테이블 필요  

* 각 단계의 페이지 테이블이 메모리에 존재하기 때문에 더 많은 메모리 접근 필요  

* 이 경우 TLB 사용이 효과적  

* 4단계 페이지 테이블 사용하는 경우  
  * 메모리 접근 시간 100ns, TLB 접근 시간 20ns  
  * TLB hit ratio 98%인 경우  
  * effective memory access time = 0.98 * 120 + 0.02 * 520 = 128ns  

<br>

## Memory Protection
* protection bit - page 접근 권한(read/write/read-only)  

* valid-invalid bit  
  * valid - 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용 존재  
  * invalid - 해당 주소의 frame에 유효한 내용이 없음(접근 불허)  

<br>

## Inverted Page Table

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-inverted-page-table-architecture.png" width="500" height="400">

<br>

* 페이지 테이블이 매우 큰 경우  
  * 모든 프로세스 별로 그 논리 주소에 대응하는 모든 페이지에 대해 페이지 테이블 엔트리 존재  
  * 대응하는 페이지가 메모리에 있든 아니든 간에 페이지 테이블에는 엔트리로 존재  

* Inverted page table  
  * 페이지 frame 하나당 페이지 테이블에 하나의 엔트리를 둔 것(system-wide)  
  * 각 페이지 테이블 엔트리는 각각의 물리적 메모리의 페이지 frame이 담고 있는 내용 표시  
  * 랜덤 엑세스 불가능(완전 탐색 필요)  

<br>

## Shared Page

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-shared-pages.png" width="500" height="400">

<br>

* Shared code  
  * `Re-entrant code`(pure code) 재진입 가능 코드  
  * read-only로 하여 프로세스 간에 하나의 code만 메모리에 올림  
  * 모든 프로세스의 논리 주소 공간에서 동일한 위치에 있어야함  

* Private code and data  
  * 각 프로세스들은 독자적으로 메모리에 올림  
  * private data는 논리 주소 공간의 아무 곳에 와도 무관  

<br>

## Segmentation

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-segment.png" width="600" height="500">

<br>

* 프로그램은 의미 단위인 여러개의 `segment`로 구성  

* 작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의  

* 크게는 프로그램 전체를 하나의 세그먼트로 정의  

* 일반적으로 code, data, stack 부분이 하나씩 세그먼트로 정의  

* Logical unit
  * main(), function, global variables, stack, symbol table, arrays  

<br>

## Segmentation Architecture

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-segmentation-hardware.png" width="400" height="300">

<br>

* 페이징보다 테이블 크기가 작음  

* 논리 주소는 `segment-number`, `offset`으로 구성  

* Segment table - 테이블의 각 엔트리는 `base`, `limit` 존재  

* Segment-table base register(STBR) - 물리적 메모리의 segment table 위치  

* Segment-table length register(STLR) - 프로그램이 사용하는 segment 수  

* Protction - 각 세그먼트 별로 protection bit 존재  

* Sharing - 세그먼트는 의미 단위이기 때문에 공유와 보안에 있어서 페이징보다 효과적  

* Allocation - 세그먼트 길이가 동일하지 않으므로 가변분할 방식에서와 동일한 문제점 발생  

<br>

## Shared Segmentation

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-sharing-segments.png" width="500" height="500">

<br>

## Segmentation with Paging

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/08-segment-with-paging.png" width="500" height="400">

<br>

* 의미 단위인 segment를 여러개의 page로 분할  

* segment-table entry가 segment의 base address를 가지고 있는 것이 아님  

* segment를 구성하는 page table의 base address를 가짐  

* 공유와 보안은 세그먼트에서, 실제 물리적 메모리는 페이지로 로드  

<br>

