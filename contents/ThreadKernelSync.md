# 커널모드 동기화 기법

<br>

### mutex 오브젝트의 생성과 소멸

<br>

```c
#include<windows.h>

HANDLE CreateMutex(
		LPSECURITY_ATTRIBUTES lpMutexAttributes, BOOL bInitialOwner, LPCTSTR lpName);
// lpMutexAttributes: 보안관련 특성 정보의 전달, 디폴트 보안설정을 위해 NULL 전달
// bInitialOwner: TURE 전달 시, 생성되는 mutex 오브젝트는 이 함수를 호출한 스레드의
//                소유가 되면서 non-signaled 상태가 된다. 반면 FALSE 전달 시, 생성되는
//                mutex 오브젝트는 소유자가 존재하지 않으며, signaled 상태로 생성된다
// lpName: mutex 오브젝트에 이름을 부여할 때 사용된다. NULL 전달 시 이름 없는 뮤텍스 오브젝트 생성

BOOL CloseHandle(HANDLE hObject);
// hObjcet: 소멸하고자 하는 커널 오브젝트의 핸들 전달
// 성공 시 TRUE, 실패 시 FALSE 반환

BOOL ReleaseMutex(HANDLE hMutex);
// hMutex: 소유를 해제할 mutex 오브젝트의 핸들 전달
// 성공 시 TRUE, 실패 시 FALSE 반환
```
> 성공 시 생성된 mutex 오브젝트 핸들, 실패 시 NULL 반환

 -mutex는 auto-reset 모드 커널 오브젝트이다 

<br>

### mutex 오브젝트의 획득과 반납

<br>

```c
WaitForSingleObject(hMutex, INFINITE);
// 임계영역의 시작
// TODO
// 임계영역 종료
ReleaseMutex(hMutex);
```
- mutex 오브젝트는 커널 오브젝트이기 때문에 획득을 위한 별도의 함수가 없어서 WaitForSignalObject 함수를 이용한다
- 즉, mutex 오브젝트는 획득 가능한 상태가 되면 signaled 상태가 된다

<br>

### mutex 기반의 동기화 예시

<br>

```c
#include<windows.h>

long long num=0;
HANDLE hMutex;

unsinged WINAPI threadInc(void *arg);
unsinged WINAPI threadDes(void *arg);

int main(int argc, char *argv[])
{
	HANDLE tHandles[NUM_THREAD];
	int i;
	
	hMutex=CreateMutex(NULL, FLASE, NULL);
	for(i=0; i<NUM_THREAD; i++)
	{
		if(i%2)
			tHandles[i]=(HANDLE)_beginthreadex(NULL, 0, threadInc, NULL, 0, NULL);
		else
			tHandles[i]=(HANDLE)_beginthreadex(NULL, 0, threadDes, NULL, 0, NULL);
	}

	WaitForMultipleObjects(NUM_THREAD, tHandles, TURE, INFINITE);
	CloseHandle(hMutex);
	printf("result: %lld \n", num);
	return 0;
}

unsinged WINAPI threadInc(void *arg)
{
	int i;
	WaitForSingleObject(hMutex, INFINITE);
	for(i=0; i<50000000; i++)
		num+=1;
	RealseMutex(hMutex);
	return 0;
}
unsinged WINAPI threadDes(void *arg)
{
	int i;
	WaitForSingleObject(hMutex, INFINITE);
	for(i=0; i<50000000; i++)
		num-=1;
	RelseMutex(hMutex);
	return 0;
}
```

<br>

### semaphore 오브젝트 기반 동기화

<br>

```c
#include<windows.h>

HANDLE CreateSemaphore(
		LPSECURITY_ATTRIBUTES lpSemaphoreAttributes, LONG lInitialCount,
		LONG lMaximumCount, LPCTSTR lpName);
// lpSemaphoreAttributes: 보안관련 정보의 전달, 디폴트 보안설정을 위해서 NULL 전달
// lInitialCount: 세모포어의 초기 값 지정, 매개변수 lMaximumCount에 저장된 값보다 크면 안되고 0 이상이어야 한다
// lMaximumCount: 최대 세마포어의 값 지정, 1을 전달하면 바이너리 세마포어가 구성된다
// lpName: 세마포어 오브젝트의 이름을 부여할 떄 생성된다 NULL 전달 시 이름 없는 세마포어 오브젝트 생성

BOOL ReleaseSemaphore(HANDLE hSemaphore, LONG lReleaseCount, LPLONG lpPreviousCount);
// hSemaphore: 반납할 세마포어 오브젝트 핸들 전달
// lReleaseCount: 반납은 세마포어 값의 증가를 의미하는데 이 매개변수를 통해서 증가되는 값의 크기를 지정할 수 있다. 
//                세마포어의 최대 값을 넘어서면 증가하지 않고 FALSE를 반환한다
// lpPreviousCount: 변경 이전의 세마포어 값 저장을 위한 변수의 주소 값 전달.
//                  불필요하다면 NULL 전달
```
> 성공 시 생성된 세마포어 오브젝트의 핸들, 실패 시 NULL 반환
 - 세마포어 값이 0보다 큰 경우 non-signaled 상태가 되고 0보다 크면 signaled 상태가 된다

<br>

### 세마포어 동기화 예시

<br>

```c
#include<windows.h>

unsigned WINAPI Read(void *arg);
unsigned WINAPI Accu(void *arg);

int num;

int main(int argc, char *argv[])
{
	HANDLE hThread1, hThread2;
	semOne=CreateSemaphore(NULL, 0, 1, NULL);  // 먼저 실행 될 스레드
	semTow=CreateSemaphore(NULL, 1, 1, NULL);  // 나중에 실행 될 스레드
	
	hThread1=(HANDLE)_beginthreadex(NULL, 0, Read, NULL, 0, NULL);
	hThread2=(HANDLE)_beginthreadex(NULL, 0, Accu, NULL, 0, NULL);

	WaitForSingleObject(hThread1, INFINITE);
	WaitForSingleObject(hThread2, INFINITE);

	CloseHandle(semOne);
	CloseHandle(semTow);
	return 0;
}

unsigned WINAPI Read(void *arg)
{
	int i;
	for(i=0; i<5; i++)
	{
		fputs("Input num: ", stdout);
		WaitForSingleObject(semTwo, INFINITE);
		scanf("%d", &num);
		ReleaseSemaphore(semOne, 1, NULL);
	}
	return 0;
}

unsigned WINAPI Accu(void *arg)
{
	int sum=0, i;
	for(i=0; i<5; i++)
	{
		WaitForSingleObject(semOne, INFINITE);
		sum+=num;
		ReleaseSemaphore(semTwo, 1, NULL);
	}
	printf("Result: %d \n", sum);
	return 0;
}
```
> 두 개의 세마포어 오브젝트를 생성해서 두 스레드의 실행 순서를 동기화 하고 있다

<br>

### Event 오브젝트 기반 동기화

<br>

```c
#include<windows.h>

HANDLE CreateEvent(
		LPSECURITY_ATTRIBUTES lpEventAttributes, BOOL bManualRest,
		BOOL bInitiaulState, LPCTSTR lpName);
// lpEventAttributes: 보안관련 정보의 전달, 디폴트 보안설정을 위해 NULL 전달
// bManualRest: TRUE 전달 시 manual-reset 모드 Event,
//              FALSE 전달 시 auto-reset 모드 Event 오브젝트 생성
// bInitiaulState: TRUE 전달 시 signaled 상태의 Event 오브젝트 생성,
//                 FALSE 전달 시 non-signaled 상태의 Event 오브젝트 생성
// lpName: Event 오브젝트에 이름을 부여할 떄 사용된다. NULL 값을 전달하면 이름 없는 Event 오브젝트 생성

BOOL ResetEvent(HANDLE hEvent); // non-signaled로 변환
BOOL SetEvent(HADNLE hEvent); // signaled로 변환
```
> 성공 시 생성된 Event 오브젝트 핸들, 실패 시 NULL 반환
 - event 오브젝트는 manual-reset 모드 커널 오브젝트이다.

<br>

### event 기반 동기화의 예시

<br>

```c
#include<windows.h>

unsigned WINAPI NumberOfA(void *arg);
unsigned WINAPI NumberOfOthers(void *arg);

static char str[STR_LEN];
static HANDLE hEvent;

int main(int argc, char *argv[])
{
	HANDLE hThread1, hThread2;
	hEvent=CreateEvent(NULL, TRUE, FALSE, NULL);
	hThread1=(HANDLE)_beginthreadex(NULL, 0, NumberOfA, NULL, 0, NULL);
	hThread2=(HANDLE)_beginthreadex(NULL, 0, NumberOfOthers, NULL, 0, NULL);

	fputs("Input String: ", stdout);
	fgets(str, STR_LEN, stdin);
	SetEvent(hEvent);

	WaitForSingleObject(hThread1, INFINITE);
	WaitForSingleObject(hThread2, INFINITE);
	ResetEvent(hEvent);
	CloseHandle(hEvent);
	return 0;
}

unsigned WINAPI NumberOfA(void *arg)
{
	int i, cnt=0;
	WaitForSingleObject(hEvent, INFINITE);
	for(i=0; str[i]!=0; i++)
	{
		if(str[i]=='A')
			cnt++;
	}
	printf("Num of A: %d \n", cnt);
	return 0;
}
unsigned WINAPI NumberOfOthers(void *arg)
{
	int i, cnt=0;
	WaitForSingleObject(hEvent, INFINITE);
	for(i=0; str[i]!=0; i++)
	{
		if(str[i]!='A')
			cnt++;
	}
	printf("Num of others: %d \n", cnt-1);
	return 0;
}
```
> manual-reset 모드로 event 오브젝트를 생성했으므로, 두 스레드는 동시에 진입하고 깨어나게 된다