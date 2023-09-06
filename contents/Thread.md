# 스레드

<br>

### 스레드 생성 방법

<br>

```c
#include<pthread.h>
int pthread_create(
		pthread_t *restrict thread, const pthread_attr_t *restrict attr,
		void *(*start_routine)(void*), void *restrict arg);
// thread: 생성할 스레드의 ID 저장을 위한 변수의 주소 값 전달
//         참고로 스레드는 프로세스와 마찬가지로 스레드 구분을 위한 ID가 부여된다
// attr: 스레드에 부여할 특성 정보의 전달을 위한 매개변수, NULL 전달 시 기본적인
//				특성의 스레드가 생성된다
// start_routine: 스레드의 main 함수 역할을 하는, 별도 실행흐름의 시작이 되는 
//                함수의 주소 값(함수 포인터) 전달
// arg: 세 번째 인자를 통해 등록된 함수가 호출될 때 전달할 인자의 정보를 담고있는
//      변수의 주소 값 전달
```
- 성공 시 0, 실패 시 0 이외의 값 반환

<br>

### 스레드의 종료를 대기하는 join

<br>

```c
int pthread_join(pthread_t thread, void **status);
// thread: 이 매개변수에 전달되는 ID의 스레드가 종료될 때 까지 함수는 반환하지 않는다
// status: 스레드의 main 함수가 반환하는 값이 저장 될 포인터 변수의 주소 값을 전달한다
```
 - 헤더파일 선언 이전에 매크로 `_REENTRANT`를 정의하면 스레드에 불안전한 함수 호출문을 스레드에 안전한 함수의 호출문으로 자동 변경 컴파일 된다.

<br>

### 워커 스레드 모델

<img src = "./images/Thread/WorkerThread.png" width = 600>

<br>

## 스레드 동기화

<br>

### 뮤텍스의 생성과 소멸

<br>

```c
int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *attr);
int pthread_mutex_destroy(pthread_mutex_t *mutex);
// mutex: 뮤텍스 생성시에는 뮤텍스의 참조 값 저장을 위한 변수의 주소 값 전달,
//        그리고 뮤텍스 소멸 시에는 소멸하고자 하는 뮤텍스의 참조 값을 저장하고
//        있는 변수의 주소 값 전달
// attr: 생성하는 뮤텍스의 특성정보를 담고 있는 변수의 주소 값 전달,
//       별도의 특성을 지정하지 않을 경우에는 NULL 전달
```

<br>

### 뮤텍스의 획득과 반환

<br>

```c
int pthread_mutex_lock(pthread_mutex_t *mutex);    // 뮤텍스 획득
int pthread_mutex_unlock(pthread_mutex_t *mutex);  // 뮤텍스 반환

pthread_mutex_lock(&mutex);
pthread_mutex_unlock(&mutex);
```
- lock, unlock 연산은 리소스를 많이 소모하기 때문에 호출 횟수를 최소화하는 것이 좋다

<br>

### 스레드의 종료와 동시에 소멸

<br>

```c
int pthread_detach(pthread_t thread);
// thread: 종료와 동시에 소멸시킬 스레드의 ID 정보 전달
```
- join 함수의 호출은 블로킹 상태에 놓이기 때문에 detach 함수를 호출해서 스레드의 소멸을 도와야한다.
