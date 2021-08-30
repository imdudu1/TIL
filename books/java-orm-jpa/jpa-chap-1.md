# JPA 소개

> 김영한 저자의 "자바 ORM 표준 JPA 프로그래밍"을 학습하며 정리한 글입니다. 내용의 중요한 부분을 생략하였기에 자세한 내용을 원하신다면 도서를 구매하는 걸 추천해 드립니다.

![&#xC790;&#xBC14; ORM &#xD45C;&#xC900; JPA &#xD504;&#xB85C;&#xADF8;&#xB798;&#xBC0D;](https://i.imgur.com/kz3qpQs.jpg)

### 자바 ORM 표준 JPA 프로그래밍

#### JPA 소개

기존의 자바와 관계형 데이터베이스를 이용한 개발은 SQL에 의존적이며 객체 중심의 JAVA와 데이터 중심인 DB사이에서 서로 간의 변환 작업은 반복적이며 개발자로 하여금 실수를 하기 쉬웠다. 또 DAO를 사용하여 데이터 접근 계층을 나눠 구현체를 숨겨둬 문제가 발생하게 되면 DAO를 열어 구현체를 확인할 필요가 있다. 이는 계층 분할을 통해 얻고자 하는 느슨한 의존관계가 아닌 논리적으로 강한 의존관계를 만들게 된다.

앞서 설명한 문제는 객체와 저장된 객체 정보를 관리하기 위한 SQL를 온전히 개발자에 의해 관리되어야 하기 때문에 발생하게 된다. 이 문제를 해결하기 위하여 JPA는 개발자가 직접 객체에 대한 SQL를 작성하는 것이 아니라 JPA가 제공하는 API를 통해서 데이터베이스에 전달하게 된다.

**저장**

```text
void persist(Object entity)
    Make an instance managed and persistent.
Parameters:
    entity - entity instance
Throws:
    EntityExistsException - if the entity already exists. (If the entity already exists, the EntityExistsException may be thrown when the persist operation is invoked, or the EntityExistsException or another PersistenceException may be thrown at flush or commit time.)
    IllegalArgumentException - if the instance is not an entity
    TransactionRequiredException - if there is no transaction when invoked on a container-managed entity manager of that is of type PersistenceContextType.TRANSACTION
```

**조회**

```text
<T> T find(Class<T> entityClass,
           Object primaryKey)
    Find by primary key. Search for an entity of the specified class and primary key. If the entity instance is contained in the persistence context, it is returned from there.
Parameters:
    entityClass - entity class
    primaryKey - primary key
Returns:
    the found entity instance or null if the entity does not exist
Throws:
    IllegalArgumentException - if the first argument does not denote an entity type or the second argument is is not a valid type for that entity's primary key or is null
```

**수정**

JPA는 별도의 수정 메서드를 제공하지 않고, 조회된 객체를 변경하면 트랜잭션을 커밋할 때 UPDATE QUERY가 수행된다.

**연관 객체 조회**

JPA에서는 Lazy loading을 통해 연관된 객체를 사용할 때 불러올 수 있다. 따라서 개발자는 연관된 객체에 대한 접근을 걱정하지 않고 마음껏 조회할 수 있다.

애플리케이션은 발전하면서 그 내부는 증가하고 복잡해진다. 규모가 커지고 복잡해지는 건 유지보수에 직결적이고 유지보수가 어려운 애플리케이션은 치열한 시장 경쟁에서 살아남을 수 없다. 객체지향 프로그래밍은 이런 복잡성을 제어하기 위한 추상화, 캡슐화, 정보은닉, 상속, 다형성 등 다양한 장치를 제공한다. 하지만 이런 다양한 장치는 "객체지향"에서 가능하며 데이터베이스는 이를 알지 못한다. 따라서 다양한 도메인 모델을 표현하고 있는 객체를 저장하기 위해선 많은 문제점을 가지는데 단순히 "값"만 저장하기엔 객체들은 다른 객체와의 관계를 가지고 있으며 이 관계 또한 영구적으로 보존할 필요가 있지만 데이터베이스는 추상화, 상속, 다형성과 같은 개념이 없는 데이터 중심적인 구조이며 서로가 지향하는 목적이 다른 둘에게서 패러다임 불일치 문제가 발생한다. 때문에 개발자는 둘 사이에서 패러다임 불일치를 해결해야 하는데 이런 과정은 많은 시간과 코드를 소비하게 된다. 이때 JPA를 사용하면 개발자는 객체를 저장하듯이 데이터베이스에 저장하고 불러와 사용할 수 있다.

객체와 데이터베이스 각각에서 연관 관계된 데이터 또는 객체를 표현하기 위해선 아래와 같다.

* 객체는 참조를 통해 연관된 객체를 조회한다. 객체의 참조는 단방향으로 이뤄진다.
* 테이블은 외래 키를 이용해 조인하여 연관된 테이블에 조회한다. 두 테이블은 외래 키를 이용해 서로를 참조할 수 있다.

두 방식은 "객체 자체를 가지고 있는가 또는 식별 가능한 키를 가지고 있나"의 차이로 볼 수 있고 서로가 필요한 데이터의 구조 또한 다르다. 결국 둘 사이에서 개발자가 많은 시간과 코드를 사용해 서로 간의 패러다임을 일치시켜줄 필요가 있다. 여기서 JPA는 지연 로딩을 통해서 이 문제를 해결한다. 데이터베이스에서 불러온 연관 관계가 포함된 데이터를 객체에서 연관 관계에 접근할 때 SELECT QUERY를 통해 개발자에게 신뢰할 수 있는 그래프 탐색을 제공한다. 하지만 이런 지연 로딩은 2번의 쿼리를 발생하기 때문에 JPA는 필요한 경우 데이터를 즉시 불러올 수 있도록 허용한다. 즉시 로딩을 하는 경우 연관 테이블을 조인하여 데이터를 불러오게 된다.

데이터베이스는 기본 키의 값으로 각 로우를 구분한다. 하지만 객체에서 비교는 두 가지 비교로 나뉘게 된다.

* 객체 인스턴스의 주소 값을 비교
* 객체 내부의 값을 비교

이처럼 객체와 데이터베이스의 비교에는 차이가 있다. 그래서 데이터베이스의 같은 로우를 조회하더라도 객체의 동일성에서는 실패하는 경우가 발생하게 된다. 하지만 JPA에서는 같은 트랜잭션의 경우 같은 로우에 대해 같은 객체를 반환하여 이런 불일치성을 해결한다.

JPA는 자바 진영의 ORM 기술 표준이다. 쉽게 말해서 인터페이스를 모아둔 것이다. 따라서 JPA 표준을 준수한 프레임워크라면 기술에 의존하지 않고 언제든지 교체할 수 있도록 할 수 있는 것이 표준이다. 이 중 대중적으로 사용되는 ORM 프레임워크는 하이버네이트로 JPA의 기반이 되는 프레임워크이다.

