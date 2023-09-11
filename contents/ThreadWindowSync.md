# windows에서의 스레드 동기화

<br>

### 동기화 기법의 분류와 critical section 동기화
- 유저모드: 응용 프로그램이 실행되는 기본모드로, 물리적인 영역으로의 접근이 허용되지 않으며 접근할 수 있는 메모리의 영역에도 제한이 따른다
- 커널모드: 운영체제가 실행될 때의 모드로, 메모리 뿐만 아니라 하드웨어의 접근에도 제한이 따르지 않는다
- 유저모드는 응용프로그램의 실행모드, 커널모드는 운영체제의 실행모드이다

<br>

### 유저모드 동기화
- 운영체제에 의해서 이뤄지는 동기화가 아닌 순수 라이브러리에 의존해서 완성되는 동기화 기법
- 운영체제에 의해서 제공되지 않으므로 커널모드로의 전환이 불필요하다. 따라서 상대적으로 가볍고 속도가 빠르다

<br>

### 커널모드 동기화
- 커널에 의해서 제공되는 동기화 기법
- 커널에 의해 제공되는 동기화이기 때문에 다양한 기능이 제공된다
- e.g) deadlock에 걸리지 않도록 타임아웃을 지정할 수 있다

<br>

### critical section 오브젝트의 생성과 소멸

<br>

```c
#include<windows.h>

void InitialzizeCriticalSection(LPCRITICAL_SECTION lpCriticalSection);
void DeleteCriticalSection(LPCRITICAL_SECTION lpCriticalSection);
// lpCriticalSection: 초기화 혹은 삭제할 critical section 오브젝트의 주소 값
```

<br>

### critical section 오브젝트의 획득과 반납

<br>

```c
#include<windows.h>

void EnterCriticalSection(LPCRITICAL_SECTION lpCriticalSection);
void LeaveCriticalSection(LPCRITICAL_SECTION lpCriticalSection);
// lpCriticalSection: 획득 혹은 반납할 critical section 오브젝트의 주소 값
```
- critical section 오브젝트는 커널 오브젝트가 아니기 때문에 유저모드에서만 작동한다

<br>

### critical section 동기화 예시

<br>

```c
#include<windows.h>

long long num=0;
CRITICAL_SECTION cs;

unsinged WINAPI threadInc(void *arg);
unsinged WINAPI threadDes(void *arg);

int main(int argc, char *argv[])
{
	HANDLE tHandles[NUM_THREAD];
	int i;
	
	InitializeCriticalSection(&cs);
	for(i=0; i<NUM_THREAD; i++)
	{
		if(i%2)
			tHandles[i]=(HANDLE)_beginthreadex(NULL, 0, threadInc, NULL, 0, NULL);
		else
			tHandles[i]=(HANDLE)_beginthreadex(NULL, 0, threadDes, NULL, 0, NULL);
	}

	WaitForMultipleObjects(NUM_THREAD, tHandles, TURE, INFINITE);
	DeleteCriticalSection(&cs);
	printf("result: %lld \n", num);
	return 0;
}

unsinged WINAPI threadInc(void *arg)
{
	int i;
	EnterCriticalSection(&cs);
	for(i=0; i<50000000; i++)
		num+=1;
	LeaveCriticalSection(&cs);
	return 0;
}
unsinged WINAPI threadDes(void *arg)
{
	int i;
	EnterCriticalSection(&cs);
	for(i=0; i<50000000; i++)
		num-=1;
	LeaveCriticalSection(&cs);
	return 0;
}
```

<br>

