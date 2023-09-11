# Async Notification I/O 모델

<br>

## Async Notification I/O 모델의 이해

<br>

### 동기 입출력

<br>

<img src="./images/AsyncNoti/SyncIO.png" width = 500> <br>

- 입출력 함수의 호출 및 반환의 시기가 데이터 전송의 시작 및 완료의 시기와 일치한다
- 함수가 호출된 동안에는 다른 작업을 할 수 없다
- 여기서 전송 완료란 TCP에서 데이터의 전송을 보장하기 때문에 버퍼에 데이터를 모두 write 했을 때를 의미한다
- select 함수는 입출력이 완료 또는 가능한 상태가 되었을 때 반환을 하므로 대표적인 동기 notification 모델이다

<br>

### 비동기 입출력

