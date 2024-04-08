# [ Process Synchronization(Concurrency Control) ]
> 데이터 접근  
> 레이스 컨디션  
> 세마포어  
> 모니터  

<br>

## Race Condition
* 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황

* 데이터의 최종 연산 결과는 마지막에 접근한 프로세스에 의해 결정

* 커널 수행 중 인터럽트 발생하는 경우

* 프로세스가 시스템콜을 하여 커널 모드로 수행중인데 문맥교환이 일어나는 경우

* 멀티 프로세서에서 공유 메모리 내의 커널 데이터

<br>

## Critical-Section Problem
* n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우

* 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section 존재

* 다른 프로세스가 critical section 수행중이면 다른 프로세스는 대기

<br>

## 충족 조건
1. Mutual Exclusion(`상호 배제`)
    * 프로세스가 critical section 부분을 수행중이면 다른 프로세스 대기

2. Progress(`진행`)
    * 어떤 프로세스도 critical section 부분을 수행하지 않고 있다면 접근 허용

3. Bounded Waiting(`유한 대기`)
    * 어떤 프로세스가 critical section 부분에 접근하려고 요청한 후부터 허용되는 순간까지 다른 프로세스의 접근 횟수 한계가 존재

<br>

## Initial Attempts to Solve Problem

### 일반적인 프로세스 구조
````c
do {
    entry section
    critical section
    exit section
    remainder section
} while (1);
````

<br>

* Synchronization variable  
  * int turn = Pi can enter its critical section `if (turn == i)`  
  * boolean flag[i] = Pi ready to enter its critical section `if (flag[i] == true)`  

<br>

### Wrong Algorithm1
````c
// Not satisfies progress requirement
do {
    while (turn != 0);
    critical section
    turn = 1;
    remainder section
} while (1);
````

<br>


### Wrong Algorithm2
````c
// Not satisfies progress requirement
do {
    flag[i] = true;
    while (flag[j]);
    critical section
    flag[i] = false;
    remainder section
} while (1);
````

<br>

### Peterson's Algorithm
````c
// Busy Waiting(Spin Lock) - 지속적인 CPU/Memory 사용
do {
    flag[i] = true;
    turn = j;
    while (flag[j] && turn == j);
    critical section
    flag[i] = false;
    remainder section
} while (1);
````

<br>

## Synchronization Hardware
* 하드웨어적으로 Atomic Test & Modify(Test_and_Set) 지원을 하는 경우 간단하게 해결  

* Synchronization variable  
  * boolean lock = false;  

<br>

````c
do {
    // Atomic Read and Write method
    while (Test_and_Set(lock));
    critical section
    lock = false;
    remainder section
} while (1);
````

<br>

## Semaphores
* 위의 방식들을 추상화

* 정수 형식, 두 가지 Atomic 연산에 의해서만 접근 가능

* Busy Waiting(spin lock) 발생, Block & Wakeup(sleep lock) 방식 구현 가능

<br>

1. P(S) 연산(공유 변수 접근)
````c
void P(semaphore mutex) 
{
    while (mutext <= 0);
    mutext--;
}
````

<br>

2. V(S) 연산(공유 변수 반납)
````c
void S(semaphore mutex) 
{
    mutext++;
}
````

<br>

## Semaphores Block & Wakeup Implementation
````c
typedef struct
{
    int value;
    struct process *L
} semaphore;

void P(semaphore s)
{
    s.value--;
    if (mutex.value < 0)
    {
        add this process to s.L;
      block();
    }
}

void V(semaphore s)
{
    s.value++;
    if (s.value <= 0)
    {
        remove a process P from s.L;
        wakeup(P);
    }
}
````

<br>

## Types of Semaphores
1. Counting semaphore  
    * 도메인이 0 이상인 임의의 정수값  
    * 주로 resource counting에 사용  

3. Binary semaphore(mutex)  
    * 0 또는 1 값만 가질 수 있는 semaphore  
    * 주로 mutual exclusion(lock/unlock)에 사용  

<br>


## Semaphores - Dining-Philosophers Problem
* Synchronization variables  
  * semaphore chopstic[5];  

<br>

````c
// Philosopher i
do {
    P(chopstick[i]);
    P(chopstick[(i + 1) % 5]);
    
    ...
    eat();
    ...
    
    V(chopstick[i]);
    V(chopstick[(i + 1) % 5]);
    
    ...
    think();
    ...
} while (1);
````

<br>

* Synchronization variables  
  * enum { thinking, hungry, eating } state[5]  
  * semaphore self[5]  
  * semaphore mutex  

<br>

````c
void test(int i)
{
    if (state[(i + 4) % 5] != eating && state[i] == hungry && state[(i + 1) % 5] != eating)
    {
        state[i] = eating;
        V(self[i]);
    }
}

void pickup(int i)
{
    P(mutex);
    state[i] = hungry;
    test(i);
    V(mutex);
    P(self[i]);
}

void putdown(int i)
{
    P(mutex);
    state[i] = thinking;
    test((i + 4) % 5);
    test((i + 1) % 5);
    V(mutex);
}

void philosopher(int i) 
{
    do {
        pickup(i);
        eat();
        putdown();
        think();
    } while (1);
}
````

<br>

## Semaphores - Bounded-Buffer Problem(Producer-Consumer Problem)
* 유한한 공유 데이터 버퍼를 사용할 때 발생하는 문제

* 생산자와 소비자 측으로 문제 발생  

* Shared data  
  * buffer  
  * buffer variables(empty/full buffer point)  

* Synchronization variables  
  * mutual exclusion - need binary semaphore  
  * resource count - need integer semaphore  

<br>

````c
// Producer
do {
    produce an item in x
  
    P(empty);
    P(mutex);
  
    ...
    add x to buffer
    ...
  
    V(mutex);
    V(full);
} while (1);

// Consumer
do {
    P(full);
  
    ...
    remove an item from buffer to y
    ...
  
    V(mutex);
    V(empty);
    consume the item in y
} while (1);
````
<br>

## Semaphores - Readers-Writers Problem
* 한 프로세스가 데이터베이스에 write 동작하는 도중에 다른 프로세스가 접근 금지

* read 작업은 동시에 여럿이 가능

* 이미 read 작업중이라면 write 작업 대기(기아 현상 발생)

* Shared data  
  * Database  
  * readcount  

* Synchronization variables  
  * db - for Database  
  * mutex - for readcount  

<br>

````c
void writer() 
{
    P(db);
    
    ...
    writing Database is performed
    ...
  
    V(db);
}

void Reader() 
{
    P(mutex);
    readcount++;
    if (readcount == 1) P(db);
    V(mutex);
  
    ...
    reading Database is performed
    ...
  
    P(mutex);
    readcount--;
    if (readcount == 1) V(db);
    V(mutex);
}
````

<br>

## Monitor
* Semaphore의 객체 지향적 버전

* 동시 수행중인 프로세스 사이에서 안전한 공유 보장을 위한 high-level synchronization construct

<br>

````c
monitor monitor-name
{
    shared variable declarations
  
    procedure body P1(...) { ... }
    procedure body P2(...) { ... }
    procedure body Pn(...) { ... }
    
    {
        initialization code
    }
}
````

<br>

* 모니터 내에서는 한번에 하나의 프로세스만 활동 가능

* 프로그래머가 동기화 제약 조건을 명시적으로 프로그래밍할 필요없음

* 프로세스가 모니터 안에서 대기하도록 condition varialbe 사용

<br>

1. c.wait();
    * 프로세스를 suspend 상태로 변경

2. c.signal();
    * 하나의 suspend된 프로세스를 resume

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/06-monitor.png" width="500" height="400">

<br>

## Monitor - Dining-Philosophers Problem

````c
monitor dining_philosopher
{
    enum { thinking, hungry, eating } state[5];
    condition self[5];
  
    void init()
    {
        for (int i = 0; i < 5; i++)
            state[i] = thinking;
    }
  
    void test(int i)
    {
        if (state[(i + 4) % 5] != eating && state[i] == hungry && state[(i + 1) % 5] != eating)
        {
            state[i] = eating;
            self[i].signal();
        }
    }
  
    void pickup(int i)
    {
        state[i] = hungry;
        test(i);
        if (state[i] != eating)
            self[i].wait();
    }
  
    void putdown(int i)
    {
        state[i] = thinking;
        test((i + 4) % 5);
        test((i + 1) % 5);
    }
}

Each Philosopher:
{
    pickup(i);	// Enter monitor
    eat();
    putdown(i);	// Enter monitor
    think();
} while (1)
````

<br>

## Monitor - Bounded-Buffer Problem

````c
monitor bounded_buffer
{
    int buffer[N];
    condition full, empty;
  
    void produce(int x)
    {
        if there is no empty buffer
            empty.wait();
        add x to an empty buffer
        full.signal();
    }
  
    void consume(int *x)
    {
        if there is no full buffer
            full.wait();
        remove an item from buffer and store it to *x
        empty.signal();
    }
}
````

<br>


