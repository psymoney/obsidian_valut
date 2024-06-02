# 1. Concept
- blocked/asleep state is the caues of deadlocks
	- a state that processes await certain events
	- a state that processes await required resources
- Deadlock state
	- a situation in which every process in a set of processes is waiting for an event that can be caused only by another process in the set

## Deadlock vs. Starvation

|        | Deadlock | Starvation |
| ------ | -------- | ---------- |
| 대상     | 자원       | CPU        |
| 획득 가능성 | 없음       | 있음         |
| 단계     | Ready Q  | Asleep     |
![[Pasted image 20240430053333.png]]

## 1-1. 자원의 분류
- 일반적 분류
	- 하드웨어 자원 vs 소프트웨어 자원
- 다른 분류
	- 선점 가능 여부
	- 할당 단위
	- 동시 사용 가능 여부
	- 재사용 가능 여부
### A. 선점 가능 여부
- 선점 가능 자원
	- 선점 당한 후, 돌아와도 문제가 발생하지 않는 자원
	- 프로세서, 메모리 등
- 비선점 자원
	- 선점 당하면 이후 진행에 문제가 발생하는 자원
		- Rollback, restart 등 특별한 동작이 필요
	- 하드 드라이브
### B. 할당 단위
- Total allocation resources
	- 자원 전체를 프로세스에 할당
	- 프로세서, 디스크 드라이브
- Partitioned allocation resources
	- 하나의 자원을 여러 조각으로 나눠 여러 프로세스에 할당
	- 메모리

### C. 동시 사용 가능
- Exclusive allocation resources
	- 한 순간에 한 프로세스만 사용 가능한 자원
	- 프로세서, 메모리, 디스크 드라이브 등
- Shared allocation resource
	- 여러 프로세스가 동시에 사용 가능한 자원
	- 프로그램, 공유 데이터(shared data) 등

### D. 재사용 가능
- SR(Serially-resuable Resources)
	- 시스템 내에 항상 존재 하는 자원
	- 사용이 끝나면 다른 프로세스가 사용 가능
	- 프로세서, 메모리, 디스크 드라이브, 프로그램
- CR(Consumable Resources)
	- 한 프로세스가 사용한 후에 사라지는 자원
	- Signal, message

## 1-2. 데드락과 자원의 종류
- 데드락을 발생시킬 수 있는 자원의 형태
	- Non-preemptible resources
	- Exclusive allocation resources
	- Serially resuable resources
	* 할당 단위는 영향을 미치지 않음
* CR을 대상으로 하는 데드락 모델
	* 너무 복잡함

# 2. 