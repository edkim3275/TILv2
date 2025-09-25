# Day3

- 총론은 끝나고 본격적인 수업 시작

- 수업은 크게 이런 식으로 분류된다. AI를 위한 python 기초 > AI를 위한 python 응용 > 딥러닝 기초 개념

  네이밍이 조금 아쉽지만 생각보다 많은 내용을 다루게 될 것 같아서 기대가 된다 :)

- 이번주는...

  OCI에 Linux 서버를 생성해보며 개발환경 및 운영환경 구성하기

  Linux 활용해보기

  flask web app, DB 연계(PostgreSQL), LLM 연계(python으로 LLM호출)

- AI Agent 역할

  입력 => Agent --- DB => LLM

  기본적인 형태는 입력을 Agent가 받아서 LLM이 잘 해석할 수 있는 수준으로 변환하는 역할

## 클라우드

- VM(Virtual Machine)

  하드웨어(Infrastructure, Host Operating System) > Hypervisior > GuestOS

  :scroll: 현재 핸드폰의 메모리(4~8GB) 경쟁으로 인한 반도체 가격 하락 > CPU, RAM 효율 향상 자원이 늘어나게됨 > 이런 노는 자원을 잘 활용하기 위한 고민 > 물리적 컴퓨터 하나에 여러개의 가상 머신 이를 관리하는 역할 하는 Hypervisor (예. 메모리가 16GB라면 각 GuestOS에 4GB씩 나머지는 HostOS가 사용) > GuestOS

  클라우드 AWS > OCI > MS Azure > Google Cloud >> vultr, linode(가성비, 개인용) >>>>> NAVER, KT :sweat:

- 도커(Docker)

  가상 머신 사용에 따른 자원 낭비(CPU, 메모리 사용률 저하) > 효과적으로 사용하기 위한 고민 > 

  보통 GuestOS 하나에 하나를 담당(하나는 DB, 하나는 서버 등)

  도커도 GuestOS 역할을 함(기존 GuestOS와의 차이. Hypervisor대신 Docker가 들어가고 하나의 OS를 공유한다는 점에서 VM에서 GuestOS들이 HostOS의 자원을 배타적으로 공유하는 점이 차이로 존재함)

### OCI

> :bulb: 클라우드 서비스는 회사마다 방법이 조금씩 상이하고 업데이트가 이루어지면 새로운 이용 방법으로 개편되는 경우가 잦기에 개략적인 서비스 사용 방법만 이해하고 그때그때 찾아보며 익히면 된다.

- GuestOS(Instance) > compartment(작업공간) > Create Instance

- [Image] 서버 이미지를 의미

  - Ubuntu 22.04

    :bulb: SW는 제일 출시가 빠른 것이 아닌 LTS 버전을 사용하자

- [Shape] 서버의 형상(서버가 사용하는 CPU, 메모리 등)

  - OCPU 1
  - (메모리 설정의 Expand 화살표 버튼을 눌러서 설정) Amount of Memory 2GB로 설정

- [Networking] 네트워크 설정

  - VNIC 설정

  - primary network : Create new virtual cloud network

    [Primary VNIC IP addresses] 따로 설정 x

    [Add SSH keys] :star: **Advanced options 에서 Download private key** SSH키 다운

    경우에 따라서 public IP가 필요하지 않는 경우도 존재하는데(외부에서의 접근이 필요없을 경우) 그러한 설정을 VNIC 설정으로 하는 듯. 나중에 따로 다시 public을 열어주는 것도 가능함

  => 이제 서버 생성이 완료됨(여기서 만들어진 서버는 GuestOS) :raised_hands:

---

- 이제 서버에 접근해야 한다.(실습의 경우 윈도우 환경이라 XSHELL, XFTP로 진행하였고, 나는 맥이라 자체 터미널에서 진행)

- 로컬에 존재하는 ssh-key 복사 > ssh-key 파일 경로 확인 > 키 권한 설정 > SSH 접속

  - SSH 파일 경로 확인 : $ ~/oracle/oci/edu004/ssh-key.key

  - 키 권한 설정 : $ chmod 600 ~/oracle/oci/edu004/ssh-key.key

  - SSH 접속 : $ ssh -i ~/oracle/oci/edu004/ssh-key.key ubuntu@공인IP주소

    ubuntu는 사용계정명(사용자 이름), 공인IP주소는 상단의 OCI에서 생성한 서버에 부여되는 Public Address를 의미한다.

- 잠시 OCI로 돌아와서..

  - 현재는 security rule에 22포트만 열려있어서 전부 접근이 가능함

  - security

    HW1(CPU/RAM ...) --- LAN(이더넷, WiFi ...) --- 방화벽(오라클) --- HW2(CPU/RAM ...)

    |_ _ ssh(22), webserver(80, 8080), DB(5432) ...

    여기서 **HW1과 HW2를 구분하기 위한 것이 IP**(예. HW1 IP : 193.123.245.200, HW2 IP : 193.123.245.202)

    자, 이제 서버는 구분이 됐음. 근데 서버마다 올라가 있는게 다르다. 이 **서버에 올라가 있는 서비스에 접근하기 위해 포트가 필요**하게 된다. **포트는 0 ~ 65536(TCP/UDP로 구분)** 프로세스를 띄우면 각 프로세스를 포트에 연결을 시켜준다. 즉, 위의 ssh, webserver, DB를 연결해준다(포트를 할당해준다). 따라서 예를들어 ssh에 접속하려면 193.123.245.200:22, webserver는 193.123.245.200:80, DB는 193.123.245.200:5432로 접근하게 된다.

  - security rule

    위의 상황은 방화벽이 약한 상황. 여기서 오라클은 방화벽을 제공해서 외부에서의 접근을 조절할 수 있고. 이를 조절하는 것이 security rule임.

    방화벽은 클라우드에서 제공하는 방화벽도 있고 서버에서 제공하는 방화벽또한 존재하기때문에. 클라우드에서도 방화벽을 open해줘야하고, 서버에서도 방화벽을 open해줘야한다.

  - source CIDR 0.0.0.0/0 이되면 모든 IP를 허용하는 것. Destination Port Range 80, 8080, 5000 추가 => 이렇게 되면 HW1의 방화벽에서 모든 IP의 포트 20, 80, 8080, 5000을 열어주게 되는 것

​	=> 이제 OCI에서 생성한 Ubuntu 서버에 접속했다! :raised_hands:

---

- ubuntu 접속 후

  `pwd` : 현재 위치 확인

  :bulb: `sudo`를 붙이지 않고 명령어를 입력할 경우(일반 사용자가 권한이 없어서 사용할 수 없는 명령어들이 존재) Permission denied 당한다

  `sudo apt-get update` : 관리자 권한으로 최신 버전으로 업데이트

  `sudo apt-get upgrade` : OS 뿐만 아니라 설치되어있는 apt로 관리되는 모든 패키지까지 함께 업데이트

  :bulb: 업데이트 : 업데이트 가능한 패키지 목록만을 최신화(목록만 갱신), 업그레이드 : 현재 설치된 것들을  가능한 최신 버전으로 업그레이드

  `sudo apt install net-tools` : network tools 설치

  `sudo apt install nginx` : nginx(web serer) 설치

  :bulb: 웹 서버(nginx) 설치가 완료되어 서버에 접속해보면 아직 접속이 안된다. 아직 서버 방화벽이 열려있지 않은 상황이라 접근이 안되는 것!

   `ufw allow 8080/tcp` : 다른 클라우드에서의 오픈 명령어(우분투 표준)

  `sudo iptables -I INPUT 1 -p tcp --dport 80 -j ACCEPT` : 오라클 클라우드(OCI)에서의 서버 오픈 명령어.

  :bulb:OCI 클라우드 방화벽은 OCI 자체 내에서 방화벽을 오픈해줄 수 있음(80,5000,3389,5432 등 필요한 tcp port 오픈) 현재는 같은 서브넷, 방화벽을 사용중(방화벽은 열려있으니 서브넷만 오픈해주면 됨)

  ? 같은 서브넷에 있다는 것은 public IP로 접속하게되면 오라클 자체 privite IP로 전환(네트워크 translate)되는데 보면 10.0.0.x라는 서브넷을 생성해서 여러 서버들을 넣어놓은 것. 10.0.0이 공통이 되면 같은 서브넷 안에 존재하게 됨 => 10.0.0.x 서브넷을 생성해서 edu01, edu02 .. 인스턴스(서버, GuestOS)들을 넣어 둔 상황

  이후 다시 접속해보면 잘 접속되는것을 확인 할 수 있다.

- 도커설치(순서중요!)

  `apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common` sudo를 안붙여서 안됨

  :bulb: `sudo su -` 현재 내 경로를 루트권한을 수퍼로 바꾼다는 것, 루트권한으로 바꾸면 sudo 붙일 필요없게됨. 이렇게 하고 pwd 해보면 루트권한 확인 가능

  `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -` : 특정 url로 접근해서 필요한 키 패키지 다운

  `add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"` : apt 패키지안에 필요한 것들을 넣겠다.

  `apt-get update`

  `apt-get install docker-ce docker-ce-cli containerd.io docker-compose`

  `systemctl status docker` : 리눅스 서버 각각 프로세서 확인(도커 설치했으니 동작을 잘 하고있는지 status를 보여달라는 명령어) => active 되는 것 확인이 가능 함

  `iptables -L` : 현재 방화벽 상태확인 가능

  `nano docker-compose.yml`

  ```yaml
  version: '3.4'
    services:
    db_singlepoint:
    	image: pgvector/pgvector:pg17
    	container_name: "pg-postgres"
      environment:
        -"POSTGRES_PASSWORD=dbtest1212"
        - "TZ=Asia/Seoul"
      volumes:
        -"postgres_data:/var/lib/postgresql/data"
        - "./init:/docker-entrypoint-initdb.d"
    restart: unless-stopped
    network_mode: "host"
  volumes:
  	postgres_data:
  ```

  `docker-compose up –d` : docker-compose.yml 파일에 정의된 컨테이너들을 백그라운드에서 실행

  docker exec -it c6ccd5626a67 /bin/bash

  psql -U postgres

  CREATE USER test01 WITH PASSWORD 'test1234' SUPERUSER; => 유저(test01) 만든 것

  create database testdb; => DB(testdb) 만든 것

  grant all privileges on database testdb to test01; => 권한 부여

  CREATE EXTENSION IF NOT EXISTS vector; => 확장 설치

  `\dt` : 현재 접속한 DB 테이블 목록만 보여줌

  `\l` : 데이터베이스 목록확인. 여기서 testdb 확인 가능

  `\c testdb` : testdb로 접속 connect testdb 라는 의미

  `\dt` : 현재 DB에서 테이블 확인

- 두 번째 서버(일반사용자로 작업할 용도)

  원격 서버 접근 방법 두 가지

  OCI는 보안이 필요해서 ssh키가 필요 ` ssh -i ~/Documents/Oracle/교육자료/oci/edu004/ssh-key-2025-09-23.key ubuntu@130.162.146.139`

  두 번째 서버는 보안이 느슨해서 ssh키 필요 없고, 자신의 계정으로 들어가면 됨 => `ssh edu004@158.247.210.130` => 계정 비밀번호 입력

  `who` : 현재 접속해있는 계정들 확인 가능

  원격 데스크톱 연결

  python 가상환경 설치(venv 기반)

  `apt install python3-venv`

  `apt install python3-pip`

  `python3 –m venv myvenv` : python 가상환경

  python 가상환경 설치(Anaconda 기반)

  `sudo apt install curl –y`

  `curl --output anaconda.sh https://repo.anaconda.com/archive/Anaconda3-2024.10-1-Linux-x86_64.sh`

  `bash anaconda.sh`

  :bulb: Anaconda 과학기술 등 개발시 받는 패키지(다른 패키지 추가 필요 x), venv는 필요한 패키지를 하나하나 추가. Anaconda는 파이썬 버전별로 지정 가능

  가성환경 설치 됐다면 확인해보기

  `source myvenv/bin/activate` : 가상환경에 들어옴 venv를 활성화
  사용하는 Python / pip 등이 시스템 전체 python 이 아닌 myvenv 폴더 안의 격리된 가상환경을 사용한다는 뜻(예를들어 python은 가상환경 안의 파이썬을 실행, pip install은 가상환경 안에만 패키지가 설치)

  다시 나오려면 `deactivate`

  아나콘다 설치 => 맥에서는 따로 anaconda prompt가 존재하지않고 맥 터미널에서 진행하면 된다.

  `conda create -n 가상환경이름 python=파이썬버전`

  `conda create -n myvenv python=3.10`

  => `conda activate myvenv` 이후에 작업하는 것은 다른 환경에 영향을 주지 않는다.

## Linux

- `uname -a` : 현재 호스트 이름

- 운영체제(Operating System)

  - `control + p` 만 누르면 프린트가 작동하는 그 과정을 OS가 해주는 것
  - OS없던 시절. 동시에 한 명만 사용가능했음
  - Linux가 나오면서 컴퓨터 한대에 여러명 사용(Time sharing. 각자 0.02s씩 사용하는 개념)가능하게 됨

- 현존하는 대부분의 OS들은 [UNIX 계보](https://en.wikipedia.org/wiki/Unix-like#/media/File:Unix_history-simple.svg)를 따라가다보면 결국 UNIX, LINUX(UNIX 본떠서 만든 시스템)가 나오게 됨 => UNIX, LINUX 쓸 줄 알면 대부분의 명령어 사용가능하다는 것

- Kernel : HW자원 관리

  Shell : `ls` 등 과 같이 로그인해서 하는 명령들을 수행하는 것이 Shell (예. 윈도우 명령 프롬프트)

- :star: 유닉스/리눅스 중요 개념

  파일, 디렉토리, 프로세스, 파일시스템, 호스트, ip주소, TCP/UDP, 가상 서버, docker, 패키지, 실행파일, 읽기전용파일, user/group, device, library

  디렉토리가 폴더다가 아니라 실행권한, owner, 생성정보 등에 대한 이해 필요

  호스트 ip주소와 1대1 대응이 아님. 호스트는 하나인데 랜카드를 4개를 꽂으면 호스트는 IP주소가 4개가 되는 것.

  TCP/UDP

  가상 서버(Virtual Machine) 클라우드에서 만든것은 가상서버(GuestOS), 도커에서 만든 가상 서버는 컨테이너(자원을 아끼기위해서 필요한 것만 넣어서 서버치고 부실함)

### 리눅스 명령어

- `top` : 실행시 나오는 Mem(memory 용량), swap: 잘 사용안하는 부분은 CPU로 내렸다가 다시 사용시 mem으로 올리려는 용도

- `man $명령어`

  `man ls` : 확인

  `uname -a` : 호스트명

  `lsb_release -a` : 현재 우분투 버전 확인

  :star: `reboot` : 리부트하기(조심)

  :star: `shutdown` : 컴퓨터 끄기 => `shutdown -h now`처럼 사용함

  `dmesg` 또는 `cat /var/log/messages` : 서버들 로그 확인하는 것

  :bulb: 운영체제는 기본적으로 root를 사용하거나 sudo를 붙여서 사용하자

  :star: `df` : => 디스크 사용량, 남은량 등 정보(단위 KB) => `df -k`로 사용함

  아래의 `/dev/sda1`이 root 파일 시스템

  mount`  : 윈도우로 따지면 [디스크 관리]가 있는데 윈도우는 C, D 드라이브에 마운트하지만 리눅스는 mount명이 따로 있음

  `systemctl` : systemcontrol로 시작하는 것. 이것 저것 시작하고 종료하는 명령어 => `systemctl start nginx` / `systemctl stop nginx` / `systemctl status nginx`

  `service` :  `systemctl`과 비슷함 => `service nginx stop`과 같이 사용

  `adduser user_id` : 계정 추가(sudo) => `ls home`으로 유저 확인

  `deluser user_id` : 계정 삭제. => `ls home`해도 기록은 남는다. 삭제하려면 명시적으로 삭제해줘야 함 `rm -rf user_id`(이런 명령어는 함부로 쓰면 위험)

  :star: ​`ssh` : 원격 명령 => `ssh -p 10022 test001@129.154.209.167` 해당 IP 포트로 접근(port 22이면 -p 옵션 불필요) => 여기서 나가려면 `exit` 혹은 `control + d`

  `who | grep edu000` : 누구 있는지 확인

  :star: ​`ssh -i ~/.ssh/oci-ssh-key.key test001@129.154.209.167` (**key 파일의 permission은 반드시 400으로 변경(600도 가능) => `chmod 400 ssh파일이름.key`**)

  `ls -al` 모든 파일들에 대한 권한 확인

  위에서 파일 permission을 400, 600 등으로 변경해주는 것이 아래의 drwx와 같이 파일에 대한 권한을 설정해주게 되는 것

- `who` : 현재 접속해 있는 계정 확인

- :star:`scp` : 서버간 파일복사

  `scp -P 10022 textfile.txt test000@129.154.209.167:/tmp` S
  SSH 접속 포트를 10022 포트로 지정해서 textfile.txt 파일을 서버의 경로로 복사

  `scp -i ~/.ssh/oci-ssh-key.key textfile.txt test000@129.154.209.167:/tmp` : 
  ssh-key를 이용해 textfile.txt 파일을 서버의 경로로 복사
  -i 를 통해 어떤 개인키 파일을 인증에 사용할지 지정, 

  `scp 원격서버user_id@원격서버IP:ssh-key경로 .`  : sshkey경로를 해당 원격서버의 `.`경로로 복사

  :bulb: Ubuntu에서 명령어 순서는 from > to

  서버접속에 키가 따로 필요하지 않은 경우는 `scp 보낼파일 계정@보낼서버:/폴더` 와 같은 식으로 복사한다.

  `scp ssh-key.key 계정@서버IP:/폴더`

  `ls -l *.key`  :  `.key`파일 전부 확인

- 패키지 설치(root 관리자만 가능한 명령)

  `apt update`, `apt install package-name`, 

  `apt remove package-name` : 프로그램만 삭제

  `apt purge package-name` : 프로그램의 관련 파일까지 전부 삭제

  `apt --only upgrade install package-name`

  `sudo apt list` : sudo apt로 가능한 명령어 확인

  `rpm -ivh 설치파일.rpm` : apt를 사용할 경우 지정된 레포에 있는 패키지를 다운받는 의미임. 다만 개인적으로 따로 레포지토리가 있다면 rpm을 설치를 한다. 해당 파일은 rpm으로 패킹되어 올라가있을 것이고 해당 rpm을 통해 설치하게 된다.

- 네트워크 관리 명령(root 관리자만 가능한 명령)

  `ping ip주소` : 연결확인(클라우드에서는 해커들 공격 때문에 ping 막아두기도 함. Oracle은 ping 막혀있음 - 클라우드 일정 이상 가격나가는 곳을 써야하는 이유.)

  `telnet ip주소 접속port` : 원격 접속 명령임. telnet은 보안이 취약해서 잘 사용은 안하나, ping이나 보안이 뚫렸는지 확인하는 용도로 사용되곤 함

  `telnet localhost 5432` 내 서버의 5432 포트가 열려있는지 확인가능

  `ifconfig` : 서버 인터페이스 알려주는 명령어(interface configuration) => `ifconfig -a`로 보통 사용. 서버내 네트워크

  `ip a` : 서버에서 사용하고있는 모든 ip 관련 정보
  lo는 localhost
  enp0s5는 이더넷 인터페이스

- `netstat`

  `sudo netstat -lptu`

  `sudo netstat -nlpt` 

  앞에는 접속 허용 IP, 뒤에는 허용하는 포트

  0.0.0.0은 어디에서 들어와도 허용한다는 의미

  PID : 프로세스 ID

- `curl` : connect-url. 원격지 서버에있는 응답을 출력 => `curl 원격지URL``

- ``wget` : 다운로드 유틸리티.

- `ufw` : 우분투 방화벽 => `ufw allow 8080/tcp`

  `ufw status` : 허용된 포트들 전부 확인가능

  ` : OCI iptables allow list

- Iptables : 우분투 방화벽(단, OCI에서만 적용)`iptables -l INPUT 1 -p tcp --dport 8080 -j ACCEPT`

- 시간

  :star: `sudo timedatectl set-timezone Asia/Seoul`또는 `sudo ln –s /usr/share/zoneinfo/Asia/Seoul /etc/localtime` : 타임셋 변경

  `date` : 현재 시간 확인