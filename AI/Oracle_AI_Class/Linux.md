# DAY4

## Linux

### Linux 명령어

- DAY3에 이어서 리눅스 명령어 진행예정

- $ sudo su - : 일반 사용자가 관리자(root) 권한을 빌려서 명령어를 실행할 수 있게 해주는 명령어.

  sudo는 superuser do의 약자, su는 substitue user(사용자 전환)의 약자. 즉, root 계정으로 완전히 들어간다는 의미

  :bulb: root 계정은 리눅스/유닉스 시스템에서 최고 권한(관리자 권한)을 가진 계정. 리눅스에서는 계정 마다 환경(홈 디렉토리, 환경변수, 실행 권한)이 다른데 위에서 sudo su 만 하면 사용자 전환만 되고 환경은 기존 계정 걸 일부 유지하고, su 뒤에 하이픈(-)을 붙이면 계정의 환경까지 전부 적용해서 root 계정으로 '로그인한 것과 동일한 상태'를 만든다.

  su 또는 su - 입력시 나오는 password는 root계정의 비밀번호를 입력하라는 것.

  :question: sudo su - 는 되고, su - 는 안되는 이유는 현재 계정이 sudo 그룹에 속한 사용자이기 때문에 가능하고, su - 는 비밀번호를 물어봄(리눅스 배포판에 따라 보안정책의 상이함에 따라 발생하는 이슈)

- $ netstat : 네트워크 연결 상태(network statistics)를 확인

  내 서버(혹은 PC)가 어떤 포트로 통신 중인지, 누가 접속해 있는지 확인할 수 있음

  - 현재 열려 있는 네트워크 연결(소켓)의 상태
  - TCP/UDP 연결 정보
  - 라우팅 테이블
  - 네트워크 인터페이스 상태

  $ netstat -nlpt : 좀 더 구체적인 정보 확인 가능

  현재 열려 있는 TCP 포트 + 어떤 프로그램이 점유 중인지 숫자로 확인

  `-n` : 호스트 이름 대신 숫자 주소(IP/포트)로 표시

  `-l` : **listening 상태(대기중인) 포트**만 표시

  `-p` : 해당 포트를 열고 있는 프로세스와 PID까지 표시(어떤 프로세스가 해당 포트를 점유 중인지)

  `-t` : **TCP**연결만 표시

  ```bash
  # 예시출력
  $ netstat -nlpt
  Active Internet connections (only servers)
  Proto Recv-Q Send-Q Local Address   Foreign Address  State   PID/Program name
  tcp        0      0 0.0.0.0:22      0.0.0.0:*        LISTEN  1234/sshd
  tcp6       0      0 :::80           :::*             LISTEN  5678/nginx
  ```

  `tcp` : TCP 연결

  `0.0.0.0:22` : 22번 포트(SSH)가 모든 IP에서 접속을 기다림

  `LISTEN` : 대기 중인 상태

  `1234/sshd` : PID가 1234번인 프로세스(sshd)가 포트 22를 열고 있음

- 리눅스에선 `cat /etc/hosts/`에서 공인 IP를 닉네임으로 매핑해놓는 것이 가능하다. 이렇게 하면 다음 ssh 접속 시 해당 공인 IP는 매핑된 닉네임으로 작성이 가능해진다.

  예. GPU 서버이동 : $ ssh user@L40s

  :bulb: L40, H200

- $ df -k : disk 사용량 확인

- 공용서버에서 진행

  $ usermod -a -G sudo edu000 : 특정 user에게 sudo 권한 주기

  $ ls -la docu : documnetaries 권한 확인

  $ python3 -m venv mytest : python 가상환경 만들기

  $ source mytest/bin/activate : 가상환경 활성화

  $ telnet IP port : 특정 IP 포트로 접속하는 명령어지만. 현재는 잘 사용하지 않고 해당 서버와 연결이 되는지 확인하는 용도로 쓰임

  $ curl url : connect url

  $ wget : 웹에서 파일을 다운로드 받는 명령어 도구(World Wide Web get)

  ```bash
  # 예시출력
  $ wget [옵션] [URL]
  $ wget https://example.com/file.zip
  $ wget -O myfile.zip https://example.com/file.zip # 저장할 파일 이름 지정
  $ wget -P /home/user/Downloads https://example.com/file.zip # 저장 경로 지정
  ```

  $ ufw : 우분투 방화벽

  $ date : 현재 시간 확인

  $ sudo timedatectl set-timezone Asia/Seoul 또는 $ sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime : date에서 UTC로 설정되어 있을 때 한국시간으로 변경해주어야 하는 경우

- $ ls : 파일 및 디렉토리 목록(기본적인 이름만 간단하게)

  $ ls -al : 옵션 두 개가 합쳐진 것

  `-a` (all) : 숨김 파일(.으로 시작하는 파일)까지 표시

  `-l` (long format) : 자세한 정보 표시 

  ```bash
  $ ls -al
  -rw-r--r--   1     user   user   1234   Sep 26 12:00 file.txt
  drwxr-xr-x   2     user   user   4096   Sep 26 11:59 Documents
  # 파일권한    링크수   소유자   그룹   파일크기   마지막수정시간  파일이름
  ```

  $ ls -IR : 특정 파일 패턴을 제외하고, 디렉토리 전체(하위폴더 포함)를 보여주는 명령어

  `-I` : 특정 패턴의 파일을 목록에서 제외

  `-R` : 하위 디렉토리까지 재귀적으로 출력

  $ ls -lrt : 가장 오래된 파일부터 순서대로 나열(오래된 순으로 볼때 씀) 반대는 $ ls -lt

  `-l` (long) : 자세히

  `-r` (reverse) : 역순

  `-t` (time) : 시간순

  $ ls -Fa : 숨김파일까지 다 보여주되, 각 파일 이름 뒤에 구분 기호 붙여서 종류 표시

  `-F` : 파일 이름 뒤에 구분 기호(suffix)를 붙여서 파일 종류 한눈에 구분하도록해준다. `/`는 디렉토리, `*`는 실행파일, `@`는 심볼릭 링크, `|`는 FIFO(named pipe)

  `-a` (all) : 숨김파일 까지 표시

- $ cd : 이동

  :bulb: 명령어 한꺼번에 할 경우 세미콜론으로 구분 가능

  ```bash
  $ cd ..; ls -l; pwd
  ```

  $ cd .. : 이전으로 이동

   . : . 이 의미하는 것은 현재폴더

  .. :  이전 폴더(parent directory)

  :bulb: 현재폴더를 지정하는 이유는 path가 현재폴더로 지정되지 않는 경우가 있음. 현재 폴더 파일을 찾지 못하는 경우가 있을 때 `./파일명.파일형식` 이런식으로 하면 찾아질 수 있음

- $ rm : 삭제

  $ rm 파일명 : 파일삭제

  $ rm -r 폴더명 : 폴더삭제

  :warning: $ rm -rf 폴더명 : 강제삭제

- $ cp : 복사

  $ cp -r dir1/src dir2/to : dir1의 src파일을 dri2의 to 경로로 복사

- $ cat 파일명 : 파일 확인

  $ cat > 파일명 : 파일 생성

  $ cat -n 파일명 : 순서로 파일 확인

- $ whereis bash : bash 위치 찾기

- $ mv : 파일 이름, 위치 변경

- $ ln : 링크

- $ ln -s file file

  바로가기에 해당하는 것이 리눅스에서 ln

  - 예시 `cd /etc/nginx`로 가서 `sites-available`에 `default`와 내용의 유사하지만 포트만 8080인 `mystic.conf` 파일을 생성한다. 이를 실제로 켜주기 위해서는 `/etc/nginx/sites-enable`에서 해당 파일을 걸어주어야만 한다.
  - `ln -s ../sites-available/mysite.conf ./mysite.conf`

- ..

- $ grep : string 패턴 찾기

  :star: $ grep -i print *.py : .py파일에서 print 문구 찾기

  $ grep -l -n print *.py : 지정된 폴더만 검색

  $ grep -r -n -i --include="*.py" "print" : 하위 디렉토리까지 전부 찾기

  $ grep -r -n -i --include="*.py" "print | more" : 필터걸어서 조금씩(한페이지씩) 확인

  -i : 대소문자 구분안한다는 의미(없으면 대소문자 구분 됨)

- ...

### 중요개념

- pipe => `|` 뒤쪽에 필터 명령(특정 결과물을 한 번 더 가공)이 온다. 예. `... | more`, `grep -i print *.py | grep model`

  filter  => sort, more 같은 필터링 명령들

  redirection => `>` 뒤쪽에 주로 파일이 온다 예. `python3 test.py > Test-result.txt`

  . => 현재 디렉토리

  .. => 부모 디렉토리

  `/` (루트 디렉토리): root 폴더(디렉토리), 리눅스 파일 시스템의 최상위 디렉토리(모든 디렉토리와 파일은 / 밑에 위치)

  `/bin` : 실행파일, `/etc` : 설정파일, `/home` : 사용자들의 홈 디렉토리(모여있는 곳)

  `/home` : 사용자 계정들의 홈 디렉토리가 모여 있는 디렉토리. 예를들어 사용자 계정이 edu000 이라면 홈 디렉토리는 /home/edu000

  `~`  (틸드, tilde) : 현재 로그인한 사용자 계정의 홈 디렉토리를 가리키는 특별한 단축 표현

  로그인 한 계정이 edu000이라면 ~ = /home/edu000

  즉, ~ 는 /home 자체가 아니라, 내 계정 전용 홈 디렉토리를 가리키는 심볼

- $ ls -l ~/temp : 홈 디렉토리 밑의 temp

  $ ls -l temp : 현재 폴더 밑의 temp

  $ ls -l /tmp : 루트 폴더 밑의 temp

  => `/home`과 `~` 차이 확인

  => home, /tmp와 temp와 ~/temp의 구분

=> 리눅스 개요는 완료 :raised_hands: 내일은 서버에서 파이썬 코딩할때 DB에 데이터 넣고, 데이터 가져오는 인터페이스 만들겠음.

### docker

> 도커는 기본적으로 시스템관리(SE)들이 사용하기때문에 적당히 아는 수준이면됨

- 도커가 편한 상황

  서비스별로 개발시점이 달라서 MariaDB 버전이 다 다르고, 어떤건 Oracle 9i, 어떤건 oracle10g , 어떤건 oracle23 을 사용한다고 하자. 보통 DB 서버를 3개 정도 올리면 뻑남. => 예시사진. 이 때 가장 좋은것이 도커 APP1, 2, 3 이 DB라고 생각해보자. 

  VM으로 각 DB당 할당할 경우 낭비가 심해짐. 



- $ docker image pull 이미지명 : 특정 이미지를 받아서 나중에 써먹을 때 사용

- $ docker rmi 이미지명 : 특정 이미지 삭제

- $ docker container ps : 구동중인 컨테이너 전부

- $ docker container ls : 현재 설치된 컨테이너

- $ docker container ls -a : stop된 애들까지 전부

- $ docker container start 컨테이너ID
  $ docker container stop 컨테이너ID
  $ docker container rm 컨테이너ID

- $ docker exec -it 컨테이너ID /bin/bash : 도커 가상 서버 들어가기

- (도커 접속후) psql -U postgres : DB(postgreSQL) 실행

  \dt : 테이블 목록

  \list

  \q : DB 빠져나가기

- exit : 도커 빠져나가기

- 서버1(클라우드에 할당받은 서버) : Hypervisor(Oracle Cloud) - VM서버(실제서버) - Docker - Container(DB; Pg17(database)) => 오라클 클라우드 환경 내에 VM서버가 존재

  `docker container ls`에서 확인해서 아래 pg17(DB)이 구동되고있는것을 확인할 수 있다. 

  서버2 : 공통서버 - Venv / Anaconda - Python App

  Python App(DB, 데이터분석, 웹 크롤링) 에서 실행한 결과들은 전부 Pg17로 들어가도록 할거임.

  위에 데이터를 DB에 넣으면 다시 꺼낼수도있어야함.
  가상서버(VM환경)에서 도커내로 카피해야하는데
  (VM환경에서) `docker cp $파일 $컨테이너ID:$컨테이너_디렉토리`
  `docker cp aaa.txt c6ccd5626a67:/tmp`

  또한 컨테이너에서 생긴것을 다시 VM으로 꺼낼 수도 있어야한다.
  (VM환경에서) `cat > bbb.txt`
  `docker cp $컨테이너ID:$컨테이너_디렉토리/파일 호스트디렉토리_파일`
  `docker cp $c6ccd5626a67:/tmp/bbb.txt /tmp`

- docker volume : 도커에서 쓰는 일종의 파일시스템, DB사용하면 외부의 Volume에 저장

  도커에서 파일이 저장되는 곳이 docker volume임. volume은 도커에서도, 도커밖에서도 접근이 됨

- docker run : docker container 생성하고 시작(docker-compose와 유사함. 우리는 docker-compose로 했음)