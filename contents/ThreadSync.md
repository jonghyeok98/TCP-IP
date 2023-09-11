# 스레드 동기화

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
