# JPA
- [1장 JPA 소개](#1장-JPA-소개)
- [2장 JPA 시작](#2장-JPA-시작)
- [3장 영속성 관리](#3장-영속성-관리)
- [4장 엔티티 매핑](#4장-엔티티-매핑)
- [5장 연관관계 매핑 기초](#5장-연관관계-매핑-기초)
- [6장 다양한 연관관계 매핑](#6장-다양한-연관관계-매핑)

## 1장 JPA 소개

JPA
- 자바 ORM 기술에 대한 API 표 명세
- JPA 2.1을 구현한 ORM 프레임워크는 하이버네이트, EclipseLink, DataNuculeus가 있으며 하이버네이트가 가장 대중적
- ORM 프레임워크 : 객체와 관게형 데이터베이스 간의 차이를 중간에서 해결

JPA CRUD
1. 저장
- persisit() 메소드는 객체를 데이터베이스에 저장
- JPA가 객체와 매핑정보를 보고 적절한 INSERT SQL을 생성해서 데이터베이스에 전달

2. 조회
- find() 메소드는 객체 하나를 데이터 베이스에서 조회
- JPA는 객체와 매핑정보를 보고 적절한 SELECT SQL을 생성해서 데이터베이스에 전달하고 그 결과로 객체를 생성해서 반환

3. 수정
- 별도의 수정 메소드를 제공하지 않는다.
- 객체를 조회해서 값을 변경하면 트랜잭션을 커밋할 때, 데이터베이스에 적절한 UPDATE SQL이 전달

패러다임의 불일치
1. 상속
- 객체는 상속이라는 기능을 가지고 있지만, 테이블은 상속이라는 기능이 없기 때문에 JPA를 사용하지 않는다면 객체를 분해해야 한다.
- JPA는 persist() 메소드를 사용하면 객체를 분해해서 저장하고, find() 메소드를 사용하면 여러 테이블을 조인해서 필요한 데이터를 조회하고 그 결과를 반환한다.

2. 연관관계
- 객체는 참조를 사용해서 다른 객체와 연관관계를 가지고 참조에 접근해서 연관된 객체를 조회한다.
- 테이블은 외래 키를 사용해서 다른 테이블와 연관관계를 가지고 조인을 사용해서 연관된 테이블을 조회한다.
- JPA는 개발자가 설정한 관계에 따라 객체 저장시 외래 키로 변환해서 적절한 INSERT SQL을 데이터 베이스에 전달한다. 

3. 객체 그래프 탐색
- SQL을 직접 다루면 처음 실행하는 SQL에 따라 객체 그래프를 어디까지 탐색할 수 있는지 정해진다.
- 하지만 비즈니스 로직에 따라 사용하는 객체 그래프가 다르기에 언제 끊어질지 모를 객체 그래프를 함부로 탐색할 수는 없다.
- JPA는 지연 로딩을 사용해서 연관된 객체를 실제 객체를 사용하는 시점까지 데이터베이스 조회를 미루며, 지연 로딩을 투명하게 처리한다.

4. 비교
- 동일성 : 객체 인스턴스의 주소 값을 비교
- 동등성 : 객체 내부의 값을 비교
- JPA는 같은 트랜잭션일 때 같은 객체가 조회되는 것을 보장한다.

JPA를 사용해야 하는 이유
- 생산성 : 지루하고 반복적인 코드와 CRUD용 SQL을 개발자가 직접 작성하지 않는다.
- 유지보수 : 개발자가 작성해야 했던 SQL과 JDBC API 코드를 JPA가 대신 처리해주므로 유지보수해야 하는 코드 수가 줄어든다.
- 패러다임의 불일치 해결
  - 상속, 연관관계, 객체 그래프 탐색, 비교
- 성능 최적화
- 데이터 접근 추상화와 벤더 독립성
- 표준

## 2장 JPA 시작

매핑 어노테이션
1. @Entity
- 이 클래스를 테이블과 매핑한다는 것을 JPA 알림
2. @Table
- 엔티티 클래스에 매핑할 테이블 정보
- 생략하면 클래스 이름을 엔티티 이름으로 매핑
3. @Id
- 엔티티 클래스의 필드를 테이블의 기본 키에 매핑
4. @Column
- 필드를 컬럼에 매핑
5. 매핑 정보가 없는 필드
- 필드명을 사용해서 컬럼명으로 매핑
- 대소문자를 구분하는 데이터베이스를 사용하면 명시적으로 매핑해야 한다.

속성
1. JPA 표준 속성 : 특정 구현체에 종속되지 않는 속성
- javax.persistence.jdbc.drive : JDBC 드라이버
- javax.persistence.jdbc.user : 데이터베이스 접속 아이디
- javax.persistence.jdbc.password : 데이터베이스 접속 비밀번호
- javax.persistence.jdbc.url : 데이터베이스 접속 URL
2. 하이버네이트 속성 : 하이버네이트 전용 속성으로 하이버네이트에서만 사용 가능
- hibernae.dialect : 데이터베이스 방언 설정
- 방언은 특정 데이터베이스만의 고유한 기능으로, 데이터 베이스가 변경되어도 애플리케이션 코드를 변경할 필요 없이 데이터베이스 방언만 교체하면 된다.

애플리케이션 개발
1. 엔티티 매니저 설정
- 엔티티 매니저 팩토리 생성 : persistence.xml에서 이름이 jpabook인 영속성 유닛을 찾아서 엔티티 매니터 팩토리 생성, 애플리케이션 전체에서 딱 한번만 생성하고 공유해서 사용
- 엔티티 매니저 생성 : 엔티티 매니저 팩토리에서 엔티티 매니저 생성, 엔티티를 데이터베이스에 등록/수정/삭제/조회할 수 있음, 데이터베이스 커넥션과 밀접한 관계가 있으므로 스레드 간에 공유하거나 재사용하면 안된다.
- 종료 : 사용이 끝난 엔티티 매니저는 종료, 애플리케이션을 종료할 때에는 엔티티 매니저 팩토리 종료

2. 트랜잭션 관리
- JPA는 항상 트랜잭션 안에서 데이터를 변경
- 트랜잭션을 시작하려면 엔티티 매니저에서 트랜잭션 API를 받아와야 한다.
- 트랜잭션 API를 사용해서 비즈니스 로직이 정상 동작하면 트랜잭션을 커밋하고 예외가 발생하면 롤백

3. 비즈니스 로직

## 3장 영속성 관리

엔티티 매니저 팩토리와 엔티티 매니저
1. 엔티티 매니저 팩토리
- 생성 비용이 커서 한 개만 만들어서 애플리케이션 전체에서 공유
- 여러 스레드가 동시에 접근해도 안전하므로 서로 다른 스레드 간에 공유

2. 엔티티 매니저
- 엔티티 매니저 팩토리에서 생성하는 비용은 거의 들지 않는다.
- 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안 된다.
- 데이터베이스 연결이 꼭 필요한 시점까지 커넥션을 얻지 않는다.

영속성 컨텍스트
- 엔티티를 영구 저장하는 환경
- 엔티티 매니저를 생성할 때 하나 만들어진다.
- 영속 상태는 식별자 값(@Id로 테이블의 기본 키와 매핑한 값)이 반드시 있어야 한다.
- 플러시 : 트랜잭션을 커밋하는 순간 영속성 컨텍스트에 새로 저장된 엔티티를 데이터베이스에 반영
- 1차 캐시 : find() 호출 시, 1차 캐시에서 식별자 값으로 조회 후, 엔티티가 있으면 데이터베이스를 조회하지 않고 메모리에 있는 1차 캐시에서 엔티티를 조회한다. 
없으면 엔티티 매니저는 데이터베이스를 조회해서 엔티티를 생성하고 1차 캐시에 저장한 후에 영속 상태의 엔티티를 반환한다.
- 동일성 보장 : find()를 반복해서 호출해도 영속성 컨텍스트는 1차 캐시에 있는 같은 엔티티 인스턴스를 반환하여 동일성을 보장
- 쓰기 지연 : 트랜잭션을 커밋하기 직전까지 데이터베이스에 엔티티를 저장하지 않고, 트랙잭션을 커밋할 때 모아둔 쿼리를 데이터베이스에 보낸다.
- 변경 감지 : JPA는 엔티티를 영속성 컨텍스트에 보관할 때, 최초 상태를 복사해서 저장한다(스냅샷). 
그리고 플러시 시점에 스냅샷과 엔티티를 비교해서 변경된 엔티티를 찾는다. 변경된 엔티티가 있으면 수정 쿼리를 생성해서 쓰기 지연 SQL 저장소에 보낸다.
- 지연 로딩 : 실제 객체 대신 프록시 객체를 로딩해두고 해당 객체를 실제 사용할 때 영속성 컨텍스틀ㄹ 통해 데이터를 불러온다.

엔티티 생명주기
1. 비영속
- 영속성 컨텍스트와 전혀 관계가 없는 상태
- 객체를 생성하고 저장하지 않은 상태

2. 영속
- 영속성 컨텍스트에 저장된 상태
- 영속성 컨텍스트에 의해 관리

3. 준영속
- 영속성 컨텍스트에 저장되었다가 분리된 상태
- 영속성 컨텍스트가 관리하지 않음으로, 영속성 컨텍스트가 제공하는 기능을 사용할 수 없다.
- detach() : 특정 엔티티를 영속성 컨텍스트로부터 분리
- clear() : 영속성 컨텍스트를 초기화해서 해당 영속성 컨텍스트의 모든 엔티티를 준영속 상태로 만드는 것
- close() : 영속성 컨텍스트를 종료해서 해당 영속성 컨텍스트가 관리하던 영속 상태의 엔티티를 모두 준영속 상태로 만드는 것

4. 삭제
- 엔티티를 영속성 컨텍스트와 데이터베이스에서 삭제된 상태


병합
- 준영속, 비영속 상태의 엔티티를 다시 영속 상태로 변경하는 방법
- 준영속, 비영속 상태의 엔티티를 받아서 그 정보로 새로운 영속 상태의 엔티티를 반환

## 4장 엔티티 매핑

스키마 자동 생성
- persistence.xml에 <property name="hibernate.hbm2ddl.auto" value="create" /> 속성 추가
- 애플리케이션 실행 시점에 value 값에 따라 DDL 수행
- create : 기존 테이블을 삭제하고 새로 생성한다. DROP + CREATE
- create-drop : create 속성에 추가로 애플리케이션을 종료할 때 생성한 DDL을 제거한다. DROP + CREATE + DROP
- update : 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 변경 사항만 수정한다.
- validate: 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않는다.
- none : 자동 생성 기능을 사용하지 않기 위한 유효하지 않은 옵션

@Entity
- 테이블과 매핑할 클래스
- 기본 생성자는 필수
- final, enum, interface, inner 클래스에는 사용할 수 없다.
- 저장할 필드에 final을 사용하면 안 된다.

@Table
- 엔티티와 매핑할 테이블을 지정
- 생략하면 매핑한 엔티티 이름을 테이블 이름으로 사용
- uniqueContraints 속성으로 유니크 제약조건 DDL 생성

@Column
- 객체 필드를 테이블 컬럼에 매핑
- nullable 속성으로 null 값의 허용 여부를 설정하는 DDL 생성, 기본 값은 true
- @Column 생략하여도 기본값으로 적용되지만, 자바 기본 타입인 경우에는 null값을 입력할 수 없다. ( 생성된 DDL에 not null 조건이 자동으로 붙는다. )

@Enumerated
- 자바의 enum 타입을 매핑

@Temporal
- 날짜 타입을 매핑
- TemporalType.DATE : 날짜, 데이터베이스 date 타입과 매핑
- TemporalType.TIME : 시간, 데이터베이스 time 타입과 매핑
- TemporalType.TIMESTAMP : 날짜와 시간, 데이터베이스 timestamp 타입과 매핑

@Lob
- BLOB, CLOB 타입과 매핑

@Transient
- 필드를 매핑하지 않을 때 사용
- 데이터베이스에 저장하지 않고 조회하지도 않는다.
- 객체에 임시로 어떤 값을 보관하고 싶을 때 사용

@Access
- JPA가 엔티티 데이터에 접근하는 방식을 지정
- AccessType.FIELD : 필드 접근, 필드에 직접 접근하는 것으로 필드 접근 권한이 private이어도 접근할 수 있다.
- AccessType.PROPERTY : 프로퍼티 접근, 접근자(Getter)를 사용한다.

기본 키 매핑
- 직접 할당 : em.persist() 를 호출하기 전에 애플리케이션에서 직접 식별자 값을 할당해야 한다. 만약 식별자 값이 없으면 예외 발생
- SEQUENCE : 데이터베이스 오브젝트인 시퀀스에서 식별자 값을 획득한 후 영속성 컨텍스트에 저장
- TABLE : 데이터베이스 시퀀스 생성용 테이블에서 식별자 값을 획득한 후 영속성 컨텍스트에 저장
- IDENTITY : 테이터베이스에 엔티티를 저장해서 식별자 값을 획득한 후 영속성 컨텍스트에 저장
- AUTO : 선택한 데이터베이스 방언에 따라 IDENTITY, SEQUENCE, TABLE 전략 중 하나를 자동으로 선택

## 5장 연관관계 매핑 기초

단방향 연관관계
- 둘 중 한 쪽만 참조하는 관계
- @ManyToOne : 다대일 관계라는 매핑 정보 어노테이션
- @JoinColumn : 외래 키 매핑 어노테이션, name 속성에 매핑할 외래 키 이름을 지정, 생략 가능

연관관계 사용
1. 저장 : JPA는 참조한 대상의  식별자를 외래 키로 사용해서 적절한 등록 쿼리를 생성
2. 조회
- 객체 그래프 탐색 : 객체를 통해 연관된 엔티티를 조회하는 것
- 객체지향 쿼리 사용(JPQL)
3. 수정 : 엔티티의 값만 변경해두면 트랜잭션을 커밋할 때 플러시가 일어나면서 변경 감지 기능 작동
4. 제거 : 연관 관계를 NULL로 설정
5. 삭제 : 연관관계를 먼저 제거한 후, 연관된 엔티티를 삭제

양방향 연관관계
- 연관관계 주인 : 두 객체 연관관계 중 하나를 정해서 테이블의 외래키를 관리하기 위함, 테이블에 외래 키가 있는 곳으로 정한다.
- @OneToMany : 일대다 관계라는 매핑 정보 어노테이션
- @mappedby : 양방향 연관관계일 때, 연관관계의 주인이 아니면 연관간계의 주인을 지정
- 객체 관점에서 양쪽 방향에 모두 값을 입력해주는 것이 가장 안전하다.
- 연관관계를 변경할 때에는 기존 연관관계를 삭제

## 6장 다양한 연관관계 매핑

다대일
- 외래키는 항상 다쪽에 있다.
- 객체 양방향 관계에서 연관관계의 주인은 외래키를 갖는 다쪽이다.

일대다
- 다대일 관계의 반대 방향
- 매핑한 객체가 관리하는 외래 키가 다른 테이블에 있어서 관리가 어렵기에 다대일 양방향 매핑을 사용하는 것이 좋음

일대일
- 주 테이블에 외래 키 : 주 테이블에 외래 키를 두고 대상 테이블을 참조
- 대상 테이블에 외래 키 : 대상 테이블에 외래키를 두는 것으로 테이블 관계를 일대일에서 일대다로 변경할 때 테이블 구조를 그대로 유지할 수 있음

다대다
- 다대다 관계는 정규화된 테이블 2개로 표현할 수 없어서 일대다, 다대일 관계로 풀어내는 연결 테이블 사용
- 엔티티 간의 관계에서도 연관 테이블과 같은 엔티티가 추가되어야 하며, 복합 기본 키를 사용한다.
- 식별 관계 : 받아온 식별자를 기본 키 + 외래 키로 사용
- 비식별 관계 : 받아온 식별자는 외래 키로만 사용하고, 새로운 식별자(데이터베이스에서 자동으로 생성해주는 대리 키)를 추가

복합 기본 키
- 별도의 식별자 클래스로 생성
- Serializable을 구현
- equals와 hashCode 메소드를 구현
- 기본 생성자가 있어야 한다.
- 식별자 클래스는 public
- @IdClass를 사용하는 방법 외에 @EmbeddedId를 사용


