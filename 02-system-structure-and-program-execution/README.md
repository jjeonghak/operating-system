# [ 02-system-structure-and-program-execution ]
> 컴퓨터 시스템 구조  
> 동기식 및 비동기식 입출력  
> 시스템 콜  

<br>

## 컴퓨터 시스템 구조
1. Computer  
    * CPU  
      Memory 인터럽트를 하나씩 읽어서 실행, 이후 intterrupt line 확인  
      Memory보다 빠르면서 저장가능한 `registers` 보유  
      `mode bit`을 통해 CPU 제어권이 현재 운영체제가 소유한 것인지 판별  
      `interrupt line`를 통해 운영체제에게 제어권 넘길지, 다음 인터럽트 처리할지 결정  
      `timer`를 통해 무한 루프같이 한 프로그램이 CPU 독점 방지  

    * Memory  
    사용자 프로그램과 운영체제 저장  
    `memory controller`를 통해 CPU와 DMA contoller만 접근 가능(동시 접근 제어)  
    사용자 프로그램이 I/O 디바이스와 정보 통신이 필요한 경우 운영체제에 CPU 제어권 반납, I/O 디바이스 접근은 오직 운영체제를 통해서만 가능  
    운영체제는 device controller에게 작업 요청  

2. I/O device  
    컴퓨터와 정보를 통신하는 디바이스  
    각 디바이스마다 `device controller`와 `local buffer` 연결  
    Disk, Mouse, Keyboard, Monitor 등  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/02-computer-system-structure.png" width="600" height="500">

<br>

## Mode bit
* 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해 방지 목적 보호 장치  
* 보안을 해칠 가능성 있는 명령어는 모니터 모드에서만 수행 가능한 `특권명령`으로 규정  
* 운영체제는 사용자 프로그램에 CPU 제어권 넘길때 mode bit 1로 변경  
  `1` 사용자 모드: 사용자 프로그램 수행, 일반명령만 수행 가능  
  `0` 모니터 모드(커널 모드, 시스템 모드): OS 코드 수행, 일반명령 및 특권명령 수행 가능  

<br>

## Timer
* 정해진 시간이 후 운영체제에게 CPU 제어권 넘어가도록 인터럽트 발생  
* 타이머는 매 클럭 틱 때마다 1씩 감소  
* 타이머 값 0이 되면 타이머 인터럽트 발생  
* time sharing 구현에 필수적  
* 현재 시간 계산을 위해서도 사용  

<br>

## Device Controller
* 해당 I/O 장치 유형을 관리하는 일종의 작은 CPU  
* 제어 정보를 위해 control register, status register 보유  
* local buffer 보유(일종의 data register), I/O 작업 결과 저장  
* I/O 작업 종료시 인터럽트를 통해 CPU에 통보  
  * device controller(장치제어기): 각 장치를 통제하는 일종의 작은 CPU, `hardware`  
  * device driver(장치구동기): OS 코드 중 각 장치별 처리루틴, `software`  

<br>

## DMA(Direct Memory Access) controller
* I/O 디바이스의 인터럽트가 높은 빈도로 발생할때 CPU 부담을 줄이기 위해서 존재  
* I/O 디바이스 작업 결과를 직접 읽어서 메인 메모리에 복사  
* CPU 중재 없이 device controller가 device의 buffer storage의 내용을 메모리에 `block` 단위로 직접 전송  
* 메인 메모리에 결과 복사 후 CPU에 인터럽트로 보고  
* 바이트 단위가 아닌 block 단위로 인터럽트 발생  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/02-direct-memory-access.png" width="400" height="400">

<br>

## I/O 수행
* 모든 입출력 명령은 특권명령  
* `시스템콜`(system call): 사용자 프로그램은 운영체제에게 I/O 요청  
* `트랩`을 사용하여 인터럽트 벡터의 특정 위치로 이동  
* 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동  

<br>

## Interrupt
* 인터럽트: 인터럽트 당한 시점의 레지스터와 program counter를 저장한 후 CPU 제어를 인터럽트 처리 루틴에 할당  
  * interrupt: 하드웨어가 발생시킨 인터럽트  
  * trap: 소프트웨어가 발생시킨 인터럽트  
  * 인터럽트 벡터: 해당 인터럽트의 처리 루틴 주소를 보유  
  * 인터럽트 처리 루틴(인터럽트 핸들러): 해당 인터럽트를 처리하는 커널 함수  

<br>

## System Call
* 사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것

<br>

## Synchronous & Asynchronous
1. Synchronous I/O
    * I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램으로 이동  
    * 인터럽트를 통해 I/O 작업 종료 확인  

2. Asynchronous I/O
    * I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램으로 즉시 이동  
    * 인터럽트를 통해 I/O 작업 종료 확인  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/02-sync-async.png" width="600" height="400">

<br>

## 저장장치 계층 구조
<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/02-storage-device-layer.png" width="600" height="500">

<br>

## 프로그램 실행(메모리 로드)
* 프로그램은 하드디스크의 file system 부분(`비휘발성`)의 실행 파일 형태로 저장  
* 실행 파일이 가상 메모리(`virtual memory`)를 거처 물리적 메모리(`physical memory`)에 올라가 프로세스 형태로 실행  
* 실질적으로 실행에 필요한 부분만 물리적 메모리에 보관, 불필요한 부분은 하드디스크의 swap area 부분(`휘발성`)에 보관  

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/02-memory-load.png" width="600" height="500">

<br>

## 커널 주소 공간
* 일반 프로그램과 같이 code, data, stack 영역 존재

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/02-kernel-address.png" width="600" height="400">

<br>

## 프로그램 실행

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/02-program-excution.png" width="600" height="400">

<br>

