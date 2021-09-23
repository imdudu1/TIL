# 메모리 관리

지금까지 우리는 운영체제가 관리하는 중요한 대상인 프로세스에 대해 공부했다. 이번 장에서는 운영체제가 중요하게 관리하는 대상인 `메모리`에 대해서 공부하게 된다.

## [배경 지식](https://youtu.be/es3WGii_7mc)

## 메모리 할당

### 연속된 메모리 할당

연속된 메모리 할당(Continuous memory allocation)은 우리가 실행한 프로그램, 즉 프로세스를 하나의 연속된 메모리에 배치되도록 하는 방법이다. 따라서 메모리에 올라갈 수 있는 프로세스의 수, 각 프로세스에게 할당되는 메모리 공간 크기, 메모리 분할 방법등의 메모리 구성 정책이 필요하다.

#### Uni-programming

연속된 메모리 할당에선 메모리에 올라갈 수 있는 프로세스의 수에 따라서 크게 Uni-programming/Multi-programming으로 나눌 수 있다. 그 중 Uni-programming은 *메모리에 프로세스가 하나만 존재하는 경우*로 가장 간단한 메모리 관리 기법이다. 굉장히 심플하지만 이 경우도 문제점이 하나 있는데 `프로그램의 크기 > 메모리 크기`인 상황인데 이 경우 `Overlay structure`로 해결할 수 있다. Overlay structure는 간단하게 말해서 공통된 메모리는 항상 메모리에 존재하지만 *개별적인 기능은 같은 메모리에 번갈아가면서 로딩*하여 사용하는 방식이다. 이 방식은 운영체제에서 해주는게 아니라 개발자 또는 사용자가 프로세스 실행 흐름을 완벽하게 이해한 상태에서 가능하기 때문에 어려움이 있다.

또 다른 문제점으론 *커널(Kernel)의 보호*이다. 메모리에는 우리가 실행한 프로그램인 프로세스도 있지만 기본적으로 운영체제의 커널 로딩되어 있다. 이런 상황에서 우리가 실행한 프로세스가 커널 영역에 접근하거나 사용하게 되면 운영체제의 동작에 치명적인 결함을 만들 수 있으므로 우리 프로세스가 커널 영역에 접근하는걸 차단할 필요가 있다. 그렇기 때문에 커널 영역과 우리 프로세스 영역 사이에 `Boundary address`를 정해서 프로세스는 Boundary address를 넘어가지 못하도록 경계를 긋는 방법이 있다.

앞서 언급한 문제점들은 메모리 할당에 관련된 문제라면 Uni-programming 자체 문제점을 몇가지 알아보자. Uni-programming은 메모리에 **단 하나**의 프로세스가 올라간다고 했다. 따라서 메모리가 남아 프로세스를 더 사용할 수 있어도 그러지 못하기 때문에 자원의 효율성이 낮고(Low system resource utilization), 자원을 효율적으로 사용하지 못하니 시스템의 성능도 같이 낮아지게 된다(Low system performance). 이 문제를 해결하기 위해선 남는 공간에도 프로세스를 더 생성할 수 있는 ***Multi-programming***을 사용해야 한다.

#### Multi-programming

##### Fixed partition

Fixed partition은 이름에서 알 수 있듯 *고정된 크기의 메모리를 미리 나눠둔 방법*이다. 각각의 분할된 메모리 공간에는 하나의 프로세스가 존재하며 분할된 메모리의 개수가 Multiprogramming-degree를 의미하며 커널과 여러 프로세스들이 동시에 메모리에 올라갈 수 있으니 서로의 메모리 공간을 침범할 수 없도록 Boundary address를 사용한다.

여기서 중요한 개념이 나타나는데 바로 Fragmentation(이하 단편화)이다. 단편화는 미리 나눠둔 메모리 영역보다 작은 프로세스들이 올라가게 되면 남는 메모리 공간이 여러 곳에서 단편적으로 생성되는걸 의미한다. 이때 생성된 단편화를 두 가지로 나눠서 설명할 수 있는 하나는 Internal fragmentation, 또 하나는 External fragmentation이다.

Internal fragmentation은 하나의 파티션에서 생긴 단편화를 의미하고, External fragmentation은 단편화된 메모리와 여유 메모리를 합친 메모리 공간이 충분하지만 ***연속된 메모리 공간이 아니기 때문에*** 더 이상 프로세스를 만들 수 없는 것을 의미한다.

##### Variable partition

Fixed partition의 경우 방식이 간단한 반면에 단편화와 같은 메모리 문제가 있어 사용하기엔 적절하지 못한 문제가 있었다.

Variable partition은 앞선 문제를 해결하여 *초기에는 전체의 하나의 영역*이였다 *프로세스를 처리하는 과정에서 메모리 공간이 동적으로 분할*하는 방법이다. 따라서 프로세스가 필요한 만큼 메모리를 자르기 때문에 Internal fragmentation 문제가 해결된다.

![Where is it located?](https://i.imgur.com/Z4ia2fu.png)

Variable partition는 메모리에 프로세스가 필요한 메모리 영역을 순차적으로 할당해준다. 그러면 먼저 할당됐던 프로세스가 종료가 되어 빈 공간이 생기고, 그 영역의 크기 만큼의 새로운 프로세스가 요청이 되면 어떤 곳에 배치해줄지 정하는 것이 배치 전략이다.

- First-fit
  - 충분한 크기를 가진 첫 번째 partition을 선택
  - Simple and low overhead
  - 공간 활용률이 떨어질 수 있음
    - 15kb를 필요로 하는 프로세스가 가장 먼저 찾은 메모리 영역이 25kb이면 나머지 7kb는 사용될 확률이 낮아져 빈 메모리로 남을 가능성이 있음
- Best-fit
  - Process가 들어갈 수 있는 partition 중 차이가 가장 작은 곳을 선택
  - 장점
    - 큰 프로세스가 들어갈 메모리 공간을 유지할 수 있음
  - 단점
    - Best-fit을 찾기 위하여 빈 메모리 공간을 전체 탐색할 필요가 있어 overhead가 발생
    - 작은 크기의 partition이 많이 발생
      - 활용하기 너무 작은 메모리 공간이 많이 생김
- Worst-fit
  - Best-fit의 반대 방법으로 Process가 들어갈 수 있는 partition 중 가장 큰 곳을 선택, Best-fit과 같이 선택을 위한 탐색을 위한 Overhead가 존재한다.
    - 작은 크기의 partition 발생을 줄일 수 있음
    - 큰 크기의 partition 확보가 어려움
- Next-fit
  - First-fit 전략과 유사
  - First-fit의 경우 Partition 탐색을 메모리의 시작부터 탐색하기 때문에 메모리의 시작부분에 메모리 할당이 몰리게 되는 문제를 해결하는 방법
    - 선택된 Partition의 다음 메모리 영역부터 탐색을 시작
      - 메모리 영역의 사용 빈도 균등화
  - Low overhead

###### External fragmentation issue

Variable partition은 프로세스가 필요한 만큼의 메모리에 맞게 partitioning하여 internal fragmentation 문제를 해결했고, 전략 배치를 통해 활용 최대한 단편화된 메모리 영역을 재활용하였지만 여전히 External fragmentation issue가 존재한다.

- 공간 통합(Coalescing holes)

공간 통합은 연속된 메모리 영역을 할당하는 특성에 맞게 연속된 partition이 존재하는 경우 각 partition들을 통합하여 사용하는 방법이다. Process가 메모리를 반환하고 나갈 때 통합 작업을 수행하여 low overhead로 문제를 해결할 수 있다.

- 메모리 압축

공간 통합의 경우 연속된 빈 partition을 합쳤다면 메모리 압축은 단편화된 전체 메모리들을 합치게 된다. 하지만 이 과정에서 모든 Process를 재배치하기 때문에 많은 시스템 자원을 소비하게 된다. 매우 효과적인 방법이지만 많은 비용을 지불해야하기 때문에 자주할 순 없고, 필요한 경우(프로세스 처리에 필요한 적재 공간 확보가 필요한 경우, 일정 시간, 요청이 올 때)에 수행해야한다.

### 비연속 메모리 할당

지금까지 프로세스를 메모리에서 연속된 위치에 한번에 올리던 방법을 알아봤다면, 지금부터는 사용자 프로그램을 여러 개의 Block으로 분할하여 실행 시, 필요한 Block들만(나머지 Block들은 swap device에 위치한다.) 적재하여 사용하는 비연속 메모리 할당을 알아보자. 운영체제는 이런 비연속 메모리 할당을 가상으로 마치 하나의 메모리 공간으로 표현하기 위해 가상 메모리라는 기법을 사용하는데 대표적으로 Paging system, Segmentation system과 이 둘을 적절히 섞어 사용하는 Hybrid paging/segmentation system들이 있다.

#### 비연속 메모리 할당에서의 주소 배정

비연속 메모리는 흩어져있는 블록 단위의 메모리 영역을 가상의 연속된 메모리 영역으로 생각할 수 있도록 가상 주소(Virtual address)를 사용하게 된다. 따라서 비연속 메모리 할당에서 주소의 개념은 `가상 주소(Virtual address)`와 `물리 주소(Physical address)`이며 주소 배정(Address mapping)은 *가상 주소에서 물리 주소로*  연결하는 작업을 의미한다.

- Block Mapping

![Block mapping](https://i.imgur.com/maocosv.png)

Block mapping에선 프로세스마다 Block 정보를 관리하는 BMT(Block Mapping Table)를 가지고 Block을 관리한다. BMT에는 여러 컬럼 중 분할된 프로그램 블록을 식별하기 위한 `block number`와 swap device에서 필요로 인해 메모리로 적재된 블록을 표시하기 위한 `residence bit`, 그리고 블록의 실제로 존재하는 `물리 주소의 시작 주소`를 가지고 있다.

Block mapping에서의 가상 주소는 $v = (b, d)$로 구성되는데 b는 *block number*를 의미하고 d는 *offset*을 의미한다. 따라서 b를 이용해 BMT에서 b 블록을 참고해 (이때 residence bit를 참고해 해당 블록이 메모리에 안올라와 있다면 적재(load)하는 작업이 추가된다.)물리 주소(Read address)와 Offset인 d를 더하면 실제 주소가 가리키고 있는 주소를 찾아낼 수 있다.

