# CPU 스케쥴링

## CPU 스케쥴링

CPU 스케쥴링은 멀티 프로그래밍 운영체제의 기본이며, 멀티 프로그래밍의 항상 어떠한 프로세스를 실행할 수 있고, CPU 활용을 극대화하기 위한 목적이다.

CPU 스케쥴링의 기본 컨셉은 우리가 사용하는 대부분의 프로세스는 CPU burst보다 I/O burst인 시간이 더 많고, 따라서 I/O burst인 시간에 CPU burst인 다른 프로세스를 수행하게 되면 성능의 올릴 수 있다는 것이다. 따라서 CPU 스케쥴링은 Reday큐에 있는 다음 CPU가 필요한 프로세스를 선정해야하는데 매우 간단하게 FIFO queue와 Priority queue를 생각해볼 수 있다.

- Non-preemptive scheduling
  - 현재 CPU를 선점 중인 프로세스가 자발적으로 종료 또는 Wating 큐로 스위칭하기 전까지 내버려둔다.
- Preemptive scheduling
  - 현재 CPU를 선점 중인 프로세스를 강제로 쫓아낼 수 있다.

프로세스의 라이프 사이클에서 CPU 스케쥴링은 다음과 같은 의사 결정을 할 수 있다.

1. 실행 상태에서 대기 상태로 전환될 때
2. 실행 상태에서 준비 상태로 전환될 때
3. 대기 상태에서 준비 상태로 전환될 때
4. 프로세스가 종료될 때

여기서 1번과 4번은 선택권이 없이 Non-preemptive해야하고, 2번과 3번은 Preemptive와 Non-preemptive중 선택해야한다.

### Dispatcher

Dispatcher는 CPU core를 제어하는 모듈로 CPU 스케쥴러로 부터 프로세스를 선택해 다음과 같은 작업을 수행한다.

- 하나의 프로세스에서 다른 프로세서의 문맥 교환(context switching)
- User mode로 전환
- 사용자 프로그램의 재개를 위한 올바른 위치로 이동

![](https://i.imgur.com/A54k7Ra.png)

앞서 공부했던 것과 관련하여 우리는 프로세스가 다른 프로세스로 전환되는 문맥 교환시 현재 프로세스의 재개를 위한 PCB를 저장과 불러오는 작업이 필요하단 걸 알고 있다. 위 사진에서 앞서 PCB를 저장하고 불러오는 시간이 dispatch latecy고 만약 dispatcher가 너무 빠르게 이뤄진다면 모든 프로세스는 수행하기 전 문맥 교환만 이뤄져 시스템이 다운될 수 있다.

이렇듯 스케쥴러의 목표는

- CPU의 효율성 극대화
- 일정 시간안에 최대한 많은 프로세스의 완료
- 요청에서 부터 종료까지의 최소한의 수행 시간
- 어떤 프로세스가 Ready queue에서 대기하는 시간을 최소화
  - Ready queue의 대기 시간의 합을 최소화
  - 가장 중요한 목표이며, 이 목표가 이뤄지면 부과적으로 다른 목표도 이뤄질 수 있다.
- 응답 시간 최소화

이렇듯 CPU 스케쥴리는 Ready queue에서 어떤 프로세스를 선택할 것인가에 대해서 고민해야 한다.

- FCFS : 먼저 들어온 (Ready queue에 도착 시간을 기준으로) 프로세스를 선정한다.
  - Non-preemptive scheduling
  - 구현이 매우 간단하고 스케쥴리에 드는 오버헤드가 적다 (들어오는 순서대로만 실행하면 되기 때문에)
  - 수행할 프로세스의 순서에 따라 매우 비효율적으로 처리될 수 있음
    - 매우 긴 CPU burst를 가진 프로세스 먼저 처리되면 짧은 CPU burst를 가진 프로세스는 오랜시간 기다려야 함 (Convoy effect)
  - Batch system에 적합 / Interactive system에 부적합
- SJF(or SPN) : 짧은 수행 시간을 가지는 프로세스를 선정한다.
  - Non-preemptive scheduling
  - 다음 CPU burst가 짧은 프로세스를 선정해야 함
    - CPU burst를 예측할 수 있어야 함
  - 시스템 내 프로세스 수 최소화
    - 짧은 프로세스를 빨리빨리 끝냄
      - 짧은 프로세스들의 자원이 반환되어 재활용할 수 있음
  - FCFS의 Convoy effect를 해결함
  - 만약 모든 CPU burst가 같다면 FCFS와 같음
  - **무한대기(Starvation) 현상 발생**
    - CPU burst가 긴 프로세스가 영원히 할당받지 못하는 경우가 발생
- SRTN(Shotest Remaining Time Next)
  - SPN의 변경
  -  Ready 상태 프로세스 중 잔여 시간이 적은 프로세스가 선점
    - Preemptive scheduling
  - SPN의 장점을 극대화
  - 단점
    - 프로세스 생성 시, 총 실행 시간 예측
    - 잔여 시간을 계속 추적 -> Overhead 발생
    - Context switching overhead 발생
      - Preemptive sheduling 특징
    - 비현실적 & 구현의 어려움
- HRRN(High-Response-Ratio-Next)
  - SPN의 변형
    - SPN + **Aging** concept, Non-preemptive scheduling
  - Aging concepts
    - 프로세스의 대기 시간을 고려하여 기회를 제공
  - 스케줄링 기준
    - Response ratio가 높은 프로세스 우선
  - SPN의 장점과 무한 대기 현상 예방
  - 여전히 수행 시간을 예측하는 Overhead가 존재
- RR : 시분할하여 프로세스를 선정한다.
  - Preemptive scheduling
  - Ready queue의 도착 시간을 기준 (=FCFS)
  - 먼저 도착한 프로세스를 먼저 처리 (=FCFS)
  - 자원 사용 제한 시간을 둠
    - 프로세스는 할당된 시간이 지나면 자원 반납 (Preemptive)
      - 제한 시간이 성능을 결정하는 핵심 요소
      - 만약 제한 시간이 INF인 경우 FCFS와 동일
    - *특정 프로세스의 자원 독점 방지*
    - **Context switch overhead가 큼**
- Priority-based : 우선순위를 부여하여 선정한다.
- MLQ : 상황에 따라 적절한 스케쥴링 알고리즘을 사용한다.
  - 작업별 (or 우선순위)별 별도의 Ready queue를 가짐
    - 최초 배정된 Queue를 벗어나지 못함
    - 각 Queue는 자신만의 스케줄링 기법 사용
  - 여러 Queue 사이에는 우선순위 기반의 스케줄링 사용
    - Fixed-priority, preemptive scheduling
  - 장점
    - 우선순위가 높은 중요한 작업에 대한 빠른 응답 시간
  - 단점
    - 여러개의 Queue 관리와 스케줄링에 대한 Overhead
    - 우선순위가 낮은 queue는 무한대기(starvation) 현상이 발생 가능
- MLFQ : 피드백을 줘 유동적으로 현재 상황에서 적절한 스케쥴리을 선택한다.
  - MLQ의 Queue 이동 제한 문제를 해결하여 동적인 상황에서 적절한 queue로 이동할 수 있다.
  - Feedback을 통해 우선 순위 조정
    - 현재까지의 프로세서 사용 정보 활용
  - 단점
    - 설계와 구현이 복잡, 스케줄링 Overhead가 큼
    - 여전히 Starvation 문제 존재
  - Queue 이동 전략
    - 각 준비 큐마다 시간 할당량을 다르게 배정
      - 프로세스 특성에 맞는 형태로 시스템 운영
    - 입출력 위주 프로세스들을 상위 큐로 이동
      - I/O 작업이 많은 프로세스들은 CPU burst가 짧기 때문에 빠르게 끝냄
    - 대기 시간이 지정된 시간보다 오래된 작업을 우선 순위를 높임
      - Starvation 문제 해결

각 스케쥴링은 다음과 같은 특성으로 구분할 수 있다.

- 공평성
  - FCFS (First-Come-First-Service)
  - RR (Round-Robin)
- 효율성 및 서능
  - SPN (Shortest-Process-Next)
  - SRTN (Shortest Remaining Time Next)
  - HRRN (High-Response-Ratio-Next)
