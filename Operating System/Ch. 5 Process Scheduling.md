## Scheduling Algorithms
----
 ### 1.  FCFC(First-Come-First-Service)
- 비선점 알고리즘
- 도착시간(ready queue) 기준
- 자원효율적임(scheduling overhead 낮음)
- 프로세스 종료 또는 I/O 처리를 만날때까지 CPU를 점유함
- 처리순서에 따라 평균 대기 시간에 영향을 주며 Convoy Effect를 야기할 수 있음



### 6. MLQ (Multi-level Queue)
- 작업별 별도의 ready queue를 가짐
	- 최초 배정 된 queue를 벗어나지 못함
	- 각각의 queue는 자신만의 스케줄링 기법 사용 -> Queue 마다 다른 알고리즘
- Queue 사이에는 우선순위 기반의 선점형 스케줄링 사용
- 장점
	- 빠른 응답시간(?) -> 단일 Queue에서 우선순위 탐색을 위해 O(n) 탐색하는 경우 방지 
- 단점
	- 여러개의 Queue 관리 등 스케줄링 overhead
	- 우선순위가 낮은 queue는 stravation 현상 발생 가능

### 7. MFQ (Multi-level Feedback Queue)
- 프로세스의 Queue 간 이동이 허용된 MLQ
- Feedback을 통해 우선 순위 조정
	- 현재까지의 프로세서 사용 정보(패턴) 활용
- 특성 
	- Dynamic priority
	- Preemptive scheduling
	- Favor short burst-time processes
	- Favor I/O bounded processes
	- Imporve adaptablitily
- 프로세스에 대한 사전 정보 없이 SPN, SRTN, HRRN 기법의 효과를 볼 수 있음
- 단점
	- 설계 및 구현이 복잡, 스케줄링 overhead가 큼
	- Stravation 문제
- 변형
	- 각 준비 큐 마다 time-quantum을 다르게 배정
		- 프로세스의 특성에 맞는 형태로 시스템 운영 가능
	- 입출력 위주 프로세스들을 상위 단계의 큐로 이동, 우선순위 높임
		- 프로세스가 block 될 때, 상위의 준비 큐로 진입하게 함
		- 시스템 전체의 평균 응답 시간 줄임, 입출력 작업 분산 시킴
	- 대기 시간이 지정된 시간을 초과한 프로세스들을 상위 큐로 이동
		- 에이징(aging) 기법
- Parameters  for MFQ scheduling
	- Queue의 수 
	- Queue별 스케줄링 알고리즘
	- 우선 순위 조정 기준
	- 최초 Queue 배정 방법

## Thread Scheduling
---

## 다중 처리기 스케줄링(Multi Processeor Scheduling)
---
### 1. 접근 방법
1. 비대칭 다중 처리(Asymmetric multiprocessing)
2. 대칭 다중 처리(Symmetric mulitprocessing, SMP)
	- 각 프로세스는 스스로 스케줄링 처리한다. (ready queue 검사 -> 실행할 쓰레드 선택)
	- 쓰레드 관리를 위한 ready queue 전략은 common ready queue와 per-core queue로 나뉨
		1. common ready queue: 자원 공유에 따른 락킹 방법과 기아(starvation) 방지를 위한 대책이 필요하다. 락킹으로 overhead가 발생할 수 있다
		2. per-core queue: 가장 일반적인 방식으로, 프로세스별 큐를 통해 캐시메모리 효율성 증가시킨다.

## 2. 다중 코어 프로세서
- 메모리스톨(memory stall): 프로세서 연산의 처리 속도가 메모리 처리보다 빨라 프로세서가 대기하거나, 캐시메모리에 존재하지 않는 데이터에 액세스 함으로써 발생하는 상황
![[5_memory_stall.png]]
- 하드웨어 스레드: 메모리스톨을 완화하기 위한 방법으로 cpu에 물리스레드를 추가하는 방법
	- 메모리스톨로 인해 한 하드웨어 스레드가 중단되면, 다른 스레드가 코어에 할당돼 작업진행
	- 각 하드웨어 스레드는 동일한 구조적 상태를 유지하고 논리적 cpu로 인식된다
![[5_multithreaded_multicore_system.png]]
- 하드웨어 스레드를 멀티스레딩 하기 위한 방법으론 coarse-grained 방식과 fine-grained 방식이 있다.
	- coarse-grained 방식은 메모리스톨과 같은 긴 지연시간을 발생시키는 이벤트에 의해 스레드 교환을 처리한다. 이때 코어는 기존에 작업중이던 스레드과 관련된 자원(명령어, 파이프 등)을 비우고, 새로운 스레드의 자원을 적재하므로 스위칭 비용이 발생한다.
	- fine-grained 방식은 스레드 스위칭을 위한 로직을 포함해 교환비용이 상대적으로 낮다.