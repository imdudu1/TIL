# 가상 메모리 관리

가상 메모리는 비연속 할당된 메모리 영역에 블록 단위로 사용자 프로그램을 자르고, 잘려진 블록 단위들은 가상 메모리 시스템에 따라서 적절한 위치와 크기로 적재/실행한다. 이제 가상 메모리 시스템을 적절한 모델을 기반하여 다양한 최적화 기법을 학습해보자.

## Cost model for Virtual Memory.

가상 메모리 접근할 때 적재되지 않은 메모리에 접근할 때  `Page fault` 발생 시 프로세스는 실행 상태에서 대기 상태로 이동하면서 Context switching 및 커널 개입이 발생하여 큰 비용이 발생한다. 따라서 가상 메모리 최적화에 적용할 Cost model은 Page fault frequency(발생 빈도)와 Page fault rate(발생률)를 기준으로 발생 빈도/률을 최소화 할 수 있도록 설계해야한다. 수업에선 Page system을 기준으로 설명하지만 Segmentation system과 큰 차이가 없다.

**Page reference string (d)** : 프로세스의 수행 중 참조한 페이지 번호 수선

**Page fault rate = F(w)**

![Page fault rate](https://i.imgur.com/cSDbtB8.png)

## Hardware Componenets

- Address translation device (주소 사상 장치)

주소 사상 장치는 VA(Virtual address)를 겪는 성능 저하를 해결하기 위한 특별한 장치를 의미한다.

- Bit Vectors

![Bit vectors](https://i.imgur.com/Q2YW6Ch.png)

Page 사용 상황에 대한 정보를 기록하는 비트들을 의미한다. 만약 페이지 프레임이 가득찬 경우 새 페이지 프레임이 요청된 경우 어떤 페이지 프레임과 교체될 지를 결정할 수 있는 기준으로 사용되는데 그 중 대표적인 2가지로 참조 비트(Reference bit 또는 used bit)와 갱신 비트(Update bit 또는 modified bits, write bits, dirty bits)이다. 그 중 갱신 비트는 dirty bits로 더 많이 알려져있다.

![참조 비트 동작 과정](https://i.imgur.com/65kf2so.png)

`참조 비트 벡터`를 이용해서 가장 최근 참조된 페이지를 표시한다. 참조 비트 벡트의 동작은 프로세스에 의해 참조되면 해당 페이지의 참조 비트를 1로 설정하고, 주기적으로 모든 참조 비트를 0으로 초기화한다. 그러면 참조 비트를 확인하면 최근에 참조된 페이지를 확인할 수 있다. 참조 비트는 한번 참조된 영역은 다시 참조될 가능성이 높다는 Locality에 기반한다.

`갱신 비트 벡터`는 페이지가 메모리에 적재된 후 프로세스에 의해 수정 되었는지를 표시한다. 우리가 분할된 페이지들은 스왑 디바이스에서 필요한 순간 메모리에 적재한다. 메모리에 적재된 페이지는 CPU에 의해 메모리에 데이터를 수정 또는 삭제하는 등의 작업을 한다. 그러면 메모리에 적재된 페이지와 스왑 데이터의 페이지는 ***같은 페이지였지만 서로 불일치***된다. 따라서 이를 표시하기 위한 비트 벡터이다. 따라서 갱신 비트된 페이지는 스왑 데이터와 일치시키기 위해 **Write-back**을 수행한다.

## Software Components

- Allocation strategies (할당 기법)
  - 각 프로세스에게 메모리를 얼마 만큼 줄 것인가에 대한 기법이다.
    - Fixed allocation (고정 할당)
      - 프로세스의 실행 동안 *고정된 크기*의 메모리를 할당한다.
    - Variable allocation (가변 할당)
      - 프로세스의 실행 동안 할당하는 메모리의 *크기가 유동적*이다.
  - 고려사항
    - 이 기법에서 가장 중요한 것은 *할당해줄 메모리 양을 예측*해야 한다. 만약 너무 적은 경우 Page falut가 많이 발생해 성능이 저하되고, 너무 많이 할당하면 메모리가 낭비된다.

- Fetch strategies
  - 특정 페이지를 메모리에 언제 적재할 것인가에 대한 기법이다.
    - Demand fetch (demand paging)
      - 프로세스가 참조하는 페이지만 적재한다. 즉, Page fault가 발생하기 때문에 Overhead가 발생한다.
    - Anticipatory fetch (pre-paging)
      - 참조될 가능이 높은 페이지를 예측한다. 참조된 페이지와 인접한 페이지는 다음에 참조될 확률이 높다라는 예측에 기반한다.
      - 예측을 성공시 page falut overhead가 없지만, 실패 시 예측한 페이지를 반환하는 작업으로 더 많은 overhead가 발생한다. 또 예측하는 작업에 대한 비용도 추가로 발생한다.
      - Hit ratio에 민감
    - 실제 대부분의 시스템은 준수한 성능을 보여주는 Demand fetch 기법을 많이 사용한다.
- Placement strategies (배치 기법)
  - Page/segment를 어디에 적재할 것인가?
    - Paging system의 경우 페이지 크기가 모두 같이 때문에 고려사항이 없지만 Segmentation system의 경우 First-fit, Best-fit, Worst-fit, Next-fit같은 고려사항이 존재한다.
- Replacement strategies (교체 기법)
  - 새로운 페이지를 어떤 페이지와 교체할 것인가? (= 빈 페이지 프레임이 없는 경우)
  - 이후 자세히 설명
- Cleaning strategies (정리 기법)
  - 변경된 페이지를 언제 write-back할 것인가에 대한 기법이다.
  - Demand cleaning
    - 해당 페이지가 *메모리에서 내려올 때 write-back을 수행*한다.
  - Anticipatory cleaning (pre-cleaning)
    - 더 이상 *가능성이 없다고 예측할 때 미리 write-back를 수행*한다. 하지만 예측에 실패하면 더 많은 overhead가 발생한다.
  - 실제 대부분의 시스템은 준수한 성능을 보여주는 Demand cleaning 기법을 많이 사용한다. (Deja vu..)
- Load control strategies (부하 조절 기법)
  - 시스템의 프로세스 수(multi-programming degree)를 조절하는 기법이다.
    - Allocation strategies와 연계, 프로세스에 따라서 할당할 메모리의 크기를 달리하기 때문에
  - 적정 수준의 프로세스 수(multi-programming degree)를 유지
    - 저부하 상태
      - 시스템 자원 낭비, 성능 저하
    - 고부하 상태
      - 자원에 대한 경쟁 심화, 성능 저하
      - ***Thrashing*** 현상 발생
        - 과도한 Page fault가 발생하는 것
    - 따라서 적-절-히 유지되는 Plateau(고원) 영역을 유지해야 한다.

![Load control strategies](https://i.imgur.com/Bo4jIDE.png)

