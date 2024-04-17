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

