# [ 04-process-management ]
> 프로세스 생성 및 종료  
> 프로세스 협력  
> 프로세스 특성 분류  

<br>

## 프로세스 생성
* 부모 프로세스(parent process)가 자식 프로세스(children process) 생성  

* 프로세스의 트리(계층 구조) 형성  

* 프로세스는 자원 필요  
  * 운영체제로부터 할당  
  * 부모와 공유  

* 자원 공유  
  * 부모와 자식이 모든 자월을 공유하는 모델(COW, copy-on-write)  
  * 일부를 공유하는 모델  
  * 전혀 공유하지 않는 모델(일반적 모델)  

* 수행  
  * 부모와 자식은 공존하며 수행되는 모델  
  * 자식이 종료될 때까지 부모가 대기하는 모델  

* 주소 공간  
  * 자식은 부모의 공간을 복사(binary and OS data)  
  * 자식은 그 공간에 새로운 프로그램 올림  

* 유닉스  
  * `fork()` 시스템 콜이 새로운 프로세스 생성  
  * 이후 이어지는 `exec()` 시스템 콜을 통해 새로운 프로그램을 메모리에 올림  

<br>

## 프로세스 종료
* 자발적(`exit`) - 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 보고
  * 자식이 부모에게 output data를 전송(via `wait`)
  * 프로세스의 각종 자원들 운영체제에게 반납

* 비자발적(`abort`) - 부모 프로세스가 자식의 수행 종료시킴
  * 자식이 할당 자원의 한계치 초과
  * 자식에게 할당된 테스크 불필요
  * 부모가 종료(단계적으로 자식 종료 후 부모 종료)

<br>

## Fork System Call
* A process is created by the fork() system call

* Creates a new address space that is a duplicate of the caller

<br>

````c
int main() 
{
    int pid;
    pid = fork();

    /*
     * Return 0 when this process is child
     * Return positive value when this process is parent
     */

    if (pid == 0)
        printf("Child Process");
    else if (pid > 0)
        printf("Parent Process");
}
````

<br>

## Exec System Call
* A process can execute a different program by the exec() system call

* Replaces the memory image of the caller with a new program

<br>

````c
int main() 
{
    int pid;
    pid = fork();
  
    if (pid == 0) 
    {
        printf("Child Process");
    
        // Child process can not comeback this parent's code
        execlp("/bin/path/process", "/bin/path/process", "arguments". (char *)0);
    }
    else if (pid > 0)
        printf("Parent Process");
}
````

<br>

## Wait System Call
* sleep until child is done

* 커널을 자식이 종료될 때까지 부모를 block

* 자식이 종료되면 커널은 부모를 ready

* 리눅스 터미널 커맨드 라인 동작 방식

<br>

````c
int mian() 
{
    int pid;
    pid = fork();
  
    if (pid == 0) 
        ...
    else
        wait();
    ...
}
````

<br>

## Exit System Call
* frees all the resources, notify parent

* 마지막 statement 수행 후 exit() 시스템 콜
* 명시적으로 코드 라인 없어도 main 함수 리턴 위치에 컴파일러 자동 삽입

<br>

## 프로세스 협력
* 독립적 프로세스(independent process)

* 협력 프로세스(cooperating process)

* 협력 메커니즘(IPC, interprocess communication)
  * `message passing`: 커널을 통해 메시지 전달
  * `shared memory`: 일부 주소 공간을 공유

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/04-interprocess-communication.png" width="500" height="400">

<br>

## Message Passing
* 프로세스 사이에 공유 변수를 일체 사용하지 않고 통신하는 시스템
  * Direct Communication - 통신하려는 프로세스의 이름을 명시적으로 표시
  * Indirect Communication - mailbox(또는 port)를 통해 메시지 간접 전달

<br>

<img src="https://github.com/jjeonghak/operating-system/blob/main/md-images/04-message-passing.png" width="500" height="400">

<br>







