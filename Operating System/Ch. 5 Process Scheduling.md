Scheduling Algorithms
1. FCFC(First-Come-First-Service)
	- 비선점 알고리즘
	- 도착시간(ready queue) 기준
	- 자원효율적임(scheduling overhead 낮음)
	- 프로세스 종료 또는 I/O 처리를 만날때까지 CPU를 점유함
	- 처리순서에 따라 평균 대기 시간에 영향을 주며 Convoy Effect를 야기할 수 있음
2. 