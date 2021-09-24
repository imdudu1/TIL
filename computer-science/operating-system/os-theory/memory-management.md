# 메모리 관리

지금까지 우리는 운영체제가 관리하는 중요한 대상인 프로세스에 대해 공부했다. 이번 장에서는 운영체제가 중요하게 관리하는 대상인 `메모리`에 대해서 공부하게 된다.

## [배경 지식](https://youtu.be/es3WGii_7mc)

## 메모리 할당

### 연속된 메모리 할당

연속된 메모리 할당\(Continuous memory allocation\)은 우리가 실행한 프로그램, 즉 프로세스를 하나의 연속된 메모리에 배치되도록 하는 방법이다. 따라서 메모리에 올라갈 수 있는 프로세스의 수, 각 프로세스에게 할당되는 메모리 공간 크기, 메모리 분할 방법등의 메모리 구성 정책이 필요하다.

#### Uni-programming

연속된 메모리 할당에선 메모리에 올라갈 수 있는 프로세스의 수에 따라서 크게 Uni-programming/Multi-programming으로 나눌 수 있다. 그 중 Uni-programming은 _메모리에 프로세스가 하나만 존재하는 경우_로 가장 간단한 메모리 관리 기법이다. 굉장히 심플하지만 이 경우도 문제점이 하나 있는데 `프로그램의 크기 > 메모리 크기`인 상황인데 이 경우 `Overlay structure`로 해결할 수 있다. Overlay structure는 간단하게 말해서 공통된 메모리는 항상 메모리에 존재하지만 _개별적인 기능은 같은 메모리에 번갈아가면서 로딩_하여 사용하는 방식이다. 이 방식은 운영체제에서 해주는게 아니라 개발자 또는 사용자가 프로세스 실행 흐름을 완벽하게 이해한 상태에서 가능하기 때문에 어려움이 있다.

또 다른 문제점으론 _커널\(Kernel\)의 보호_이다. 메모리에는 우리가 실행한 프로그램인 프로세스도 있지만 기본적으로 운영체제의 커널 로딩되어 있다. 이런 상황에서 우리가 실행한 프로세스가 커널 영역에 접근하거나 사용하게 되면 운영체제의 동작에 치명적인 결함을 만들 수 있으므로 우리 프로세스가 커널 영역에 접근하는걸 차단할 필요가 있다. 그렇기 때문에 커널 영역과 우리 프로세스 영역 사이에 `Boundary address`를 정해서 프로세스는 Boundary address를 넘어가지 못하도록 경계를 긋는 방법이 있다.

앞서 언급한 문제점들은 메모리 할당에 관련된 문제라면 Uni-programming 자체 문제점을 몇가지 알아보자. Uni-programming은 메모리에 **단 하나**의 프로세스가 올라간다고 했다. 따라서 메모리가 남아 프로세스를 더 사용할 수 있어도 그러지 못하기 때문에 자원의 효율성이 낮고\(Low system resource utilization\), 자원을 효율적으로 사용하지 못하니 시스템의 성능도 같이 낮아지게 된다\(Low system performance\). 이 문제를 해결하기 위해선 남는 공간에도 프로세스를 더 생성할 수 있는 _**Multi-programming**_을 사용해야 한다.

#### Multi-programming

**Fixed partition**

Fixed partition은 이름에서 알 수 있듯 _고정된 크기의 메모리를 미리 나눠둔 방법_이다. 각각의 분할된 메모리 공간에는 하나의 프로세스가 존재하며 분할된 메모리의 개수가 Multiprogramming-degree를 의미하며 커널과 여러 프로세스들이 동시에 메모리에 올라갈 수 있으니 서로의 메모리 공간을 침범할 수 없도록 Boundary address를 사용한다.

여기서 중요한 개념이 나타나는데 바로 Fragmentation\(이하 단편화\)이다. 단편화는 미리 나눠둔 메모리 영역보다 작은 프로세스들이 올라가게 되면 남는 메모리 공간이 여러 곳에서 단편적으로 생성되는걸 의미한다. 이때 생성된 단편화를 두 가지로 나눠서 설명할 수 있는 하나는 Internal fragmentation, 또 하나는 External fragmentation이다.

Internal fragmentation은 하나의 파티션에서 생긴 단편화를 의미하고, External fragmentation은 단편화된 메모리와 여유 메모리를 합친 메모리 공간이 충분하지만 _**연속된 메모리 공간이 아니기 때문에**_ 더 이상 프로세스를 만들 수 없는 것을 의미한다.

**Variable partition**

Fixed partition의 경우 방식이 간단한 반면에 단편화와 같은 메모리 문제가 있어 사용하기엔 적절하지 못한 문제가 있었다.

Variable partition은 앞선 문제를 해결하여 _초기에는 전체의 하나의 영역_이였다 _프로세스를 처리하는 과정에서 메모리 공간이 동적으로 분할_하는 방법이다. 따라서 프로세스가 필요한 만큼 메모리를 자르기 때문에 Internal fragmentation 문제가 해결된다.

![Where is it located?](https://i.imgur.com/Z4ia2fu.png)

Variable partition는 메모리에 프로세스가 필요한 메모리 영역을 순차적으로 할당해준다. 그러면 먼저 할당됐던 프로세스가 종료가 되어 빈 공간이 생기고, 그 영역의 크기 만큼의 새로운 프로세스가 요청이 되면 어떤 곳에 배치해줄지 정하는 것이 배치 전략이다.

* First-fit
  * 충분한 크기를 가진 첫 번째 partition을 선택
  * Simple and low overhead
  * 공간 활용률이 떨어질 수 있음
    * 15kb를 필요로 하는 프로세스가 가장 먼저 찾은 메모리 영역이 25kb이면 나머지 7kb는 사용될 확률이 낮아져 빈 메모리로 남을 가능성이 있음
* Best-fit
  * Process가 들어갈 수 있는 partition 중 차이가 가장 작은 곳을 선택
  * 장점
    * 큰 프로세스가 들어갈 메모리 공간을 유지할 수 있음
  * 단점
    * Best-fit을 찾기 위하여 빈 메모리 공간을 전체 탐색할 필요가 있어 overhead가 발생
    * 작은 크기의 partition이 많이 발생
      * 활용하기 너무 작은 메모리 공간이 많이 생김
* Worst-fit
  * Best-fit의 반대 방법으로 Process가 들어갈 수 있는 partition 중 가장 큰 곳을 선택, Best-fit과 같이 선택을 위한 탐색을 위한 Overhead가 존재한다.
    * 작은 크기의 partition 발생을 줄일 수 있음
    * 큰 크기의 partition 확보가 어려움
* Next-fit
  * First-fit 전략과 유사
  * First-fit의 경우 Partition 탐색을 메모리의 시작부터 탐색하기 때문에 메모리의 시작부분에 메모리 할당이 몰리게 되는 문제를 해결하는 방법
    * 선택된 Partition의 다음 메모리 영역부터 탐색을 시작
      * 메모리 영역의 사용 빈도 균등화
  * Low overhead

**External fragmentation issue**

Variable partition은 프로세스가 필요한 만큼의 메모리에 맞게 partitioning하여 internal fragmentation 문제를 해결했고, 전략 배치를 통해 활용 최대한 단편화된 메모리 영역을 재활용하였지만 여전히 External fragmentation issue가 존재한다.

* 공간 통합\(Coalescing holes\)

공간 통합은 연속된 메모리 영역을 할당하는 특성에 맞게 연속된 partition이 존재하는 경우 각 partition들을 통합하여 사용하는 방법이다. Process가 메모리를 반환하고 나갈 때 통합 작업을 수행하여 low overhead로 문제를 해결할 수 있다.

* 메모리 압축

공간 통합의 경우 연속된 빈 partition을 합쳤다면 메모리 압축은 단편화된 전체 메모리들을 합치게 된다. 하지만 이 과정에서 모든 Process를 재배치하기 때문에 많은 시스템 자원을 소비하게 된다. 매우 효과적인 방법이지만 많은 비용을 지불해야하기 때문에 자주할 순 없고, 필요한 경우\(프로세스 처리에 필요한 적재 공간 확보가 필요한 경우, 일정 시간, 요청이 올 때\)에 수행해야한다.

### 비연속 메모리 할당

지금까지 프로세스를 메모리에서 연속된 위치에 한번에 올리던 방법을 알아봤다면, 지금부터는 사용자 프로그램을 여러 개의 Block으로 분할하여 실행 시, 필요한 Block들만\(나머지 Block들은 swap device에 위치한다.\) 적재하여 사용하는 비연속 메모리 할당을 알아보자. 운영체제는 이런 비연속 메모리 할당을 가상으로 마치 하나의 메모리 공간으로 표현하기 위해 가상 메모리라는 기법을 사용하는데 대표적으로 Paging system, Segmentation system과 이 둘을 적절히 섞어 사용하는 Hybrid paging/segmentation system들이 있다.

#### 비연속 메모리 할당에서의 주소 배정

비연속 메모리는 흩어져있는 블록 단위의 메모리 영역을 가상의 연속된 메모리 영역으로 생각할 수 있도록 가상 주소\(Virtual address\)를 사용하게 된다. 따라서 비연속 메모리 할당에서 주소의 개념은 `가상 주소(Virtual address)`와 `물리 주소(Physical address)`이며 주소 배정\(Address mapping\)은 _가상 주소에서 물리 주소로_ 연결하는 작업을 의미한다.

#### Block Mapping

![Block mapping](https://i.imgur.com/maocosv.png)

Block mapping에선 프로세스마다 Block 정보를 관리하는 BMT\(Block Mapping Table\)를 가지고 Block을 관리한다. BMT에는 여러 컬럼 중 분할된 프로그램 블록을 식별하기 위한 `block number`와 swap device에서 필요로 인해 메모리로 적재된 블록을 표시하기 위한 `residence bit`, 그리고 블록의 실제로 존재하는 `물리 주소의 시작 주소`를 가지고 있다.

Block mapping에서의 가상 주소는 $v = \(b, d\)$로 구성되는데 b는 _block number_를 의미하고 d는 _offset_을 의미한다. 따라서 b를 이용해 BMT에서 b 블록을 참고해 \(이때 residence bit를 참고해 해당 블록이 메모리에 안올라와 있다면 적재\(load\)하는 작업이 추가된다.\)물리 주소\(Read address\)와 Offset인 d를 더하면 실제 주소가 가리키고 있는 주소를 찾아낼 수 있다.

**Virtual Storage Methods**

**Paging system**

Paging system은 앞서 배운 Block mapping과 매우 비슷하지만, Swap space에 있는 단위가 Block이 아닌 `Page(이하 페이지)`라는 점과 메모리를 `Page frame`으로 나눠 관리한다는 점이 다르다. Paging system은 일장한 크기에 페이지 단위로 프로세스를 잘라 Swap device에 보관하고 있다, 필요한 순간 메모리에 적재한다. 이때 적재할 위치는 _비연속적_이며 적재하는 단위는 페이지이다. 페이지는 논리적인 분할이 아니라 크기에 따른 분할이라 하나의 함수 코드가 2개의 페이지 단위로 잘려서 존재할 수 있기 때문에 페이지의 공유 및 보호 과정이 앞으로 배울 Segmentation에 비해 복잡하다. 하지만 모든 페이지가 동일한 크기를 가지는 단순함으로 관리가 편하기 때문에 효율적일 수 있다. 또한 앞서 외부 단편화\(External fragmentation\)도 존재하지 않는다. 단, **내부 단편화는 존재**하게 되는데 동일한 크기로 페이지를 자르기 때문에 마지막 페이지에서 내부 단편화\(Internal fragmentation\)이 발생할 수 있다.

**Direct Mapping**

![Direct mapping](https://i.imgur.com/PTS0zC0.png)

Block mapping과 매우 유사한 방법이다. 먼저 가상 주소\(virtual address\)의 p\(페이지 번호\)로, PMT\(Page Map Table\)에서의 p번째 페이지를 찾기 위해서 entrySizs\(PMT의 한 행의 크기\)와 b를 곱한 결과를 PMT base address에 더한다. 이렇게 구해진 주소에 P 페이지 정보가 존재하며 residence bit를 확인하고 적재된 페이지인 경우 page frame number와 pageSize를 곱한 값을 Page frame의 base address에 더해 최종적으로 물리 메모리에 접근할 수 있다.

이 과정에서 Residence bit가 0인 경우, 즉 해당 페이지가 메모리에 적재되지 않은 페이지에 접근할 시 **`Page fault`**가 발생했다고 한다. Page fault는 Context switching \(I/O\)이 발생하고 이 과정은 비싼 작업이기에 이 과정을 얼마나 피하는지가 성능 향상에 큰 영향을 주게 된다.

추가적으로 몇가지 문제가 있는데 물리 메모리에 접근하기 위해서 PMT에서 탐색 및 방문 후 물리 메모리에 접근하기 때문에 메모리 접근 횟수가 _2배_가 되어 성능 저하가 발생할 수 있고, PMT를 위한 메모리 공간이 별로도 필요하다.

**Associative Mapping**

앞의 Direct Mapping의 문제점 중 물리 메모리에 접근하기 위해선 PMT를 거쳐야 하는 문제점을 해결하기 위해서 Associative mapping에선 `TLB`를 도입한다. TLB는 _PMT 접근을 위한 전용 하드웨어_로 요청시 병렬적으로 PMT를 통해 물리 메모리 주소에 접근할 수 있게 해줘 매우 빠른 속도와 낮은 overhead를 가지지만 가격이 비싸 큰 PMT를 다루기가 어렵다는 단점이 존재한다.

**Hybrid direct/associative mapping**

앞에서 배운 두 방식은 각각 장단점이 존재하기에 두 방식을 혼합해 서로의 장점을 서로의 단점을 보완하는 방식이다. 따라서 H/W 비용은 줄이면서 Associative mapping의 장점을 활용한다.

일단 PMT는 커널에 존재하며 그 중 일부를 작은 크기의 TLB에 적재해둔다. 그렇다면 어떤 PMT를 TLB에 올려둘까라는 문제가 생기지만 이것은 컴퓨터 구조를 학습하며 배운 내용을 통해 쉽게 생각할 수 있는데 바로 지역성\(Locality\)이다. 지역성은 _한번 접근된 영역은 또 접근할 확률이 높기 때문에 더 가까운 곳에 둬 다음 접근을 빠르게 처리하기 위한 개념_으로 이 경우에 매우 적절하다.

![Hybrid Direct/Associative Mapping](https://i.imgur.com/BzU2zZb.png)

Hybrid Direct/Associative Mapping의 방식은 다음과 같다.

1. TLB에 적재되어 있는 경우
   1. residence bit를 검사하고 page frame번호 확인
2. TLB에 적재되어 있지 않은 경우
   1. Direct mapping으로 page frame 번호 확인
   2. 해당 PMT entry를 TLB에 적재함

**Memory Management**

앞서 Paging system에서 주소 공간을 연결하는 방법을 알았다면 이번 단원에선 Paging system에서 메모리를 관리하는 방법을 알아 볼 예정이다. Paging system에서 프로세스를 Page 단위로 잘랐다면 메모리 영식 Page과 같은 크기로 메모리를 잘라서 관리한다. 이때 잘려진 메모리 영역을 Page frame\(이하 페이지 프레임\)이라고 하여 FPM\(Fixed partition multi-programming\)과 유사한 기법을 사용한다.

![Frame table](https://i.imgur.com/9ZZULV0.png)

잘려진 메모리를 관리하기 위해선 프레임 테이블이 필요한데, 페이지 프레임당 하나의 entry의 정보를 가지게 되는데 각 페이지 프레임은 다음과 같은 필드를 가진다.

* Allocated / Available flag
  * 페이지 프레임의 현재 상태를 나타내는 필드
* PID
  * 어떤 프로세스에 의해 사용되고 있는지 식별하기 위한 Process ID
* Link
  * 사용 가능한 페이지 프레임들을 Linked list처럼 연결하기 위한 링크 필드
* AV
  * Linked list의 HEAD 역할

**공유 페이지\(Page sharing\)**

공유 페이지는 여러 프로세스가 특정 페이지를 공유해 사용할 수 있다. 예를 들어서 c언어에서 같은 printf 함수의 코드가 모든 프로세스마다 개별적으로 가지게 된다면 불필요한 중복된 메모리를 만들어 효율적이지 못하다. 하지만 이런 공통된\(변화지 않는\) 코드를 여러 프로세스들이 **공유**하게 만든다면 매우 효율적일 것이다. 따라서 공유 페이지는 procedure pages, data page의 경우등을 공유해 효율적인 메모리 활용을 가능케한다.

이때 공유 가능한 Pocedure pages는 _pure code_여야하며, Read-write data의 경우 _병행성\(concurrency\) 제어_ 기법 관리하에서만 가능하다.

**Procedure page sharing \(Problem\)**

![Procedure page sharing](https://i.imgur.com/0ySzgem.png)

![Procedure page sharing problem solution](https://i.imgur.com/vbQRP2f.png)

Data page를 공유하는 방법은 PMT에서 같은 페이지 프레임을 가리키면 간단히 구현할 수 있다. 하지만 Procedure page를 공유하는 경우 단순히 페이지 프레임만 공유한다고 제대로 동작하지 않는다. 앞서 Address mapping 과정을 통해 우리는 프로세스는 지정된 메모리 영역에 올라가는 것이 아니라 현재 메모리 상태에 따라서 동적인 메모리에 올라가게 된다. 따라서 프로그램 코드는 Branch같은 동작이 _메모리 주소에 의존적이지 않도록 상대 주소를 통해 실행_된다.

하지만 서로 다른 PMT 정보를 가진 두 프로세스 P1, P2는 공유 페이지 정보가 들어있는 슬롯이 달라 가상 주소를 계산시 문제가 발생하게 된다. \(우리는 가상 주소를 계산할 때 대상 주소 영역이 가리키는 PMT 슬롯에 접근해 물리 메모리 주소를 얻는 다는걸 기억해야만 한다.\) 가장 간단한 방법은 _공유 페이지을 사용하는 모든 프로세스들은 공유 페이지 정보가 담깃 슬롯 위치를 통일_ 시키도록 하는 것이다.

**Page protection**

페이지 공유를 하면 발생하는 또 다른 문제점은 페이지에 대한 보안이다. 모든 프로세스가 공유 페이지를 조작할 수 있다면 분명 보안 문제가 발생할 수 있다. 이를 해결하기 위해서 페이지 테이블에 해당 페이지에 대한 권한을 표현한 Protection bit를 사용한다.

이렇듯 Paging system을 정리해보면 프로그램을 고정된 크기\(page\)로 분할하고, 메모리도 같은 크기로 미리 분할해둬\(page frame\) _필요한 메모리만 적재_해 메모리를 효율적으로 사용하고, _외부 단편화 문제를 해결_했다. 하지만 프로그램을 분할시 서로 다른 성격의 논리 구조가 하나의 페이지에 존재할 수 있어 _Page sharing/protection이 복잡한 문제_와 페이지 맵핑에서 페이지 상태들을 관리하기 위한 테이블등의 추가적인 메모리가 필요하고, 물리 메모리에 접근하기 위해서 추가적인 접근이 필요하다.\(추가적인 메모리 접근은 전용 H/W를 추가했지만 비용이 비싸고 크기가 작음.\)

**Segmentation system**

앞서 Paging system은 크기를 기준으로 분할한 반면에 Segmentation system은 `논리 단위(stack, heap, main procedure, shared lib, etc)로 분할`하기 때문에 Block들의 크기가 서로 다를 수 있다. 블록들의 크기가 다 다르기 때문에 메모리를 미리 분할하지 못하기에 VPM과 유사하다.

Segmentation system의 장점은 논리 단위로 분할하기 때문에 같은 _논리 영역을 공유하거나\(Segment sharering\), 논리 영역에 따른 보안 설정이 간편_하고\(논리 영역에 같은 성격의 데이터들이 존재한다는 걸 보장함\), 필요한 만큼한 메모리 영역을 할당하기 때문에 _Internal fragmentation_이 발생하지 않는다. 하지만 크기가 제각각인 메모리들이 할당되고 반환되는 과정에서 _External fragmentation**은 발생**_한다. 지금까지 장점을 설명했다면 Segmentation system의 단점으론 _크기가 일정하지 않기 때문에 주소 배정\(Address mapping\)과 관리에 따른 Overhead가 크다_는 단점이 있다.

**Address mapping**

![SMT\(Segmentation Map Table\)](https://i.imgur.com/0yn5u1a.png)

주소 배정은 앞의 Paging system과 동일하지만 몇가지 특징이 다른데 Segmentation 정보를 유지하지 위하여 `SMT`를 사용하고, SMT는 앞서 PMT와 다르게 _제각각인 블록 크기를 알 수 있도록_ `Segmentation length`가 추가 되었고, _논리 단위별로 나뉜 블록의 권한 설정을 표시_하는 `Protection bits`가 있다. 또 권한 설정을 통해 잘못된 메모리에 접근할 경우 또는 잘못된 행위를 하는 경우 다음과 같은 예외가 발생하게 된다.

* Segment fault
  * Paging system의 Paging fault와 동일한 역할
* Segment overflow
  * Segment length보다 큰 메모리에 접근하려고 시도할 경우 발생
* Segment protection
  * Segment에 없는 권한의 행위를 하는 경우 발생 \(쓰기 금지 영역에 쓰기 작업을 할 경우\)

Memory management

![State table](https://i.imgur.com/vzM8yIi.png)

Segmentation system의 경우 VPM과 유사하게 적재 시 크기에 맞추어 분할 후 적재한다.

**Hybrid Paging-Segmentation System**

앞에서 소개한 두 메모리 관리 시스템은 각각의 장단점이 존재했다면 Hybrid Paging-Segmentation System은 이 두개의 장점만 사용하자는 방식이다.

**Address mapping**

![Hybrid Paging/Segmentation direct\(address\) mapping](https://i.imgur.com/RhaMSiU.png)

이 방식에서 가상 주소는 v = \(s, p, d\) 형태이며, s는 segment number, p는 page number, d는 page에서의 offset을 의미한다. 가상 주소의 형태에서 알 수 있듯이 이 방식에선 SMT, PMT를 둘 다 사용하는데 SMT는 프로세스 마다 하나의 SMT를 구성하고, PMT는 Segment마다 하나의 PMT를 구성한다.

SMT는 PMT의 위치를 가리키고, PMT는 블록 단위로 잘린 프로세스의 메모리 영역이 적재되었는지 \(residence bit\)를 확인한 뒤 Page frame number를 통해 해당 물리 메모리 시작 위치부터 d만큼 떨어진 메모리에 접근할 수 있다.

**Summary**

논리적 분할과 고정 크기 분할을 결합한 Hybrid paging-segmentation system은 Page sharing과 Protection이 쉬운 장점\(Segmentation system\)과 메모리 할당/관리의 overhead가 낮고\(Paging system\), External fragmentation 문제를 해결하는 등의 개선된 방식이지만, 단점으로 여전히 내부 단편화\(Internal fragmentation\)이 존재하고 SMT, PMT를 같이 사용하기 때문에 관련 메모리 소모가 크다. 또 주소 배정 과정이 복잡하여 성능을 저하할 수 있다.

