# MySQL

- MySQL은 언어? 데이터베이스?

- MySQL은 언어가 아니고, 관계형 데이터베이스 관리 시스템(RDBMS)임. 그리고 우리가 MySQL을 쓸 때 SQL(Structured Query Language)이라고 하는 언어로 데이터를 다루는 것.

  MySQL = DBMS(데이터를 저장하고 관리해주는 소프트웨어)

  SQL = 데이터를 다루기 위한 언어

- 예를들어 아래처럼 쓰는 건 SQL 문법. 이를 MySQL이 해석해서 처리하는 것

  ```sql
  SELECT * FROM users WHERE age > 10;
  ```

- MySQL 말고도 쓸 수 있는 DB는 많다.

  - PostgreSQL(많이 사용되는 오픈소스 RDBMS)
  - MariaDB(MySQL 포크, 거의 호환됨)
  - Amazon Aurora(MySQL 호환 버전 포함)
  - NoSQL 계열 : MongoDB, DynamoDB 등(다만 구조가 다름)

- MySQL로 사용되고 있던 서비스가 다른 DB로 간단하게 변경되기 어려운 이유

  - 쿼리 문법 차이

    DB마다 미묘하게 SQL 문법이 달라서 코드 수정 필요

  - 라이브러리/ORM 호환성

    사용 중인 백엔드 프레임워크나 ORM이 MySQL에 특화되어 있을 수도 있음

  - 데이터 마이그레이션

    DB를 옮기려면 기존 데이터를 안전하게 옮겨야 함. 실수나 오류가 나면 서비스에 치명적일 수 있음

  - 시간 + 인력 + 돈

- 우리가 흔히 "DB"라고 말하는 건 보통 "DBMS + 그 안에 있는 데이터"를 의미한다.

  예를 들어 "MySQL을 써요"는 "MySQL이라는 DBMS를 사용해서 데이터를 저장하고 있어요"라는 의미

### Question

- SQL의 종류

  SQL은 공통 언어임. 하지만 DBMS마다 약간씩 다르게 구현해서(이를 SQL dialect(방언)이라고 부름) 기본적인 SQL 문법은 거의 같지만 세부적인 함수나 문법, 성능 최적화 방식은 다르다.

- RDBMS vs. DBMS

  | 구분       | DBMS                     | RDBMS                                      |
  | ---------- | ------------------------ | ------------------------------------------ |
  | 정의       | 데이터 저장/관리 시스템  | 관계형 데이터 저장 시스템                  |
  | 데이터구조 | 자유로움(파일, 키-값 등) | 테이블 기반(행/열)                         |
  | 예시       | MongoDB, Redis           | MySQL, PostgreSQL, Oracle                  |
  | 관계(연결) | 거의 없음                | 외래키(FK) 등으로 테이블 간 관계 표현 가능 |
  | 표준       | 특정 표준 없음           | 대부분 SQL 기반 사용                       |

  모든 RDBMS는 DBMS이지만, 모든 DBMS가 RDBMS는 아니다.

- NoSQL?

  NoSQL = Not Only SQL

  기존 RDBMS와는 다른 방식으로 데이터를 저장하는 DBMS

  비정형데이터에 강함(JSON, 문서 구조 등), 수평 확장성 우수(분산 처리), 스키마 없음 / 유연함

- SQL과 NoSQL의 가장 큰 차이: 스키마 강제 여부

  SQL의 경우 스키마가 고정된다. 즉, 테이블을 만들 때 컬럼과 타입이 반드시 사전에 정해져야 하고, 모든 데이터는 이 스키마를 반드시 따라야 한다.

  하지만 NoSQL의 경우 스키마가 강제되지 않는다. 예를 들어 MongoDB의 경우 JSON 문서 형태로 저장되고 각 문서마다 필드 구성이 달라도 문제가 없다. 컬렉션 안에 있는 데이터끼리 서로 다른형태여도 된다는 것.(스키마도 자유롭고 확장도 쉬움)

  ```json
  // 문서 1
  {
    "name": "Edgar",
    "age": 28,
    "city": "Seoul"
  }
  
  // 문서 2 (age 없음, 새로운 필드 hobby 추가)
  {
    "name": "Anna",
    "hobby": "Piano"
  }
  
  // 문서 3 (데이터 타입이 다름)
  {
    "name": "Ken",
    "age": "Twenty nine",    // 문자열 타입
    "city": ["Tokyo", "Osaka"]  // 배열
  }
  
  // 문서 4 (완전히 다른 구조)
  {
    "product": "MacBook Pro",
    "price": 3200000,
    "spec": { "cpu": "M3", "ram": "16GB" }
  }
  ```

- ORM(Object-Relational Mapping)

  프로그래밍 언어의 객체(Object)와 데이터베이스의 테이블/행(Row)을 자동으로 연결해주는 도구/라이브러리 => 코드를 DB 처럼 사용하게 도와줌

  전통적인 SQL 방식

  ```js
  const result = await db.query('SELECT * FROM users WHERE id = ?', [userId]);
  ```

  ORM 방식(Sequelize)

  ```js
  const user = await User.findByPk(userId);
  ```

  대표적인 ORM으로는 JavaScript(Sequelize, TypeORM, Prisma), Python(SQLAlchemy, Django ORM), Java(Hibernate), Ruby(ActiveRecord)

- 비정형 데이터(Unstructured Data)

  정형 데이터는 형태가 정해져 있는 데이터, 비정형 데이터는 자유로운 구조의 데이터를 말한다.

  ```json
  {
    "id": 1,
    "name": "Alice",
    "hobbies": ["reading", "gaming"],
    "profile": {
      "bio": "Hello!",
      "location": "Seoul"
    }
  }
  ```

  이러한 데이터는 구조가 자유롭고, 컬럼도 고정되어 있지 않다. 이런 데이터를 저장하려면 NoSQL이 훨씬 유리하다.

- 수평 확장성(Horizontal Scaling)

  '확장성'은 서비스가 커질 때 성능을 어떻게 늘릴 수 있느냐에 대한 이야기임.

  수직 확장(Vertical Scaling)은 기존 서버의 CPU, 메모리를 늘리는 경우, 수평확장은 서버 여러대를 추가해서 부하를 분산하는 경우.

  NoSQL은 구조상 여러 서버로 분산 저장하기 쉽게 만들어졌다. 따라서 대규모 트래픽이나 데이터를 처리할 때 수평 확장이 유리하다. 반면 RDBMS는 데이터가 테이블로 얽혀 있어서 분산 처리에 더 복잡하다.

- 스키마(Schema)

  스키마란 데이터 구조/설계도라고 생각하면 된다. "이 테이블에는 어떤 컬럼이 있고, 어떤 타입을 가지는지"에 대한 정의다.