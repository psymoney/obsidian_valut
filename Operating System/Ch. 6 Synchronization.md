## Terminologies
1. 공유 자원(Shared data): 여러 프로세스 간 공유되는 데이터로 동기화 문제에서 제어하고자 하는 대상
2. 경쟁 상황(Race condition): 공유 데이터에 대한 각 프로세스의 작업 순서에 따라 작업 결과가 바뀌는 상황
3. 진입 구역(Entry section): 임계 구역으로의 진입을 허가하거나 통제하는 부분
4. 임계 구역(Critical section): 공유 자원에 대한 프로세스간 동기화를 보장하기 위해 작업을 수행하는 하나의 프로세스만 진입이 허용되는 구역
5. 퇴출 구역(Exit Section): 임계 구역에 대한 점유를 해제함으로써, 임계 구역을 빠져 나오는 구역

## 동기화 문제
프로세스가 병행 수행되는 환경에서 여러 프로세스가 공통된 자원을 공유하는 경우, 작업 순서에 따라 작업 결과가 바뀌는 경쟁 상황(Race Condition)이 발생한다.

## 임계구역 문제 해결 조건
1. Mutual exclusion: 임계 구역에 진입한 프로세스 이외의 프로세스는 임계 구역에 진입해서는 안됨
2. Progress: 임계 구역 이외에 있는 프로세스가 다른 프로세스의 임계 구역 진입을 방해해서는 안됨
3. Bounded waiting: 임계 구역에 진입하고자 하는 프로세스의 대기시간은 무한해서는 안됨

# 동기화 문제 해결 방법
## 1. Software Solutions
- 속도가 느리며 구현이 복잡함
- busy waiting 문제
- 실행의 원자성을 보장하지 못함
	- 공유 데이터 수정 중 interrupt 억제로 해결 가능하나 overhead 발생
### 1-1. Perterson's Algorithm
- 2개의 프로세스에 대한 Mutual exclusion을 보장하는 알고리즘
- 임계 구역 진입 상태를 의미하는 `boolean` 배열 `flag`와, 임계 구역 진입 순번을 의미하는 `int`변수 `turn`을 이용해 구현

```C
while (true) {
	flag[i] = true;  // 현재 프로세스의 임계 구역 진입 상태를 준비 상태로 변경
	turn = j;        // 임계 구역 진입 순번을 다른 프로세스로 변경

	while (flag[j] && turn == j) {}   // Entry section, 다른 프로세스가 진입 대기 중 이거나, 내 순번이 아닌 경우 대기
	/* CRITICAL SECTION */
	flag[i] = false;   // Exit section, 프로세스 j가 진입할 수 있는 조건을 만들어 줌
}
```

### 1-2. Dijkstra's Algorithm
- n개의 프로세스에 대한 Mutual exclusion을 보장하는 알고리즘
- `flag`배열은 임계 구역 진입을 시도 하지 않는 `idle`, 1단계 임계 구역 진입 시도를 의미하는 `want-in`, 2단계 임계 구역 진입 시도 및 임계 구역 진입을 의미하는 `in-CS` 값을 갖는다

```C
while (j <= n) {
	/* 임계 구역 진입 시도 1단계 */
	// 해당 단계에서는 임계 구역 진입 2단계에 있는 프로세스가 
	// idle 상태로 변경될 때까지 대기하며, 
	// 해당 프로세스가 idle 상태가 되면
	// 순번을 자신으로 변경하고 다음 단계로 이동한다
	flag[i] = 'want-in';
	while (turn != i) {
		if (flag[turn] == idle) {
			turn = i;
		}
	}
	/* 임계 구역 진입 시도 2단계 */
	// 해당 단계에서는 임계 구역 진입 2단계에 있는 다른 프로세스가 
	// 존재하지 않을때까지 while문을 반복하며, 임계 구역 진입 2단계에 있는 
	// 다른 프로세스가 존재하는 경우 1단계로 돌아간다
	flag[i] = 'in-CS';
	j = 0;
	while (j <= n && (j == i || flag[j] != 'in-CS')) {
		j++;
	}
	/* CRITICAL SECTION */	
	flag[i] = 'idle'  // Exit section
}
```

## 2. Hardware Solutions
interrupt 되지 않는 하나의 단위로서 원자적 명령어를 이용한 방법이 있다.
- 구현이 간단하다
- busy waiting 문제
### 2-1. TestAndSet(TAS) instruction
- 실행 과정에서 interrupt 되지 않으므로 Mutual exclusion 만족
- CS에 진입하지 않은 다른 프로세스가 CS 진입에 영향을 주지 않으므로 Progress를 만족한다.
- CS 진입 대기중인 프로세스들의 TAS 의 실행 순서는 무작위이므로, 프로세스가 3개 이상인 경우 BW이 보장되지 않는다 
  -> p0, p1, p2가 존재할 때, p2의 TAS() 명령어 수행 결과는 항상 `true`일 가능성이 존재함

*TAS instruction*
```C
# reference: Figure 6.5 in Operating System Concept
boolean test_and_set(boolean *target) {
	boolean rv = *target;
	*target = true;
	return rv;
}
```

*ME implementation with TAS*
```C
# reference: Figure 6.6 in Operating System Concept
do {
	while (test_and_set(&lock)) {}  // lock is initialized to 0
	// test_and_set(&lock)이 false 일 떄, CS 진입 가능
	/* CRITICAL SECTION */
	lock = false;
} while (true);

```

### 2-2 CompareAndSwap(CAS) instruction
- 실행 과정에서 interrupt 되지 않으므로 ME  만족
- Progress 만족
- BW 보장되지 않음

*CAS instruction*
```C
int compare_and_swap(int *value, int expected, int new_value) {
	int temp = *value;

	if (*value == expected) {
		*value = new_value;
	}
	return temp;
}
```

*ME implementation with CAS*
```C
while (true) {
	while (compare_and_swap(&lock, 0, 1) != 0) {}  // lock is initialized to 0
	// lock == 0 일 때, CS 진입 가능
	/* CRITICAL SECTION */
	lock = 0;  // 다른 프로세스 진입 허용
}
```

*ME implementation ensuring BW with CAS*
```C
while (true) {
	waiting[i] = true;
	key = 1;
	while (waiting[i] && key == 1) {
		key = compare_and_swap(&lock, 0, 1);
	}
	waiting[i] = false;
	/* CRITICAL SECTION */

	// 대기중인 프로세스 찾기
	j = (i + 1) % n;
	while ((j != i) && !waiting[j]) {
		j = (j + 1) % n;
	}
	// 대기중인 프로세스가 없다면, 
	// lock을 0으로 변경해 다음 프로세스가 진입할 수 있게 함
	if (j == i)
		lock = 0;
	// 대기중인 프로세스가 있다면,
	// 해당 프로세스의 waiting 상태를 변경해,
	// 해당 프로세스가 Entry section을 빠져나올 수 있게 함
	else
		waiting[j] = false;
}
```

## 3. OS supported SW Solutions

### 3-1. Spinlock
- 정수 값을 갖는 변수 `lock`은 자원의 이용 가능 여부와 이용 대기 중인 프로세스를 나타냄
- `lock` 변수의 접근은 초기화, 연산 `P()`, 연산 `V()` 로만 가능함
- 연산 `P()`와 `V()`는 OS 지원을 통해 원자적(atomic)으로 동작함
- 단점:
	- busy waiting 문제 여전히 존재
	- BW 보장되지 않음
	- 멀티 프로세서 시스템에서만 사용 가능함:
	  `P()`연산의 원자성이 보장되기 때문에, `lock <= 0`을 만족하는 상황에서 다른 프로세스가 `P()` 연산을 실행하는 경우 해당 연산이 종료되지 않고 CPU를 점유하는 문제 발생함

*Instruction P and V*
```C
void P(int lock) {
	while (lock <= 0) {}
	lock--;
}

void V(int lock) {
	lock++;
}
```

*ME Implementation using spinlock*
```C
while (true) {
	P(lock);  // lock의 획득을 위한 대기는 P() 내부에서 이뤄진다
	/* CRITICAL SECTION */
	V(lock);
}
```

### 3-2. Semaphores
- 프로세스를 ready queue에 담음으로써 busy waiting 문제를 해결
- 정수형 변수와 ready queue를 갖는 `S`를 이용함
- 종류:
	- Binary semaphore: 
		- `S`의 정수형 변수가 0 또는 1의 두 종류 값만을 갖는다
		- 상호배제 또는 프로세스 동기화의 목적으로 사용
	- Counting semaphore:
		- `S`의 정수형 변수가 0이하의 정수값을 가질 수 있는 경우
		- Producer-Consumer 문제 등 해결에 사용
- 단점:
	- semaphore의 queue는 wake-up 순서를 보장하지 않아 기아문제 발생할 수 있음

```C
typedef struct {
	int value;
	struct process *list;
} semaphore;

// P()
void wait(semaphore *S) {
	S->value--;
	if (S->value < 0) {
		S->list.add(current_process);
		sleep();
	}
}

// V()
void signal(semaphore *S) {
	S->value++;
	if (S->value <= 0) {
		process proc = S->list.pop();
		wakeup(proc);
	}
}
```

## 4. Language-level solution

### 4-1. Monitor
#### 특징
- Mutual exclusion: 모니터 내에는 항상 하나의 프로세스만 진입 가능
- Information hiding: 공유 데이터는 모니터 내의 프로세스만 접근 가능
- 장점:
	- 사용 쉬움
	- deadlock등 에러 발생 가능성 낮음
- 단점:
	- 지원하는 언어에서만 사용 가능
	- 컴파일러가 OS를 이해하고 있어야 함
#### 구성요소
- Entry Queue: 모니터 내의 procedure 수 만큼 존재
- Condition queue: 모니터 내의 특정 이벤트를 기다리는 프로세스가 대기
- Signaler queue: 모니터에 항상 하나의 신호제공자 큐가 존재, signal() 명령을 실행한 프로세스가 잠시 대기



