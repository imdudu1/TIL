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
  - [이후 자세히 설명]
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

## Replacement Strategies

**Fixed allocation**

앞서 SW components를 알아보면서 교체 기법(Replacement strategies)에 대한 간략한 설명을 알았다면, 지금부턴 Fixed allocation, Variable allocation, 두 가지의 경우에 따라 어떤 규칙을 통해 교체될지 알아보자.

### [MIN(OPT, B0) algorithm](https://youtu.be/xLovOdiRtjI?t=822)

MIN algorithm은 *앞으로* 가장 오랫동안 참조되지 않을 페이지를 교체하고, 만약 여러개의 페이지가 동일한 교체 조건에 있다면, Tie-breaking rule(사용자 맘대루!)를 적용한다.

여기서 *앞으로*라는 단어에 주목해야하는데, 이는 미래를 예측해야하기 때문에 실현 불가능한 기법임을 암시한다. 그렇다면 왜 이런 알고리즘을 배우는걸까 라고 한다면, 그건 시나리오에서 나올 수 있는 최선의 경우를 기준으로 실현 가능한 알고리즘의 성능을 검증할 수 있기 때문이다.

### [FIFO algorithm](https://youtu.be/xLovOdiRtjI?t=1320)

우리가 자료구조 또는 알고리즘 수업에서 많이 듣는 FIFO(First In First Out)를 통해 교체하는 기법이다. 순서를 알 수 있도록 적재된 시간을 기억하고 있어야 하며, **Locality**를 고려하지 않았기 때문에 자주 사용되는 페이지가 교체될 가능성이 높다.

FIFO algorithm에서 발생하는 문제점을 해결하기 위한 해결책으로 가장 쉽게 떠올릴 수 있는 방법은, 페이지를 적재할 수 있는 메모리를 늘리는 방법이지만 FIFO algorithm은 *locality를 고려하지 않았기 때문에* **FIFO anomaly**가 발생하여 늘리기 전보다 더욱 떨어지는 현상이 발생할 수 있다.

### [LRU(Least Recently Used) algorithm](https://youtu.be/xLovOdiRtjI?t=1935)

LRU algorithm은 이름에서 알 수 있듯이 가장 오랫동안 참조되지 않은 page를 교체한다. 즉, Locality를 기반으로 둔 교체 기법이다. LRU에서도 가장 오랫동안 참조되지 않은 페이지를 알기 위해서 마지막으로 참조된 시간을 가지고 있어야 하므로 시간을 기록하는 Overhead가 발생할 수 있다. 하지만 이 문제는 기록할 정보를 간소화하여 해소 가능하다. 또 다른 문제점은 *메모리 할당을 Loop 실행에 필요한 Page frame보다 작을 경우* Page fault가 급격하게 증가하게 되는데 이 문제는 Allocation 기법으로 해결해야 한다.

 LRU는 MIN algorithm에 가장 근접한 성능을 보여주며, 실제로 많이 활용되는 기법이다.

### [LFU(Least Frequently Used) algorithm](https://youtu.be/ICq6zoZ0vUQ?t=125)

LFU는 앞의 LRU의 시간 기록에 대한 Overhead를 해결한다. 앞서 LRU에서 설명했듯이 기록할 내용을 간소화하는 방식이로, LFU는 페이지의 참조 횟수를 기록한다. 이렇게 기록된 참조 횟수는 Page fault가 발생하고 교체가 필요할 때 가장 적은 참조 횟수를 가진 페이지를 교체하게 된다. 이 경우 많이 참조된 페이지는 앞으로도 참조될 가능성이 높다는 측면의 Locality를 만족하지만, 최근에 참조된 페이지는 앞으로 다시 참조될 가능성이 높다는 측면의 Locality는 만족하지 못한다. 그래서 최근에 적재된 페이지가 교체될 가능성이 있고, 참조 횟수 누적에 대한 Overhead가 발생할 수 있다.

### NUR(Not Used Recently) algorithm

NUR algorithm은 LRU보다 적은 Overhead로 비슷한 성능을 달성하는데 목적이 있다. NUR은 앞에서 배웠던 Bit vector를 사용해 교체 순서를 정하게 된다.

![교체 순서](https://i.imgur.com/SqZgt0d.png)

위 사진에서 참조(r)은 참조된 순간 1로 기록하고 주기적으로 0으로 바꾸게 된다. 갱신(m)은 페이지에 변경이 있을 경우 1로 기록되고 이 경우 페이지가 교체될 때 Write-back을 수행해야 한다. 앞서 설명에서 알 수 있듯이 단순히 최근에 참조가 됐는지만 확인하기 때문에 LFU의 참조 횟수를 누적하는 Overhead를 없앨 수 있다.

#### [Clock algorithm][https://youtu.be/ICq6zoZ0vUQ?t=685]

![Clock algorithm](https://i.imgur.com/PR6UpvR.png)

NUR을 기반으로 한 알고리즘을 실제로 사용된 알고리즘으로 NUR과의 차이점은 Reference bit를 주기적으로 초기화하던 것과 달리 Clock algorithm은 시계 바늘이 움직이는 것 처럼 Reference bit를 초기화한다.

Clock algorithm은 이름에서 알 수 있듯이 시계의 시침이 돌아가는 것 처럼, 먼저 적재된 페이지가 교체될 가능성이 높아 이 경우 FIFO와 유사하다고 할 수 있다. 하지만 Reference bit를 사용해 Locality를 만족시키는 면에선 LRU(또는 NUR)과도 비슷하다고 할 수 있다.

##### [Second Chance algorithm](https://youtu.be/ICq6zoZ0vUQ?t=988)

Second chance algorithm은 앞의 Reference bit만 고려한 Clock algorithm과 달리 Update bit도 함께 고려한다.

![교체 조건](https://i.imgur.com/xt592Pk.png)

Reference bit은 앞의 Clock algorithm과 같고, 만약 Update bit가 켜져있는 경우 변경 사항을 Swap device에 있는 페이지와 동기화시키기 위한 Write-back 요청을 위한 Write-back list에 추가한다.

**Variable allocation**

### [Working Set algorithm](https://youtu.be/ByQmerWj1bg?t=797)

![Working Set algorithm](https://i.imgur.com/Ty7ZU85.png)

프로세스가 특정 시점(△: Window size)에 자주 참조하는 페이지들의 집합을 유지하는 알고리즘이다. Window는  System parameter로 크기를 정해주고, 시간에 따라 이동하며 *[t-△, t]에 참조되는 페이지들을 유지*하기 때문에 시간에 따라서 할당하는 *프레임의 수가 유동적으로 변활 수 있다*. 이런 특성으로 인해 적재되는 프레임이 없더라도 Window에 벗어난 페이지를 반납하게 되고, 새로 적재되는 페이지가 있더라고 교체되는 페이지가 없을 수 있다.

![Working set과 Window size의 관계](https://i.imgur.com/vSd1rky.png)

Working Set algorithm(이하 WS)는 Window 크기 만큼의 페이지를 유지한다. 즉, ***Locality에 기반을 둔 알고리즘***이며 page fault rate가 감소하고, 시스템의 성능 향상을 기대할 수 있다. 여기서 어떤 페이지들을 유지할 것 인가의 기준이 되는 Window size는 고정이며, 현재 Window의 상태에 따라서 Memory allocation은 가변적으로 변하게 된다. 이런 특성은 지속적으로 Working set을 관리하는 Overhead가 발생한다고 해석할 수 있다.

![Window size vs. WS size](https://i.imgur.com/ouZnYES.png)

따라서 너무 작은 Window size를 지정하면 Page fault rate가 증가할 것이고, 너무 큰 크기를 지정하면 Locality적인 특성 때문에 자원을 낭비하는 꼴이 되기 때문에 얼만큼의 Window size를 지정해주냐에 따라서 성능을 결정 짓는 중요한 요소이다. 추가적으로 WS에 반대인 Memory allocation이 고정이고, Window size가 가변인 알고리즘은 LRU이며, LRU를 참고하기 바란다.

**Working set transition**

![Working set transition](https://i.imgur.com/7FdlNLd.png)

![Working set transition](https://i.imgur.com/ixouilD.png)

Working set transition은 WS에 반복문(for, while, loop, etc...)에서 다른 반복문으로 넘어가는 순간 Working set의 크기가 증가하는 특성이다.

[**성능 평가**](https://youtu.be/ByQmerWj1bg?t=1011)

WS과 같은 알고리즘을 비교하기 위해선 단순히 Page fulat의 수로 비교하기는 문제가 있다. 왜냐하면 할당하는 메모리의 크기도 다르기 때문에 같은 기준점을 잡을 필요가 있다. 따라서 *Page fulat가 발생했을 때 처리에 필요한 비용*과 *프레임을 유지할 때 필요한 비용*을 알 필요가 있다.

### [Page Fault Frequency algorithm](https://youtu.be/ByQmerWj1bg?t=1637)

앞서 Working set algorithm은 Window size에 따라서 Working set을 관리하기 때문에 지속적으로 관리할 필요가 있었다(추가적인 적재이 필요 없더라도 Window에서 벗어난 메모리를 반납하는 등).
이런 문제를 해결하기 위해서 나온 알고리즘이 Page Fault Frequency algorithm(이하 PFF)으로 Page fault rate, 즉 Page fault가 얼마나 자주 발생하는지를 기준으로 *Resident set* 크기를 조절하게 된다. 따라서 '지속적인' 관리를 하던 문제를 `Page fault가 발생하는 시점`으로 Overhead를 줄이는 알고리즘이다.

![Criteria for age fault rate](https://i.imgur.com/aHMgU2F.png)

앞서 설명에서 Page fault rate의 빈도에 따라서 PF(Page frame)을 조절하는걸 알았다며 그 빈도의 정도를 정해주는 것이 성능에 중요한 요소이다. 여기서 IFT(Inter-fault time)은 직전 Page fault와 지금 Page fault 사이의 시간 간격을 의미하며 이것을 threshold value와 비교해 빈도를 구분할 수 있다.

### [Variable MIN algorithm](https://youtu.be/ByQmerWj1bg?t=2224)

앞서 알아봤단 MIN algorithm과 같이 미래를 예측하는 실현 불가능한 기법이다. 따라서 이 알고리즘은 다른 알고리즘의 성능 측정에 대한 기준을 제시한다.

VMIN(Variable MIN algorithm)은 페이지 r이 참조되면 ***(t, t+△]***에 대해서 다시 참조되는지 보고 유지할지 안할지를 결정하며 진행된다. 알고리즘 과정을 살펴보면 Window size(△)를 얼만큼 주는가에 따라서 결과는 달라지게 된다. 따라서 적절한 △를 주는 것이 중요한데 그 적절한 값은 ***△=R/U***라는 공식으로 찾을 수 있다. 여기서 R과 U는 각각 Page fault 처리 비용과 PF 유지 비용을 의미하며 둘 중 비용이 비싼 작업을 줄이고 대신 가격이 저렴한 작업을 늘리는 방식으로 찾게 된다.

