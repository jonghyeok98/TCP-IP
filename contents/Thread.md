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