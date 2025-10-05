# SQL

## 환경설정(복습)

> Linux에 도커 가상서버(DB를 위한 가상서버) 만들기

- (개인별 리눅스 서버 접속) $ ssh oci-server

- Linux update

  $ sudo apt-get update

  $ sudo apt-get upgrade

  $ sudo apt install net-tools

- nginx

  $ sudo apt install nginx

- 서버 방화벽

  $ ufw allow 8080/tcp (우분투 표준 방화벽 관리)

  $ sudo iptables -I INPUT 1 -p tcp --dport 80 -j ACCEPT (OCI 표준으로 80, 5000, 3389, 5432 등 필요한 tcp port를 오픈한다.)

- docker 설치

  $ apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

  $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

  $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

  $ sudo apt-get update

  $ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose

  $ sudo systemctl status docker : docker daemon이 정상적으로 떠 있는지 확인

- DB 데이터를 위한 가상서버 만들기

  $ date : 타임존 확인

  $ sudo timedatectl set-timezone Asia/Seoul : 타임존 확인시 UTC로 확인되면 해당 명령어로 date timezone Asia/Seoul로 변경

  $ nano docker-compose.yml

  ```yaml
  version: '3.4'
  services:
    db_singlepoint:
      image: pgvector/pgvector:pg17
      container_name: "pg-postgres"
      environment:
        POSTGRES_PASSWORD: "dbtest1212"
        TZ: "Asia/Seoul"
      volumes:
        - postgres_data:/var/lib/postgresql/data
        - ./init:/docker-entrypoint-initdb.d
      restart: unless-stopped
      network_mode: "host"
  
  volumes:
    postgres_data:
  ```

  $ docker compose up -d : postgreSQL DB 컨테이너(DB를 갖고있는 가상서버) 생성 => compose에서 작성한 image를 docker hub(hub.docker.com)에서 받아서 설치

  $ docker container ls : 컨테이너 상태 확인

  <img width="902" height="56" alt="Image" src="https://github.com/user-attachments/assets/30935b7f-1240-4656-85b7-4ba19a86cb59" />

  $ docker exec -it 컨테이너ID /bin/bash : 컨테이너 내부 shell 환경에서 실행

  >:bulb: `uname -a`, `df`를 할 경우 도커는 호스트의 이름을 그대로 가져오다보니 가상서버 환경과 도커 환경이 구분이 안된다? 도커 서버의 루트에서 `ls`로 확인해보면 항상 `docker-entrypoint-initdb.d`폴더가 존재함.

- 가상서버 내에 DB 만들기

  $ psql -U postgres

  (DB환경) postgres=# create user test01 password 'test1234' superuser;	=>	(DDL; Data Definition Language) 사용자 생성하기. 사용자명 test01, 비밀번호 test1234. 이 사용자는 superuser(데이터베이스 내 모든 권한을 가지는 슈퍼유저 계정)

  postgres=# create database testdb;	=>	새로운 데이터베이스 testdb 생성

  postgres=# grant all privileges on database testdb to test01;	=>	test01 사용자에게 testdb 데이터베이스에 대한 모든 권한을 부여. 이렇게 되면 test01이 testdb에 접속해 테이블 생성, 데이터 조작 등을 할 수 있음.

  postgres=# CREATE EXTENSION IF NOT EXISTS vector;	=>	vector 확장을 설치(없으면 생성). pgvector 라이브러리에서 제공하는 벡터 타입 및 함수를 추가하는 명령어로써 AI / ML 에서 임베딩 데이터를 다루거나, 벡터 검색(Vector Similarity Search)을 구현할 때 필요함

  postgres=# \q

- GUI Client와 DB 연결하기

  $ sudo iptables -I INPUT 1 -p tcp --dport 5432 -j ACCEPT (OCI 표준)	=>	먼저 방화벽을 열고나서 DB Connect를 시도해준다.

## SQL

> SQL; Structed Query Language.
>
> 관계형 데이터베이스(RDBMS)를 다루기 위한 표준언어로써 데이터베이스 전체를 다루는 언어로 이해하자.
>
> SQL은 목적에 따라 몇 가지 하위언어로 나뉘게 된다.

- postgres=# \du : 생성한 사용자 목록 확인

  postgres=# psql -U test01 -d testdb	=>	psql -U 사용자명 -d 데이터베이스명. 

  - `psql`  : postgreSQL 실행
  - `-U <user>` : 어떤 DB 사용자 계정으로 접속할지 지정
  - `-d <dbname>` : 어떤 데이터베이스에 접속할지 지정

- postgres=# \l	=>	데이터베이스 목록 확인

  SELECT current_database(), current_schema();	=>	현재 접속한 데이터베이스, 스키마 확인

  postgres=# \d student	=>	테이블 구조(스키마) 보기

  postgres-# \t	=>	Tuples only 모드 전환(결과에서 헤더와 구분선 제거)

  postgres-# \dt	=>	Describe Tables. 현재 DB 안의 테이블만 목록으로 보여줌

### DDL(Data Definition Language)

- 데이터 정의 언어. 스키마나 객쳬(테이블, 뷰, 인덱스, 사용자 등)의 구조 정의/수정/삭제

- CREATE(생성), ALTER(구조 변경), DROP(삭제)

- 관례 : 테이블명 / 컬럼명 => 소문자 + snake_case

  ```sql
  CREATE TABLE student(idx SERIAL, name VARCHAR(50), age INT, dept VARCHAR(50), grade CHAR(1), reg_date TIMESTAMPTZ DEFAULT now() );
  
  ALTER TABLE student ADD stuent_nm varcher;
  ```

### DML(Data Manipulation Language)

- 데이터 조작 언어. 테이블 안의 실제 데이터를 다루는 언어

- SELECT(조회), INSERT(삽입), UPDATE(수정), DELETE(삭제)

  ```sql
  # 원하는 컬럼 확인. student 테이블 모든 데이터 조회
  SELECT * FROM student;
  
  INSERT INTO student(name, age, dept) VALUES ('홍길동', 22, '산업공학');
  
  # 나이가 가장 어린 학생들 고르기
  SELECT * FROM student WHERE age IN (SELECT MIN(age) FROM student);
  
  # 나이가 21보다 작은 사람 조회
  SELECT * FROM student WHERE age < 21;
  
  # 나이가 20보다 큰 사람 수
  SELECT COUNT(1) FROM student WHERE age > 20;
  
  SELECT dept, COUNT(1) FROM student GROUP BY dept;
  SELECT dept, COUNT(1) AS dept_num FROM student GROUP BY dept;
  
  # 학과 별 나이 합계
  SELECT dept, SUM(age) AS dept_age_sum FROM student GROUP BY dept;
  
  SELECT * FROM student WHERE dept like '산업%';
  SELECT * FROM student WHERE dept in ('건축공학', '산업공학');
  
  SELECT * FROM student WHERE idx BETWEEN 3 and 5;
  SELECT * FROM student WHERE idx > 2 and idx < 6;
  
  # inner join
  SELECT A.name, A.dept, B.course, B.score from student A join student_score B on A.name=B.student.nm;
  # outer join
  SELECT A.name, A.dept, B.course, B.score from student A left outer join student_score B on A.name=B.student.nm WHERE A.dept='건축공학';
  
  UPDATE student set age=25 WHERE name="홍길동"
  
  DELETE from student where idx=1;
  ```

### DCL(Data Control Language)

- 데이터 제어 언어. 권한과 보안 관련
- GRANT(권한 부여), REVOKE(권한 회수)

### TCL(Transaction Control Language)

- 트랜잭션 제어 언어

- COMMIT, ROLLBACK, SAVEPOINT

  ```sql
  # DB액션들을 실제로 파일에 반영
  commit;
  
  # 지금까지 한 작업 취소하고 처음부터 다시시작
  rollback;
  ```


### 벡터 조회

```sql
# 테이블 생성
CREATE TABLE product_embeddings (id SERIAL PRIMARY KEY, name TEXT, embedding VECTOR(3)); -- 벡터의 크기를 3으로 설정

# 데이터 삽입
INSERT INTO product_embeddings (name, embedding) VALUES ('Product A', '[0.1, 0.2, 0.3]'), ('Product B', '[0.5, 0.6, 0.1]'), ('Product C', '[0.9, 0.8, 0.7]');

# 데이터 조회 - 유사도 가장 우선순위에 있는 것(similarity가 가장 작은 값)
SELECT id, name, embedding <=> '[0.2, 0.3, 0.4]' AS similarity FROM product_embeddings ORDER BY similarity LIMIT 1;

# 데이터 조회
SELECT id, name, embedding <=> '[0.2, 0.3, 0.4]' AS similarity FROM product_embeddings ORDER BY similarity;
```

