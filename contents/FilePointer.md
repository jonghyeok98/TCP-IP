# 파일 포인터와 파일 디스크립터

<br>

## 파일 포인터
> 파일 포인터는 FILE* 타입의 포인터 변수를 말하며, C표준 라이브리러에서 사용하는 I/O 개체로 스트림 기반으로 동작한다

<br>

### fopen() 함수

``` c
FILE* fp = fopen("input-file", "r");
```
> fopen() 라이브러리 함수를 호출하면 반환 값으로 FILE 포인터를 얻을 수 있다

<br>

 * 이용자 파일 기술자 표 구조체 안에서 파일 구조체를 가리키는 포인터이다
 * stdin: 표준 입력
 * stdout: 표준 출력
 * stderr: 표준 에러

<br>

### 파일 디스크립터를 통해 파일 포인터를 얻을 수 있다
    
 ```c
int fd = open(”input-file”, O_RDONLY);
FILE* fp = fdopen(fd, “w”);
```

<br>
    
### fdopen()을 이용하여 소켓의 파일 포인터를 얻을 수 있다
    
```c
readfp=fdopen(sock, "r");
writefp=fdopen(sock, "w");
```

---
<br>

## 파일 디스크립터
> int 타입으로 선언되는 고유 식별 번호이며, 각 OS별로 제공되는 I/O system call에서 각 I/O 자원을 구별하기 위해 사용된다

<br>

### open()
    
```c
int fd = open(”input-file”, O_RDONLY);
```
> open() 시스템 콜을 호출하면 반환 값으로 파일 디스크립터를 얻을 수 있다

<br>
    
- 파일 디스크립터 표의 구조체 중에서 몇 번째를 할당했는지를 나타내는 번호를 말한다
- 0: 표준 입력
- 1: 표준 출력
- 2: 표준 에러 출력
- 3번 부터 할당을 한다
- read(), write(), close(), fcntl() 등이 있으며 이를 저수준 표준 입출력 함수라고 부른다(Standard Input Output Function)

<br>

### 파일 포인터가 할당되면 간단하게 파일 디스크립터를 구할 수 있다
    
```c
int fileno(FILE* stream);
    fd=fileno(fp)
```
