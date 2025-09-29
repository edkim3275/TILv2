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

### DDL(Data Definition Language)

- 데이터 정의 언어. 스키마나 객쳬(테이블, 뷰, 인덱스, 사용자 등)의 구조 정의/수정/삭제
- CREATE(생성), ALTER(구조 변경), DROP(삭제)

### DML(Data Manipulation Language)

- 데이터 조작 언어. 테이블 안의 실제 데이터를 다루는 언어
- SELECT(조회), INSERT(삽입), UPDATE(수정), DELETE(삭제)

### DCL(Data Control Language)

- 데이터 제어 언어. 권한과 보안 관련
- GRANT(권한 부여), REVOKE(권한 회수)

### TCL(Transaction Control Language)

- 트랜잭션 제어 언어
- COMMIT, ROLLBACK, SAVEPOINT